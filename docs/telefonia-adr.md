# Decisão de Telefonia — Linha Viva / Carol (chamada real para +55, número BR nativo)

> **Data:** 2026-07-22 · **Objetivo:** trunk SIP BRASILEIRO com número BR real (caller ID nativo)
> que permita a Carol (Retell AI) ligar para celular BR (+55), disparada pelo gatilho real do n8n.
> **Fonte:** 2 pesquisas de engenharia VoIP + docs oficiais Retell + caso real documentado.
> **Decisão do operador:** provedor BRASILEIRO com número real BR (descartado Telnyx/US).

> 🔴 **STATUS (2026-07-22) — ETAPA DE IMPLEMENTAÇÃO PÓS-VENDA:** a vinculação do número BR real é
> **crucial**, mas **NÃO é impeditiva** para a demo/venda de 23/07. A demo roda 100% via **web call**
> (Carol por voz no navegador, já funcional). Esta telefonia é o **primeiro entregável de
> implementação do sistema completo APÓS o fechamento** — go-live prometido em 10 dias úteis (ver
> `OFERTA.md`). Provedor definido: **Nvoip** (conta já criada; falta DID + trunk). Arquitetura:
> tentar Rota 1 (direta) e, se o INVITE não chegar, Rota 2 (SBC jambonz) como fallback garantido.
> **Pré-requisito técnico já 100% pronto do nosso lado** (Retell + n8n verificados); só falta o trunk.

---

## A descoberta que define a arquitetura (leia primeiro)

O gargalo **não é achar DID BR**, é o provedor BR **aceitar o INVITE que vem da Retell**:
- A Retell é hospedada nos EUA e envia o INVITE do **SBC dela** (IPs `18.98.16.120/30` e
  `3.42.144.0/23`), autenticado por **usuário/senha (digest)**, **sem fazer SIP REGISTER**.
- Quase todo provedor BR foi desenhado para o **modelo oposto**: o cliente REGISTRA de um IP fixo,
  ou o provedor libera por IP fixo. Auth de INVITE credencial vindo de IP estrangeiro sem registro é
  **config atípica** no BR.
- **Evidência real (não teórica):** há um caso público **Fale Vono + Retell que FALHOU** — o
  provedor confirmou que **nunca recebeu o INVITE** da Retell (`18.98.16.121`), firewall descartando
  IP estrangeiro. Thread sem resolução. → **Fale Vono reprovado.**

**Regra Anatel (Origem Verificada / STIR-SHAKEN, obrigatória desde abr/2025):** chamada de saída em
massa com DID BR precisa da atestação do **operador dono do DID**. Isso **favorece** usar operadora
BR nativa (ela atesta o próprio número) e **proíbe** falsear bina (multa até R$50mi + bloqueio do
número por 15 dias). Nunca usar "bina aleatória" de atacado.

---

## Duas rotas (escolher a arquitetura)

### Rota 1 — Conexão direta (Retell → provedor BR)
Contratar um provedor BR que comprovadamente aceite o INVITE credencial da Retell (ou whitelist dos
IPs dela). **Menos peças**, mas depende do suporte do provedor cooperar com config atípica.
- **Provedor nº 1: Nvoip** — único BR que documenta os DOIS modos que a Retell precisa: auth por
  login/senha "sem depender de IP fixo" **E** autorização/whitelist por IP. Cultura API-first
  (OAuth, docs de dev), self-service com CNPJ, DID fixo/móvel.
- **Contingência: DirectCall** — operadora consolidada, mais barata (móvel R$0,186/min; ilimitado
  desde R$60,16/mês), DID em ~3 dias úteis, mas doc oficial é **registro-based** (host fixo) — exige
  confirmar whitelist dos IPs Retell antes de pagar.
- **Como não contratar no escuro:** rodar o **script de 5 perguntas** (abaixo) ANTES de pagar.

### Rota 2 — SBC próprio no meio (a DEFINITIVA)
Subir um SBC leve (**jambonz** — tem integração oficial documentada com a Retell, repo
`jambonz/retell-sip-integration-example`) numa **VPS com IP estático**.
- Do lado da **Retell**: o SBC expõe um trunk IP/credencial que a Retell aceita nativamente.
- Do lado do **provedor BR**: o SBC faz **REGISTER** / é liberado por **IP fixo** — o modelo nativo
  de QUALQUER operadora BR (DirectCall, Nvoip, Vozio, Telein...).
- **Elimina o requisito (a) por completo** — a Retell nunca fala direto com o provedor BR. Converte
  qualquer trunk BR registro-based em compatível.
- Custo: 1 VPS + ~1-2h de setup (trabalho de @devops). Ainda precisa de um DID BR de um provedor
  (Nvoip/DirectCall) para a terminação e o caller ID.

> **Recomendação:** contratar o **Nvoip** (necessário nas duas rotas) e, dado o risco documentado +
> preferência por solução definitiva, ir pela **Rota 2 (SBC jambonz)** como arquitetura de produção,
> usando a Rota 1 (direta) só se o Nvoip confirmar compatibilidade no script de qualificação (atalho).

---

## Tabela comparativa (provedores BR)

| Provedor | DID BR | Trunk terminação | Auth credencial vs só-IP | Ativação (CNPJ) | Custo | Compat. Retell direta |
|---|---|---|---|---|---|---|
| **Nvoip** | ✅ fixo/móvel | ✅ trunk SIP | ✅ doc: login/senha **sem IP fixo** + whitelist IP | ✅ self-service | ⚠️ min. móvel n/c | ⚠️ maior chance (confirmar) |
| **DirectCall** | ✅ 67 DDDs | ✅ UDP/TCP | ⚠️ registro-based (host fixo) | ✅ self-service, DID ~3d | ✅ móvel R$0,186/min | ⚠️ confirmar whitelist |
| **Vozio** | ✅ | ✅ ilimitado | ⚠️ não disclosed | ⚠️ comercial | ✅ R$99/mês ilim. | ⚠️ n/c |
| **Fale Vono** | ✅ | ✅ | ⚠️ | ✅ | ✅ | ❌ **falha documentada c/ Retell** |
| **Zenvia/TotalVoice** | ❌ voz +55 encerrada | ❌ | — | — | — | ❌ fora |
| Callix / Sipfy / Voipex | n/c | n/c | n/c | n/c | n/c | ❌ sem dado público |

Qualquer provedor da tabela funciona na **Rota 2** (SBC próprio), pois lá o modelo é registro/IP fixo.

---

## Script de 5 perguntas para o suporte do provedor (mandar ANTES de pagar)

1. "Vou terminar chamadas de saída a partir de um **SBC nos EUA (Retell AI)** que **não tem IP fixo
   único** e **não faz SIP REGISTER**, só envia INVITE autenticado por **usuário/senha (digest)**.
   Vocês aceitam esse INVITE **sem registro prévio**? Sim/Não."
2. "Se depender de IP, vocês fazem **whitelist de IPs de origem estrangeiros**? Consigo liberar
   `18.98.16.120/30` e `3.42.144.0/23`? Sim/Não."
3. "O trunk de saída aceita usar **meu DID de vocês como bina/caller ID** para **celular BR**? Sim/Não."
4. "Qual o **host/porta de terminação** (termination URI) e qual **transporte** (TCP/UDP/TLS) p/ SBC externo?"
5. "Têm cliente usando o trunk com **Retell/Vapi/Twilio Elastic SIP/BYOC externo**? E como fica a
   **Origem Verificada (STIR-SHAKEN)** para saída em volume com esse DID?"

> Se travar nas perguntas 1 e 2 → o provedor não serve para conexão direta → vai para a **Rota 2**.

---

## Passo a passo — contratar Nvoip (base das duas rotas)

1. Rodar o script de 5 perguntas (acima) no suporte comercial/técnico. Avançar só se 1 e 2 forem "sim"
   (Rota 1); se não, seguir mesmo assim para a Rota 2 (SBC próprio).
2. Criar conta Nvoip com **CNPJ SEAL DIGITAL** (39.801.819/0001-93). Confirmar objeto/telefonia.
3. Contratar **1 DID BR** (fixo do DDD-alvo; móvel se houver) + crédito/plano de minutos móvel.
4. Coletar: **host de terminação** (termination_uri), **usuário SIP**, **senha SIP**, **DID em E.164**
   (`+55DD9XXXXXXXX`), porta + transporte (pedir **TCP**).
5. Entregar tudo **pelo cofre** (segredos nunca no chat). @devops recebe.

## O que EU faço (Orion → @devops) quando os dados chegarem

**Rota 1 (direta):** `POST https://api.retellai.com/import-phone-number`
```json
{
  "phone_number": "+55DD9XXXXXXXX",
  "termination_uri": "<host de terminacao Nvoip>",
  "sip_trunk_auth_username": "<usuario SIP>",
  "sip_trunk_auth_password": "<senha SIP>",
  "transport": "TCP",
  "nickname": "Trunk Nvoip BR - Carol",
  "outbound_agents": [{ "agent_id": "agent_a32aac07fe9301318d015935d4", "weight": 1.0 }],
  "allowed_outbound_country_list": ["BR"]
}
```
→ smoke test pelo dashboard Retell para o celular do Luiz.

**Rota 2 (SBC jambonz):** subir jambonz na VPS (IP estático) → configurar carrier BR (REGISTER/IP)
→ criar SIP trunk interno que a Retell importa apontando para o IP do jambonz + credencial → smoke
test. (Detalhamento de setup vira uma story própria se escolhida.)

**Ajuste final no n8n** (`voz-sdr-disparo`, `XcsZLra8ZpoQpUCp`): trocar
`const from_number = "+14706164413"` → o DID BR, no nó **Normalizar E164**. (Resto já pronto:
credencial Retell `ZdKW9le7weHFunFr` plugada, body correto, janela de discagem ok.)

---

## Achados do painel Nvoip (levantamento 2026-07-22) — para a execução pós-venda

Fonte: provisioner oficial Nvoip (`github.com/Nvoip/nvoip-pabx-provisioner`) + KB oficial 3CX.

**Dados para importar na Retell (quando executar):**
- `termination_uri` = `sip.nvoip.com.br` (provisioner) **ou** `app.nvoip.com.br` (KB 3CX) — **hosts
  oficiais divergentes, confirmar no suporte** · porta `5060`
- `sip_trunk_auth_username` = **Usuário SIP** (Configurações › Usuários; 8 dígitos, termina em `001`)
- `sip_trunk_auth_password` = **Senha SIP** (não é o login do painel; ver como admin)
- `phone_number` = DID contratado em E.164 (`+55DD...`)
- `transport` = **TCP** (Retell recomenda; Nvoip suporta TCP/UDP; TLS não confirmado)
- **Formato do número DISCADO** (dial plan Nvoip): **DDD sem zero + número, SEM `+` e SEM `55`**
  (ex.: `11987654321`). Fora disso → erro 404. A Retell disca em E.164 com `+`, então precisa de
  **regra de tradução** (remover `+55`).

**🔴 RISCO #1 confirmado:** o modelo Nvoip é **autenticação por REGISTRO** ("com base no registro").
A Retell **não registra** (só INVITE digest de IPs rotativos). Se a Nvoip exigir registro ativo para
liberar saída, a **Rota 1 (direta) não fecha** → vai para **Rota 2 (SBC jambonz/Asterisk que registra
na Nvoip e recebe os INVITEs da Retell)**. Confirmar no suporte antes: aceita INVITE digest sem
REGISTER de IP externo? Faz whitelist dos CIDRs `18.98.16.120/30` e `3.42.144.0/23`?

**Contratação (self-service):** Número Virtual › escolher DDD → ativação < 3 min, sem documentos
(portabilidade exige docs, 5-10 dias). Comprar créditos (pré-pago por minuto). DID fixo R$29-39/mês
+ ativação = 1 mensalidade (anual isenta). DID móvel = não confirmado. Tarifa celular BR exata não
publicada (ref. mercado ~R$0,24-0,28/min).

**Suporte Nvoip:** 0800 878 3122 · (11) 4118-6267 · `suporte.nvoip.com.br/portal/pt/newticket`.

---

## Riscos e armadilhas

1. **Firewall descarta INVITE de IP gringo** (risco nº 1, matou o caso Fale Vono). Mitigação:
   whitelist dos ranges Retell OU Rota 2 (SBC próprio).
2. **Exigência de SIP REGISTER** — a Retell não registra. Provedores que só autorizam "contact"
   registrado barram. Mitigação: modo credencial-sem-registro/whitelist, ou Rota 2.
3. **Origem Verificada (Anatel)** — usar operadora BR nativa que atesta o DID; nunca falsear bina.
4. **Anti-fraude / discagem em massa** — operadoras BR suspendem trunk com ASR/ACD baixo + volume
   alto. Mitigação: aquecer volume, respeitar janela, opt-out, começar com 1 DID.
5. **Formato do número no RURI/To** — alguns provedores BR esperam sem `+`, com `55`, ou só DDD+num.
   Se o INVITE chega e volta 404/484, é dial plan — ajustar formato.
6. **Termination URI: preferir hostname** (não IP cru) + porta/transporte corretos; testar TCP antes.
7. **Latência internacional** — Retell(US)→BR adiciona hops; medir na chamada. Rota 2 com VPS no BR
   pode concentrar o único hop internacional em Retell→SBC.

---

## Checklist de destravamento (chamada real)

- [ ] Script de 5 perguntas rodado no suporte do provedor (decide Rota 1 vs 2)
- [ ] Conta Nvoip criada (CNPJ SEAL) + DID BR + crédito móvel (Luiz)
- [ ] Credenciais entregues pelo cofre: termination URI + user/senha SIP + DID BR (Luiz → @devops)
- [ ] **[Rota 1]** `import-phone-number` na Retell + vínculo à Carol · **OU** · **[Rota 2]** jambonz na VPS
- [ ] Smoke test dashboard Retell → celular do Luiz (@devops)
- [ ] `from_number` trocado no `voz-sdr-disparo` (@dev)
- [ ] **Disparo real via webhook `voz-sdr-lead`** → Carol liga (conclusão)

---

## Fontes

- Retell custom telephony (IPs, sem IP estático, auth credencial): https://docs.retellai.com/deploy/custom-telephony
- Retell import-phone-number (campos): https://docs.retellai.com/api-references/import-phone-number
- **Caso BR falho (Fale Vono, INVITE não chega):** https://community.retellai.com/t/retell-byo-sip-trunk-not-sending-invite-to-provider-dial-no-answer/3387
- Retell via jambonz (Rota 2): https://blog.jambonz.org/using-jambonz-for-retell-custom-telephony · https://github.com/jambonz/retell-sip-integration-example
- Nvoip SIP trunk (login/senha sem IP fixo + whitelist): https://www.nvoip.com.br/blog/sip-trunk/ · https://www.3cx.com/docs/sip-trunk/nvoip-brazil/
- DirectCall (register-based, preços): http://directcall.com.br/manual/asterisk/ · http://directcall.com.br/produto/sip-trunk/
- Vozio: https://vozio.com.br/sip-trunk
- Anatel Origem Verificada / STIR-SHAKEN: https://www.mobiletime.com.br/noticias/13/03/2026/origem-verificada-2025/ · https://sippulse.com/en/blog/origem-verificada-stir-shaken-brasil
- Zenvia/TotalVoice voz +55 (verificar direto): https://zenvia.com/blog/perguntas-sobre-totalvoice/

> **NÃO CONFIRMADO** (validar no painel/suporte): tarifa Nvoip móvel BR; aceite real do INVITE
> credencial/whitelist da Retell por Nvoip/DirectCall; encerramento de voz da Zenvia.
