# SPEC — Workflows n8n (Linha Viva / Carol) · node a node

> **Host:** `n8n.bilinski.cloud` · **Projeto:** `uV4O9lYLRnNVj5y4` · **Workflow base existente:** `LUdXBsryBbm8O7Aa`
> **Padrão de engenharia:** idempotência (Redis `SET NX EX`), verificação de assinatura, workflow de erro global, lib de config central. Referência validada: `SurfRio/sdr-ia`.
> **Integração Retell:** HTTP Request (não community node), para não depender de instalar `@retellai/n8n-nodes-retellai` no host antes da demo.

## Visão geral e topologia

```
[Lead entra]                                  [Retell dispara eventos]
     |                                                  |
     v                                                  v
WF-B voz-sdr-disparo  --create-phone-call-->   WF-C voz-sdr-webhook
     |  (usa)                                           | (call_analyzed)
     v                                                  v
WF-A voz-lib-config <----(usado por B e D)----  WF-D voz-sdr-pos-chamada
                                                        |
                                          [CRM + agenda + notifica + retry + opt-out]

WF-E voz-sdr-on-error  <--- errorWorkflow de A, B, C, D
```

## Convenções

- **Credenciais** (do cofre n8n, ver `agente/SETUP-CREDENCIAIS.md`): `Retell` (HTTP Header Auth), `Cal.com`, CRM.
- **Erro**: em Settings de cada workflow, `Error Workflow = voz-sdr-on-error`.
- **Timezone**: `America/Sao_Paulo`.
- **Chaves Redis**: `voz:dial:{phone}` (lock de discagem), `voz:evt:{event}:{call_id}` (idempotência de evento), `voz:optout:{phone}` (suppression).
- **Segredo em Code node**: `$env.RETELL_API_KEY` (nunca hardcoded).

---

## WF-A · `voz-lib-config`

Constantes centrais. Chamado por WF-B e WF-D via Execute Workflow.

| # | Nó | Tipo | Config |
|---|---|---|---|
| 1 | `Start` | `n8n-nodes-base.executeWorkflowTrigger` | `inputSource: passthrough` |
| 2 | `Config` | `n8n-nodes-base.code` | retorna o objeto abaixo |

**Code `Config`:**
```js
return [{ json: {
  from_number: "+14706164413",
  retell_agent_id: "agent_a32aac07fe9301318d015935d4",
  retell_api_base: "https://api.retellai.com/v2",
  janela: { tz: "America/Sao_Paulo", seg_sex: [9, 20], sab: [10, 13], domingo: null, feriados: false },
  // Janela por recomendacao do parecer legal (Anatel): seg a sex 9h-20h, sab 10h-13h, nunca domingo/feriado.
  retry: { max: 2, backoff_min: [30, 120] },
  owners: ["especialista_1"],            // round-robin de closers
  webhook_events_url: "https://n8n.bilinski.cloud/webhook/voz-retell-events"
} }];
```

Conexões: `Start → Config`.

---

## WF-B · `voz-sdr-disparo` (lead → ligação)

| # | Nó | Tipo | Config-chave |
|---|---|---|---|
| 1 | `Webhook Lead` | `n8n-nodes-base.webhook` | POST, path `voz-sdr-lead`, `responseMode: responseNode` |
| 2 | `Config` | `n8n-nodes-base.executeWorkflow` | chama `voz-lib-config` |
| 3 | `Normalizar E164` | `n8n-nodes-base.code` | ver código abaixo |
| 4 | `Telefone valido?` | `n8n-nodes-base.if` | `{{$json.telefone_valido}} === true` |
| 5 | `Checar opt-out` | `n8n-nodes-base.redis` | operation GET, key `voz:optout:{{$json.to_number}}` |
| 6 | `Opted-out?` | `n8n-nodes-base.if` | valor existe → ramo "parar" (log Anatel) |
| 7 | `Dentro da janela?` | `n8n-nodes-base.code` + `if` | compara hora atual America/Sao_Paulo com `janela` |
| 8 | `Lock discagem` | `n8n-nodes-base.redis` | SET key `voz:dial:{{$json.to_number}}` value `1`, `NX`, TTL 3600 |
| 9 | `Lock obtido?` | `n8n-nodes-base.if` | se não setou → parar (evita discar 2x) |
| 10 | `Retell create-phone-call` | `n8n-nodes-base.httpRequest` | POST, cred `Retell`, body abaixo, `retryOnFail: true` |
| 11 | `Persistir call` | `n8n-nodes-base.supabase` ou `googleSheets` | grava `call_id` + status `dialing` |
| 12 | `Responder 200` | `n8n-nodes-base.respondToWebhook` | `{ ok: true, call_id: {{$json.call_id}} }` |

**Code `Normalizar E164`:**
```js
let d = String($json.telefone || '').replace(/\D/g, '');
if (d.startsWith('00')) d = d.slice(2);
if (d.startsWith('55')) d = d.slice(2);          // remove DDI duplicado
if (d.length === 11 || d.length === 10) {         // DDD + numero
  const ddd = d.slice(0, 2);
  let n = d.slice(2);
  if (n.length === 8 && /^[6-9]/.test(n)) n = '9' + n;   // 9o digito celular
  d = ddd + n;
}
const e164 = '+55' + d;
const telefone_valido = /^\+55\d{2}9?\d{8}$/.test(e164);
return [{ json: { ...$json, to_number: e164, telefone_valido } }];
```

**HTTP Request `Retell create-phone-call`** — `POST {{$node["Config"].json.retell_api_base}}/create-phone-call`, Authentication: `Retell` (Header Auth), Body (JSON):
```json
{
  "from_number": "{{ $node[\"Config\"].json.from_number }}",
  "to_number": "{{ $json.to_number }}",
  "override_agent_id": "{{ $node[\"Config\"].json.retell_agent_id }}",
  "retell_llm_dynamic_variables": {
    "name": "{{ $json.nome }}",
    "origem_lead": "{{ $json.origem }}",
    "contexto": "{{ $json.contexto }}"
  },
  "metadata": {
    "lead_id": "{{ $json.lead_id }}",
    "n8n_execution_id": "{{ $execution.id }}"
  }
}
```
> **Demo em Web Call:** trocar o endpoint por `/create-web-call` e o body por `{ "agent_id": ..., "retell_llm_dynamic_variables": {...}, "metadata": {...} }`; a resposta traz `access_token` para o widget.

Conexões: `Webhook Lead → Config → Normalizar E164 → Telefone valido?` (true) `→ Checar opt-out → Opted-out?` (false) `→ Dentro da janela?` (true) `→ Lock discagem → Lock obtido?` (true) `→ Retell create-phone-call → Persistir call → Responder 200`. Ramos "false"/"parar" vão para `NoOp` de log.

---

## WF-C · `voz-sdr-webhook` (eventos Retell)

> A URL pública deste webhook (`https://n8n.bilinski.cloud/webhook/voz-retell-events`) vai no campo **"URL do webhook de nível de agente"** da Retell.

| # | Nó | Tipo | Config-chave |
|---|---|---|---|
| 1 | `Webhook Retell` | `n8n-nodes-base.webhook` | POST, path `voz-retell-events`, **Raw Body = on**, responder 200 imediato |
| 2 | `Verificar assinatura` | `n8n-nodes-base.code` | HMAC do raw body vs `x-retell-signature` |
| 3 | `Idempotencia evento` | `n8n-nodes-base.redis` | SET `voz:evt:{{event}}:{{call_id}}` `1` `NX` TTL 86400 |
| 4 | `Ja processado?` | `n8n-nodes-base.if` | se não setou (já existe) → parar |
| 5 | `Switch evento` | `n8n-nodes-base.switch` | por `{{$json.body.event}}` |
| 6 | `Pos-chamada` | `n8n-nodes-base.executeWorkflow` | só no ramo `call_analyzed` → chama `voz-sdr-pos-chamada` |

**Code `Verificar assinatura`:**
```js
const crypto = require('crypto');
const raw = $json.rawBody ?? JSON.stringify($json.body);
const sig = ($json.headers || {})['x-retell-signature'];
const expected = crypto.createHmac('sha256', $env.RETELL_API_KEY).update(raw).digest('hex');
if (!sig || sig !== expected) throw new Error('assinatura Retell invalida');
return $input.all();
```

**Switch `Switch evento`** (`{{$json.body.event}}`): `call_started` → log; `call_ended` → log; `call_analyzed` → `Pos-chamada`.
> Só `call_analyzed` aciona a automação: os 3 eventos disparam, e é o único que traz `call.call_analysis.custom_analysis_data`. A idempotência por `event+call_id` é o cinto de segurança contra disparo triplo.

Conexões: `Webhook Retell → Verificar assinatura → Idempotencia evento → Ja processado?` (false/novo) `→ Switch evento → (call_analyzed) Pos-chamada`.

---

## WF-D · `voz-sdr-pos-chamada` (roteamento por resultado)

| # | Nó | Tipo | Config-chave |
|---|---|---|---|
| 1 | `Start` | `n8n-nodes-base.executeWorkflowTrigger` | passthrough (recebe `call`) |
| 2 | `Extrair analise` | `n8n-nodes-base.code` | pega `call.call_analysis.custom_analysis_data` |
| 3 | `Switch proximo_passo` | `n8n-nodes-base.switch` | 5 saídas |
| 4a | `CRM upsert` | `n8n-nodes-base.httpRequest` (CRM) | cria/atualiza lead qualificado, tag |
| 4b | `Agendar evento` | `Cal.com` / `googleCalendar` | se `data_hora_agendada` presente |
| 4c | `Notificar closer` | `httpRequest` / Slack / WhatsApp | resumo + `recording_url` |
| 5a | `Nurture` | tags + sequência | por `objecao_principal` |
| 5b | `Retry` | `redis` + `scheduleTrigger` | backoff, max 2, respeita horário |
| 6 | `Opt-out suppression` | `n8n-nodes-base.redis` | SET `voz:optout:{{phone}}` |
| 7 | `Log` | `supabase` / `googleSheets` | `phone_hash` (não o telefone cru) |

**Code `Extrair analise`:**
```js
const call = $json.body?.call ?? $json.call ?? $json;
const cad = call.call_analysis?.custom_analysis_data ?? {};
return [{ json: {
  call_id: call.call_id,
  recording_url: call.recording_url,
  transcript: call.transcript,
  proximo_passo: cad.proximo_passo || (cad.opt_out ? 'remover_lista' : (cad.agendou_reuniao ? 'agendar' : 'nurture')),
  ...cad
} }];
```

**Switch `Switch proximo_passo`** (`{{$json.proximo_passo}}`): `agendar` → `CRM upsert → Agendar evento → Notificar closer`; `follow_up`/`nurture` → `Nurture`; `remover_lista` → `Opt-out suppression`; `transferir_humano` → `Notificar closer` (urgente). Todos → `Log`.

Conexões: `Start → Extrair analise → Switch proximo_passo → (ramos) → Log`.

---

## WF-E · `voz-sdr-on-error`

| # | Nó | Tipo | Config |
|---|---|---|---|
| 1 | `On Error` | `n8n-nodes-base.errorTrigger` | dispara em falha de A-D |
| 2 | `Montar alerta` | `n8n-nodes-base.code` | `{workflow, node, erro, call_id, ts}` |
| 3 | `Alertar ops` | `httpRequest` / Slack / WhatsApp | mensagem de alerta |

Falha nunca silenciosa. Setar `Error Workflow = voz-sdr-on-error` no Settings de WF-A a WF-D.

---

## Agendamento — 2 rotas

- **Rota A (recomendada, o "uau" na demo):** funções nativas Cal.com no agente Retell (`verificar disponibilidade` + `reservar`). A Carol agenda ao vivo na call; o `call_analyzed` já traz `data_hora_agendada`; o n8n só confirma/notifica.
- **Rota B (fallback):** a Carol captura a preferência de horário; o `WF-D` cria o evento (Google Calendar/Cal.com) no pós-chamada.
Recomendo A com B de backup.

## Configuração da Retell (ligada aos workflows)

1. Campo "URL do webhook de nível de agente" = `https://n8n.bilinski.cloud/webhook/voz-retell-events`.
2. Eventos do webhook: `call_analyzed` (obrigatório) + `call_started`/`call_ended` (log).
3. Extração pós-chamada: adicionar os campos de `agente/custom-analysis-schema.json`.

## Checklist de ativação (na ordem)

- [ ] `/mcp` autenticado + credenciais no cofre
- [ ] WF-A criado
- [ ] WF-C criado e a URL colada na Retell (testar com "Teste" na Retell)
- [ ] WF-D criado
- [ ] WF-B criado
- [ ] WF-E criado + setado como Error Workflow em A-D
- [ ] Teste E2E: 1 lead → ligação → `call_analyzed` → CRM + agenda + notificação (1 disparo só)
- [ ] Ativar workflows (a ativação é feita na UI do n8n; a API/MCP pode não expor "activate")
