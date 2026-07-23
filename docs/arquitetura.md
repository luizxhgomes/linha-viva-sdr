# Design Técnico — Linha Viva (SDR de Voz)

> Documento técnico-estratégico. Sustenta os claims da `OFERTA.md` e guia a implementação. O que importa em voz é uma conta de milissegundos: o "não parece IA" é latência, não engano.

## 1. Decisão de stack

| Camada | Escolha | Papel |
|---|---|---|
| Orquestração de chamada | **Retell AI** | turn-taking, streaming STT, gestão de LLM e TTS, webhooks, extração pós-chamada |
| Cérebro (LLM) | modelo rápido (classe mini/realtime) | decide o próximo passo com TTFT baixo |
| Voz (TTS) | **ElevenLabs Flash v2.5** (voz Catarina) | fala PT-BR natural, ~75ms de inferência |
| Automação de negócio | **n8n** (`n8n.bilinski.cloud`) | disparo, idempotência, roteamento, CRM, agenda, retry |
| Telefonia | **Twilio SIP** + número importado na Retell | discagem BR em produção |
| CRM / agenda | GHL / Google / Cal.com | card do lead, agendamento, handoff |

Agente atual: `agent_a32aac07fe9301318d015935d4`. From: `+14706164413`.

## 2. Orçamento de latência (o coração do produto)

O protótipo atual responde em 1675 a 2225ms, alto demais (denuncia a IA). O alvo é **p50 sub-1s**. Como se chega lá:

| Alavanca | Impacto | Ação |
|---|---|---|
| Modelo LLM | grande | trocar GPT-5 por modelo rápido (TTFT baixo). É o 1º suspeito da latência atual |
| TTS | grande | ElevenLabs Flash v2.5 (~75ms) no lugar de modelo pesado |
| Tamanho da resposta | médio | prompt força 1 a 2 frases por turno (respostas curtas chegam antes) |
| Modo de transcrição | médio | "otimizar para velocidade" no lugar de "precisão" (mitigado por boosted keywords) |
| RAG | médio | conhecimento-núcleo no prompt; base de conhecimento só para objeção/FAQ, fora do turno |
| Rede | fixo | BR para infra Retell (US) soma 100 a 250ms. Web call reduz o hop |

Meta realista em BR via PSTN: p50 800 a 1000ms, p90 abaixo de 1300ms. A Retell reporta cerca de 714 a 800ms voice-to-voice em condições ideais. Testar com 15 a 20 ensaios e teste cego (meta 8 de 10 em "pareceu humano?").

## 3. Humanização (parâmetros que mais importam)

Ver `agente/retell-config.json` para os valores. Os de maior impacto:
- **Barge-in** (`interruption_sensitivity 0.75`): o lead interrompe e a Carol para na hora, como gente.
- **Responsividade** (`responsiveness 1.0` + ajuste dinâmico): resposta ágil, adapta ao ritmo.
- **Backchannel** (aham, sei, entendi, certo): escuta ativa durante a fala do lead.
- **Normalize for speech**: valores, datas e horas falados por extenso.
- **Som ambiente** (central de atendimento, volume baixo): silêncio estéril denuncia a IA.
- **Voz**: Flash v2.5, `voice_speed 1.05`, `voice_temperature 0.9`.

## 4. Arquitetura do fluxo (resumo)

5 workflows n8n (spec node a node em `n8n/SPEC-workflows.md`):
`voz-lib-config` (constantes), `voz-sdr-disparo` (lead para ligação), `voz-sdr-webhook` (eventos Retell), `voz-sdr-pos-chamada` (roteamento por resultado), `voz-sdr-on-error`. Idempotência via Redis (`SET NX EX`), verificação de assinatura do webhook, e `call_analyzed` como único gatilho de automação.

## 5. Extração pós-chamada e roteamento

A Retell extrai (modelo Claude Sonnet) os campos de `agente/custom-analysis-schema.json`. O `proximo_passo` roteia no n8n: agendar (CRM + agenda + notifica o closer), nurture (por objeção), remover_lista (opt-out), retry (não atendeu, máximo 2). Handoff quente ao closer com resumo e gravação.

## 6. Modelo de custo unitário

| Item | Faixa (por minuto de ligação) |
|---|---|
| STT + LLM + TTS + telefonia + orquestração | R$0,72 a R$1,38/min (all-in) |

- Operação de referência de 1.000 minutos/mês: cerca de R$720 a R$1.380 de tecnologia.
- Cabe folgado dentro dos R$3.000/mês do tier Prontidão (margem saudável para operação gerenciada e tuning).
- Comparação de valor: 1 SDR humano carregado no Brasil custa R$5.600 a R$16.100/mês, com rampa de 30 a 90 dias e turnover. Fonte: ver `ESTUDO-MERCADO.md`.

## 7. Telefonia BR

- Número Retell nativo só disca destinos US. Para BR: **Twilio Elastic SIP Trunk** + DID BR importado na Retell em E.164, whitelist do SBC Retell `18.98.16.120/30`.
- Regulatory Compliance (RC) Bundle do Twilio leva cerca de 2 a 3 semanas de aprovação. Para a demo de quinta, confirmar se `+14706164413` já disca BR; se não, usar web call.
- Anatel 2025: prefixo 0303 deixou de ser obrigatório (agosto/2025); aderir à Origem Verificada quando a operadora oferecer.

## 8. Multi-tenant e escalabilidade (multi-SDR)

A mesma fundação (telefonia, número, n8n, CRM, voz) serve vários SDRs. Cada novo SDR é um novo agente Retell + variação de roteiro, reusando os 5 workflows com `retell_agent_id` e `from_number` parametrizados no `voz-lib-config`. É o que sustenta a escada de expansão (SDR 2 reativação de base, SDR 3 anti no-show) com setup reduzido.

## 9. Compliance by design

Ver `legal/PARECER-LEGAL.md` (12 invariantes). Embutidas no produto: disclosure de IA + aviso de gravação na abertura, opt-out em tempo real com supressão global, gate de compliance pré-discagem (Não Me Perturbe + janela de horário por DDD + limite de tentativas), preço só da fonte de verdade, PII redaction, retenção limitada.

## 10. Itens a validar

- `voice_id` da Catarina (ElevenLabs).
- `+14706164413` disca BR (Twilio) ou só US.
- Modelo LLM final (após medir latência).
- CRM/agenda a integrar.
- Web Call habilitado na conta Retell (para o fallback da demo).
