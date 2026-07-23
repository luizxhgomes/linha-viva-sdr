# HISTÓRICO DE CONFIGURAÇÃO — Agente Carol (Retell) · Linha Viva

> **Documento vivo.** Registra o estado real do agente na Retell, os backups brutos e cada
> alteração feita, para que nada se perca entre sessões. **Sempre atualize a seção CHANGELOG
> quando aplicar qualquer mudança**, e gere um novo snapshot bruto em `backups/` antes de mexer.
>
> Cliente: Base Viral / Rodrigo Noll · Agente: **Carol** · Canal: voz (pt-BR)

---

## 🎯 Diretriz do operador (2026-07-21) — a bússola de toda decisão

**Prioridade absoluta = QUALIDADE de conversa natural, NÃO velocidade.**

- Ficou definido (reunião do dia anterior) que deixar a Carol **mais rápida piora** a experiência.
- A voz **rica e mais lenta** (`eleven_multilingual_v2`) traz humanização, respiração e tom
  conversacional. **Manter.** O objetivo é uma ligação cada vez mais natural, nunca robotizada.
- O que se busca é **entendimento enriquecido**: a Carol precisa **entender foneticamente** e
  **capturar corretamente**: (1) nome da pessoa, (2) nome da empresa, (3) segmento,
  (4) modelo/qualidade de indicação que ela já faz hoje.
- Essas variáveis devem ser **salvas** e **reusadas na própria conversa** (personalização viva).
- **Refinar o que já existe, não reconstruir.** Melhoria funcional e operacional sobre a base atual.

---

## 🔖 Referência (IDs)

| Item | Valor |
|---|---|
| Agente | `agent_a32aac07fe9301318d015935d4` (SDR - CAROL) |
| Retell LLM (cérebro) | `llm_c6e2f79f863501dbde1789d6b4f6` |
| Voz custom (Raquel, ElevenLabs) | `custom_voice_b7edce44dfe1c6e54e84a39ba0` |
| API base | `https://api.retellai.com` · auth `Bearer $RETELL_API_KEY` (cofre) |

## 💾 Backups brutos (imutáveis, datados)

Snapshots completos puxados da API, para restaurar se algo der errado:

| Data | Arquivo | Versão |
|---|---|---|
| 2026-07-21 | `backups/2026-07-21_v5_retell-agent.raw.json` | agente v5 (baseline, ANTES) |
| 2026-07-21 | `backups/2026-07-21_v5_retell-llm.raw.json` | llm v5 (baseline, ANTES) |
| 2026-07-21 | `backups/2026-07-21_v6_retell-agent.raw.json` | agente pós-melhoria (DEPOIS) |
| 2026-07-21 | `backups/2026-07-21_v6_retell-llm.raw.json` | llm pós-melhoria (DEPOIS) |
| 2026-07-21 | `backups/2026-07-21_v7_retell-agent.raw.json` | agente pós-correções teste real |
| 2026-07-21 | `backups/2026-07-21_v7_retell-llm.raw.json` | llm pós-correções teste real |
| 2026-07-21 | `backups/2026-07-21_v8_retell-agent.raw.json` | agente pós-humanização (voz estável) |
| 2026-07-21 | `backups/2026-07-21_v8_retell-llm.raw.json` | llm pós-humanização (gpt-4.1 + conciso) |
| 2026-07-21 | `backups/2026-07-21_v9_retell-agent.raw.json` | agente pós-polimento (voz 0.5) |
| 2026-07-21 | `backups/2026-07-21_v9_retell-llm.raw.json` | llm pós-polimento (pronúncia BR + fuso) |
| 2026-07-21 | `backups/2026-07-21_v10_retell-agent.raw.json` | agente (sem mudança; par do v10) |
| 2026-07-21 | `backups/2026-07-21_v10_retell-llm.raw.json` | llm pós-agendamento (2 opções + comercial) |
| 2026-07-21 | `backups/2026-07-21_v11_retell-agent.raw.json` | agente pós-pronunciation_dictionary |
| 2026-07-21 | `backups/2026-07-21_v12_retell-agent.raw.json` | **agente pós-troca de voz (Cartesia Hailey BR)** |
| 2026-07-21 | `backups/2026-07-21_v12_retell-llm.raw.json` | llm (par do v12) |
| 2026-07-21 | `backups/2026-07-21_v13_retell-agent-REVERT-raquel.raw.json` | **reversão p/ voz Raquel** |
| 2026-07-21 | `backups/2026-07-21_v14_retell-agent.raw.json` | agente pós-redução backchannel_frequency |
| 2026-07-21 | `backups/2026-07-21_v15_retell-agent.raw.json` | **agente com eleven_v3 (dicionário IPA ativo)** |
| 2026-07-21 | `backups/2026-07-21_v16_retell-llm.raw.json` | llm com coleta obrigatória de variáveis |
| 2026-07-21 | `backups/2026-07-21_v17_retell-agent.raw.json` | agente pós-otimização de latência |
| 2026-07-21 | `backups/2026-07-21_v17_retell-llm.raw.json` | llm gpt-4.1-mini (latência+custo) |

> **Nota sobre versionamento Retell:** o agente está em draft (`is_published: false`), então o
> número interno da Retell **continua `version 5`** — editar um draft atualiza a versão corrente
> em vez de criar uma nova. Nossa numeração "v6" é lógica (a rodada de melhoria de 2026-07-21).

> **Como restaurar:** `PATCH /update-agent/{id}` e `PATCH /update-retell-llm/{id}` com o corpo do snapshot.
> **Antes de qualquer alteração futura:** gerar novo `backups/AAAA-MM-DD_vN_*.raw.json`.

---

## 📸 Estado real ATUAL (snapshot v5 · 2026-07-21)

### Voz / fonética
| Campo | Valor atual |
|---|---|
| voice_id | `custom_voice_b7edce44...` (Raquel BR) ✅ voz certa |
| voice_model | `eleven_multilingual_v2` (rico) ✅ manter |
| voice_temperature | `1.1` |
| voice_speed | `0.95` |
| enable_dynamic_voice_speed | `false` |
| volume | `1` |

### Cérebro (LLM)
| Campo | Valor atual |
|---|---|
| model | `gpt-5.6-terra` (mais inteligente) ✅ manter p/ qualidade |
| model_temperature | `0.9` |
| general_prompt | ~8.8k chars — rapport + gatilhos + fluxo (bem construído) |
| general_tools | nenhuma |

### Conversa / áudio
| Campo | Valor atual |
|---|---|
| stt_mode | `accurate` ✅ manter (qualidade de entendimento) |
| responsiveness | `0.4` |
| enable_dynamic_responsiveness | `false` |
| interruption_sensitivity | `0.55` |
| enable_backchannel | `true` |
| backchannel_words | aham, ah-sim, sei, entendi, certo, isso, uhum, ok, beleza, perfeito |
| ambient_sound | `call-center` · volume `0.78` |
| begin_message_delay_ms | `1200` |
| boosted_keywords | Show, Bacana, Entendi, Maravilha, Top demais, Vai ser incrível |

### Extração pós-chamada (aplicada no agente)
Apenas 2 campos: `resumo_detalhado_da_chamada`, `user_sentiment`.
> Existe schema rico desenhado em `custom-analysis-schema.json` (11 campos) **ainda não aplicado**.

---

## 🛠️ PLANO DE MUDANÇA 2026-07-21 — status: **✅ APLICADO E VALIDADO**

Foco: qualidade, fonética e captura de variáveis. **Nenhuma mudança que priorize velocidade.**

### A) Agente — ajustes de QUALIDADE/CLAREZA (`/update-agent`)
| Campo | De | Para | Motivo |
|---|---|---|---|
| ambient_sound_volume | 0.78 | **0.35** | 0.78 abafa a voz; baixar dá clareza e naturalidade |
| voice_temperature | 1.1 | **1.0** | mantém expressividade, reduz instabilidade de pronúncia |
| backchannel_words | (com ah-sim, ok) | remover `ah-sim`, `ok`; manter aham/sei/entendi/certo/isso/uhum | mais PT-BR natural |
| boosted_keywords | 6 genéricos | + Base Viral, Rodrigo Noll, Ecossistema de Indicação, indicação, imersão, VPI, CRM | reconhecer certo os termos do negócio |
| voice_model | multilingual_v2 | **inalterado** | qualidade rica (diretriz) |
| voice_speed / stt_mode | 0.95 / accurate | **inalterados** | não acelerar; manter entendimento fino |

*(Opcionais finos, só com seu OK: `responsiveness 0.4→0.55` e `begin_message_delay_ms 1200→800` — corrigem silêncios artificiais que soam robóticos, sem "acelerar" a fala. Deixados de fora por padrão para respeitar 100% a diretriz de não priorizar velocidade.)*

### B) Prompt do LLM — CAPTURA + CONFIRMAÇÃO FONÉTICA + REUSO (`/update-retell-llm`)
Adicionar **uma seção nova** (sem tocar no resto do prompt) instruindo a Carol a:
1. capturar e guardar **nome, empresa, segmento, modelo de indicação**;
2. **confirmar foneticamente** de forma natural quando não captar bem ("é [nome], né?" / "a [empresa], isso?") — nunca soletrar como robô;
3. **reusar** esses dados ao vivo (chamar pelo nome, citar empresa/segmento nos exemplos).

### C) Schema de extração — SALVAR as variáveis (`post_call_analysis_data`)
Aplicar o schema rico de `custom-analysis-schema.json` **+ 2 campos novos**:
- `nome_empresa` (string)
- `modelo_indicacao` (string — como/qual modelo de indicação a pessoa já faz hoje)

---

## ⏳ Pendências / Backlog

| Prioridade | Item | Contexto |
|---|---|---|
| 🔴 Alta | **Backend da promessa de fechamento** — a partir da v7 a Carol PROMETE (na conversa) que vai registrar na agenda + confirmar no WhatsApp + enviar o link da call. **Isso ainda NÃO acontece de verdade.** Falta construir o fluxo pós-call (n8n): criar evento no Google Agenda/Cal.com + disparar WhatsApp com confirmação e link. Decisão do operador (2026-07-21): manter só a fala por enquanto; construir o backend em seguida. Conecta com [[projeto_recepcao_viva_agente_whatsapp]]. |
| 🟡 Média | **Latência × inteligência (v19)** — medição real: e2e p50 ~2,3s; gargalo = STT+endpointing (~1,3s), não o LLM (~600ms). v19 aplicou `gpt-4.1` full (mais esperto) + `enable_dynamic_responsiveness:true` (turn-taking adaptativo). **Re-medir na próxima call** e comparar. Se precisar cortar mais: `custom_stt_config` Deepgram endpointing curto (risco médio de atropelo). |
| 🟢 Baixa | **Telefonia BR real** (SIP trunk) — ver `SETUP-TELEFONIA-BR.md`. Web call segue como demo/backup. |

## 📝 CHANGELOG

| Data | Versão | O que mudou | Backup antes |
|---|---|---|---|
| 2026-07-22 | **v22 (✅ FIXADA — CONFIG OFICIAL DA DEMO 23/07)** | **Resolução das travadas + config final aprovada pelo operador**, com medição real de latência a cada passo. Consolidação da rodada v19→v22: **cérebro** gpt-4.1-mini→full→**mini** (operador optou por consistência: o full dava picos de LLM 1093ms = as travadas; mini ~600ms consistente, papo levemente menos elaborado mas fluidez estável); **voz** eleven_v3→**eleven_flash_v2_5** (v3 instável, "várias vozes numa frase"; flash estável + TTS 160ms); **STT** fast→accurate→**Deepgram custom `endpointing_ms:300`** (accurate era preciso mas +500ms de travada; Deepgram = preciso E rápido, derrubou e2e de 2,8s→**~2,3s p50**); **bordão** `begin_message` +"assistente virtual da Base Viral" (disclosure IA garantida); `voice_temperature` 0.5→**0.3** (estabiliza). **Decisão consciente do operador:** NÃO reduzir endpointing abaixo de 300ms nem espremer mais latência — risco de atropelo/resposta a fala incompleta não compensa 300ms numa demo (o piso saudável de latência é a "escuta educada"). MANTIDOS: Deepgram 300ms, flash_v2_5, `enable_dynamic_responsiveness:true`, responsiveness 0.9, interruption 0.55, `model_high_priority:true`. Validado por re-GET. **Latência final: e2e p50 ~2,3s (TTS 160ms + LLM ~600ms + escuta ~1,5s).** | `2026-07-22_v21_*` |
| 2026-07-22 | v20 (aplicado) | **Correções pós-teste real do operador** (screenshot da web call). 3 problemas: (1) abertura sem "assistente virtual", (2) voz "várias vozes numa mesma frase" (instabilidade), (3) fonética não brasileira. **Diagnóstico:** (1) o `begin_message` FIXO no LLM estava "Carol, da Base Viral" SEM "assistente virtual" — não era o LLM parafraseando, era o bordão fixo incompleto; (2)+(3) = `eleven_v3` (modelo expressivo, **instável por natureza**; o próprio ElevenLabs não recomenda v3 p/ agentes de voz em tempo real → inconsistência call-a-call explica aprovar na v15 e reprovar agora). **Aplicado:** `begin_message` → +"assistente virtual da Base Viral" (disclosure IA garantida em toda call) · `voice_temperature` 0.5→**0.3** (estabiliza o v3). MANTIDOS gpt-4.1, dynamic_responsiveness, voz Raquel eleven_v3. **Pendente decisão do operador (voz):** se temp 0.3 não estabilizar/desanglicizar o bastante, próxima rota = trocar `voice_model` eleven_v3→**turbo_v2_5** (estável/consistente, mas perde IPA de nomes → trade-off estabilidade × pronúncia). Só o operador decide ouvindo. Única voz BR nativa na conta = Cartesia Hailey (já rejeitada). | `2026-07-22_v19_*` |
| 2026-07-22 | v19 (aplicado) | **Otimização latência × inteligência** (objetivo do operador: responder RÁPIDO **e** esperto em conversa informal; persona/voz já validadas). **Diagnóstico por medição real (`list-calls`):** e2e p50 ~2,3s (picos 3,3s), mas LLM só ~600ms e TTS ~400ms → o gargalo é **STT+endpointing (~1,3s), NÃO o cérebro**. Pesquisa Retell confirmou: o mini é "mais fraco em multi-turn". **Aplicado:** LLM `gpt-4.1-mini`→**`gpt-4.1` full** (mais coerente multi-turn = papo mais esperto; custo de latência baixo pois LLM não é gargalo + `high_priority` já ativo) + **`enable_dynamic_responsiveness` false→true** (turn-taking adaptativo: responde rápido pra quem fala rápido, espera pra quem hesita — ataca o gargalo sem atropelar). **MANTIDOS:** responsiveness 0.9, `stt_mode` fast, voz `eleven_v3`, prompt (14290 chars) intocado. **Descartado:** `s2s_model`/realtime (forçaria voz nativa OpenAI, mataria Raquel/IPA de nomes — requisito duro). **Descoberto:** `handbook_config` já tem `natural_filler_words`+`high_empathy`+`conversational_personality`+`ai_disclosure` ativos (humanização nativa da Retell). **A validar em call:** re-medir e2e. **Opções extras se precisar cortar mais:** `enable_dynamic_voice_speed` (naturalidade, exige QA de pronúncia ao acelerar) e `custom_stt_config` Deepgram `endpointing_ms` curto (corta o maior pedaço, mas risco médio de atropelo). Validado por re-GET. | `2026-07-22_v18_*` |
| 2026-07-21 | v5 (baseline) | Estado inicial documentado + snapshots brutos salvos | — |
| 2026-07-21 | v6 (aplicado) | **A)** ambient_volume 0.78→0.35 · voice_temperature 1.1→1.0 · backchannel só PT-BR natural · boosted_keywords 6→13 (termos do negócio). **Ritmo:** responsiveness 0.4→0.55 · begin_delay 1200→800ms. **B)** prompt +seção "CAPTURA, CONFIRMAÇÃO E REUSO" (nome/empresa/segmento/modelo de indicação + confirmação fonética natural + reuso ao vivo); texto original 100% preservado (8849→10109 chars). **C)** extração pós-call 2→14 campos (inclui `nome_empresa` e `modelo_indicacao`). Mantidos: voz `multilingual_v2`, `voice_speed 0.95`, `stt_mode accurate`. Validado por re-GET campo a campo. | `2026-07-21_v5_*` |
| 2026-07-21 | v17 (aplicado) | **Otimização de latência (operador escolheu "Opção 2" após ver alternativas+preços).** Delay medido v16: e2e p50=2385ms (ASR 568 accurate + LLM 678 gpt-4.1-Fast + TTS 635 eleven_v3 + ~380 espera). Aplicado: `stt_mode` accurate→**fast**, `responsiveness` 0.7→**0.9**, LLM `gpt-4.1`→**`gpt-4.1-mini`**. Voz `eleven_v3` MANTIDA (pronúncia intacta). Ganho estimado ~800ms (e2e ~1.6s, a confirmar na próxima call). Custo/min $0.163→**$0.111** (−32%): base $0.055 + ElevenLabs $0.040 + gpt-4.1-mini $0.016. | `2026-07-21_v16_*` |
| 2026-07-21 | v16 (aplicado) | **Coleta obrigatória das variáveis do score** (operador: interatividade 100%, mas a call `call_5b8f3f5b...` saiu com `nome_empresa` VAZIO no score — a conversa flui tão solta que pula coletas). Diagnóstico por extração: nome ok, empresa VAZIA (nunca perguntou), segmento veio por tabela, modelo ok. Prompt: (1) seção CAPTURA agora exige as 4 (nome/empresa/segmento/modelo) OBRIGATÓRIAS antes do convite + checklist mental + exemplos de captar empresa/segmento com leveza ("como chama a sua empresa?"); (2) fluxo passo 2 pede nome da empresa + segmento explícitos, "não avança sem". Leveza preservada ("nunca como formulário nem interrogatório"). 13386→14290 chars. Validado por re-GET. | `2026-07-21_v15_*` |
| 2026-07-21 | v15 (✅ VALIDADO) | **RAIZ da pronúncia de nomes resolvida — APROVADO pelo operador** ("nome saiu certo, está com sotaque, achei bom demais"). (nome "Luiz" saía "LOOUIZ" mesmo com pronunciation_dictionary aplicado desde v11). **Diagnóstico verificado (doc ElevenLabs + teste na API):** phoneme tags IPA só funcionam em `eleven_flash_v2` e `eleven_v3`; `multilingual_v2` IGNORA o dicionário → por isso o IPA nunca valeu. Teste de modelos na voz Raquel: `flash_v2` rejeitado (não suporta pt); `flash_v2_5`/`turbo_v2_5` aceitos mas SEM IPA; **`eleven_v3` aceito, suporta pt E IPA**. Solução: trocar só o `voice_model` da MESMA voz Raquel `multilingual_v2`→**`eleven_v3`** → dicionário IPA passa a valer (Luiz=/luˈis/). ⚠️ Trade-off: v3 é modelo mais novo/expressivo, timbre pode variar um pouco — validar auditivamente; reverter = PATCH voice_model multilingual_v2 (backups v13/v14). | `2026-07-21_v14_*` |
| 2026-07-21 | v14 (aplicado) | **Backchannel "Gierto"** (operador ouviu a Carol soltar "gierto" no meio da conversa). **Raiz (verificada na doc Retell `/build/interaction-configuration`):** backchannel são palavras geradas isoladas pela mesma voz TTS; a doc confirma "certas vozes não funcionam bem com certas palavras"; `certo` está no default pt+11labs mas a voz Raquel o distorce em "gierto" (palavra curta isolada, sem contexto). Mapeadas 4 viabilidades (curar lista / desligar / reduzir freq / dicionário-incerto); **operador escolheu REDUZIR FREQUÊNCIA**: `backchannel_frequency` 0.6→0.3 (paliativo — reduz incidência, mantém a escuta ativa). Backchannel segue ligado com as 6 palavras. | `2026-07-21_v13_*` |
| 2026-07-21 | v13 (REVERSÃO) | Operador testou a voz Cartesia Hailey (v12) e **rejeitou**: soou lenta, fonética estranha, "não parece brasileira". **Voz revertida para a Raquel** (ElevenLabs `custom_voice_b7edce44...`, `eleven_multilingual_v2`, temp 0.5, speed 0.95). Todos os ganhos de conversa preservados (gpt-4.1, agendamento comercial, fechamento WhatsApp, concisão). Backup: `2026-07-21_v13_retell-agent-REVERT-raquel.raw.json`. ⚠️ **Lição:** voz nativa "no papel" ≠ boa na prática; NUNCA trocar voz sem o operador validar na call real (preview curto engana). A anglicização de nomes segue como questão em aberto, a atacar só sob comando do operador. | `2026-07-21_v12_*` |
| 2026-07-21 | v11→v12 (aplicado) | **CORREÇÃO DE RAIZ da anglicização de nomes** (5º teste: TTS leu "Luiz" como "LOOU-IZI" com fonética inglesa; operador rejeitou solução nome-a-nome como "tapar buraco"). **Diagnóstico (verificado na doc Retell + list-voices):** a causa é a VOZ — `eleven_multilingual_v2` decide o idioma palavra por palavra e anglifica; a doc confirma "a voz é o determinante primário do sotaque"; dicionário/SSML/phonetic-spelling são todos caso-a-caso. **v11 (descartado como principal):** `pronunciation_dictionary` IPA com nomes comuns (só remendo). **v12 (solução real):** troca da voz para a única nativa brasileira da conta — **`cartesia-Hailey-Portugese-Brazilian` (modelo `sonic-3`)**. Voz treinada em PT-BR não tem fonética inglesa → zero anglicização por construção, para qualquer lead. Operador escolheu o timbre novo (vs manter Raquel+turbo). `voice_speed 0.95`, `pt-BR`, prompt e demais ajustes preservados. ⚠️ voz Raquel (ElevenLabs `custom_voice_b7edce44...`) fica no backup v11 se quiser voltar. | `2026-07-21_v11_*` |
| 2026-07-21 | v10 (aplicado) | **Enquadramento do agendamento** (4º teste: operador aprovou voz/pronúncia/fuso da v9 — latência p50=2289ms; mas a Carol ofereceu "essa semana ou semana que vem" + "manhã/tarde/noite" e ACEITOU meia-noite quando testada). Correções no prompt: (A) passo 6 agora exige SEMPRE 2 opções concretas (dia da semana + data + hora exata) e horário SEMPRE comercial 8h–19h, com recusa educada + re-oferta se pedirem fora; proibido "essa semana ou semana que vem" e "manhã/tarde/noite" solto; (B) perguntas vêm SOZINHAS (sem textão emendado) + pitch em pedacinhos checando com a pessoa. Só prompt (voz intacta). 12466→13386 chars, original preservado. Validado por re-GET. | `2026-07-21_v9_*` |
| 2026-07-21 | v9 (aplicado) | **Polimento fino** (3º teste: operador APROVOU agilidade/conversacional/interatividade; latência confirmada 3236→2319ms p50, encerramento por user_hangup). Correções finas: (1) TTS lia "V4" como "V four" → prompt regra de pronúncia 100% BR ("vê quatro", "agá dois ó", "três eme"); (2) Carol verbalizava "na região de Brasília" (ruim) → raciocina no fuso mas não fala em voz alta, só a hora; (3) início de frase ainda robótico → `voice_temperature` **0.4→0.5** (respiro sem o caos de 1.0). Prompt 12037→12466 chars, original preservado. Validado por re-GET. | `2026-07-21_v8_*` |
| 2026-07-21 | v8 (aplicado) | **Humanização definitiva** (feedback do 2º teste: voz mudando tom/volume/"sotaque" aleatoriamente e robótica, demora pra responder, textão que se perde do objetivo). **Causa→correção:** (1) voz aleatória = `voice_temperature` alto → **1.0→0.4** (voz estável/consistente); (2) demora = `gpt-5.6-terra` pesado (3236ms) → modelo **gpt-4.1** (a Retell normaliza "gpt-4o"→gpt-4.1; ~1s, fluido); (3) textão/divagação = `model_temperature` **0.9→0.6** + prompt "COMO VOCÊ FALA" endurecido (1 frase por vez, terminar com pergunta, cortar >2 linhas) + nova diretriz "SEU NORTE" (sempre rumo ao agendamento); (4) espera pra responder = `responsiveness` **0.55→0.7**. Mantidos: voz Raquel `multilingual_v2`, `voice_speed 0.95`, `max_duration 10min`, `high_priority`. Prompt 11315→12037 chars, original preservado. Validado por re-GET. | `2026-07-21_v7_*` |
| 2026-07-21 | v7 (aplicado) | **Correções pós-teste real** (call `call_944b3c25...`, diagnóstico: latência p50=3236ms, call cortada por `max_duration`, STT ouviu "Cio" no lugar de "SEAL", LLM leu "meio-dia" como 11h e entrou em loop). **Prompt:** (1) regra de horário PT-BR (meio-dia=12h etc. + sem loop se corrigido); (2) confirmação de nome/empresa agora OBRIGATÓRIA; (3) fechamento enriquecido — Carol avisa que registra na agenda + confirma no WhatsApp + envia link da call pelo WhatsApp + se despede (nunca encerra sem isso). 10109→11315 chars, original preservado. **Config:** `max_call_duration_ms` 302000→600000; `boosted_keywords` +SEAL/+SEAL Digital (15). **Latência:** `model_high_priority` false→true (fila dedicada, modelo mantido). Validado por re-GET. | `2026-07-21_v6_*` |
