# RUNBOOK — Como acionar o protótipo Linha Viva (Carol)

> Guia rápido para você (Luiz) rodar a demo sozinho e para retomarmos a integração VOIP.
> Estado em 2026-07-21.

## O que continua NO AR mesmo com o terminal fechado (nuvem, não mexer)

Isso já está configurado e ativo, não precisa fazer nada:
- **Agente Retell "SDR - CAROL"** (`agent_a32aac07fe9301318d015935d4`): voz Raquel (ElevenLabs Multilingual v2), prompt v7 (rapport + gancho + gatilhos + provas sociais), extração pós-chamada, webhook apontando para o n8n.
- **Workflows n8n ativos** em `n8n.bilinski.cloud`: `voz-web-call`, `voz-call-resumo`, `voz-sdr-disparo`, `voz-sdr-pos-chamada`, `voz-sdr-on-error`, `voz-lib-config`.
- **Endpoints** (funcionam a qualquer momento):
  - Web call (cria a conversa): `POST https://n8n.bilinski.cloud/webhook/voz-web-call`
  - Resumo da chamada: `POST https://n8n.bilinski.cloud/webhook/voz-call-resumo`

## Para ACIONAR a demo do web call (a página que você abre e conversa)

Só o servidor local precisa ser reiniciado. Abra um terminal e rode:

```bash
cd "/Users/luizhenrique/Enterprise/active/AIOX_Fullstack/Negocios/produto/sdr-ia-ligacao/demo"
python3 -m http.server 8000
```

Depois abra no navegador: **http://localhost:8000/web-call.html** e clique em "Falar com a Carol".
(Deixe o terminal aberto enquanto usa. Para parar, Ctrl+C.)

## Alternativa sem servidor: testar no dashboard Retell

Dashboard Retell → agente **SDR - CAROL** → aba **Simulação** → botão do microfone / **Executar teste**. Conversa com a Carol na hora, sem subir nada.

## Referência rápida (IDs e caminhos)

| Item | Valor |
|---|---|
| Agente Retell (Carol) | `agent_a32aac07fe9301318d015935d4` |
| Retell LLM (cérebro/prompt) | `llm_c6e2f79f863501dbde1789d6b4f6` |
| Voz (Raquel, ElevenLabs) | `custom_voice_b7edce44dfe1c6e54e84a39ba0` · modelo `eleven_multilingual_v2` |
| Prompt vivo da Carol | `agente/carol-general-prompt-v3.txt` |
| Config do agente | `agente/retell-config.json` |
| Página web call | `demo/web-call.html` |
| Player de demo (pré-gravado) | `demo/index.html` |
| Chaves (cofre) | `.claude/settings.local.json` (env) |
| n8n API key (para eu criar/editar workflow) | `Projetos_Internos/n8n_builder/.mcp.json` |

## Onde ajustar a Carol (se quiser mexer sozinho)

- **Voz, velocidade, expressividade, modelo de voz**: dashboard Retell → agente → Configurações de fala/voz.
- **O que ela fala (prompt)**: dashboard Retell → agente → prompt do Response Engine. A versão viva também está em `agente/carol-general-prompt-v3.txt`.
- Ou me pede que eu ajusto por API na próxima sessão.

## PLANO DE AMANHÃ — telefonia VOIP real (substituir o Twilio)

Twilio saiu (conta suspensa + caro). Vamos com um **SIP trunk brasileiro** (número BR real, tarifa nacional, sem bloqueio geográfico). Guia completo em `agente/SETUP-TELEFONIA-BR.md`.

**Você traz (idealmente antes da nossa próxima sessão):**
1. Conta criada num provedor BR (recomendo **DirectCall** — `directcall.com.br` — que dá trunk + número para avaliar; alternativas: Vozio, Diskfree, Fale Vono). Cadastro com o CNPJ da SEAL.
2. Um **número BR (DID)** contratado.
3. Do suporte do provedor, os dados de **terminação (outbound)** do trunk:
   - Termination URI / SIP domain.
   - Usuário e senha SIP (ou liberação por IP: peça para liberar o SBC da Retell `18.98.16.120/30`).

**Eu faço (na sessão):**
1. Importo o número BR na Retell como número custom (BYO carrier), apontando pro trunk do provedor.
2. Vinculo ao agente Carol e troco o `from_number` nos workflows.
3. Testo a ligação real para um celular BR e meço a latência.

Depois disso, a Carol liga de verdade (número BR de verdade) e o web call continua como backup.

## Estado geral do pacote (para vender)

Além do backend técnico, o pacote comercial está pronto em `Negocios/produto/sdr-ia-ligacao/`: `OFERTA.md`, `ESTUDO-MERCADO.md`, `legal/PARECER-LEGAL.md`, `apresentacao/ROTEIRO-QUINTA.md` (+ `.html`), `DESIGN-TECNICO.md`.
