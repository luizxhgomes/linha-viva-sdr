# Parecer Legal, Produto "SDR de Voz com IA" (Carol, ligações ativas outbound com IA)

> **Emitente:** Squad Legal (legal-chief, orquestrando lgpd-specialist, cdc-specialist, telecom/Anatel e IA).
> **Data:** 2026-07-20.
> **Objeto:** análise técnico-jurídica do produto SDR de voz com IA da SEAL DIGITAL: uma agente de IA por voz ("Carol") que faz ligações ativas (outbound) para leads que se aplicaram a uma imersão de vendas (contexto Rodrigo Noll / Base Viral), qualifica o lead e agenda reunião. Stack Retell AI + ElevenLabs + n8n. A ligação é gravada e há extração de dados pós-chamada. A SEAL DIGITAL opera o produto como fornecedora do cliente (a SEAL é a OPERADORA, o cliente que detém a base de leads é o CONTROLADOR).
> **Escopo:** compliance LGPD, CDC, regras de telecom/Anatel de 2025-2026 aplicáveis a chamadas ativas, direitos de personalidade sobre a voz, marco legal de IA vigente e em tramitação, e alocação de risco contratual. Não abrange direito tributário (já tratado em memória própria da SEAL), societário nem trabalhista.
> **RESSALVA:** este é um parecer técnico-jurídico interno de apoio à decisão de produto. NÃO substitui advogado constituído para redação e revisão dos contratos, termos e políticas finais, que devem ser assinados por profissional habilitado antes do primeiro cliente pagante entrar no ar. Pontos de incerteza real estão marcados com [A CONFIRMAR COM ADVOGADO].

---

## Sumário Executivo

### Os 8 vereditos em farol

1. **Disclosure de IA (a Carol pode se passar por humana?):** 🔴 BLOQUEANTE no recorte "negar ser IA". Fazer a Carol se passar por pessoa, ou negar ser IA quando perguntada, é o risco mais grave do produto, porque na voz a ilusão de humanidade é muito mais forte que no texto. A conduta segura, e que deve virar trava técnica não desativável, é a Carol se identificar como assistente virtual logo na abertura e NUNCA negar ser IA. Com essa trava, o item vira 🟢.
2. **Aviso de gravação no início:** 🟡 CONDICIONAL, vira 🟢 com o aviso embarcado. Gravar a própria conversa por um interlocutor não é ilícito penal, mas a SEAL grava de forma sistemática e extrai dados, o que é tratamento de dado pessoal e aciona o dever de transparência da LGPD. O aviso de gravação e finalidade na abertura resolve.
3. **Base legal para ligar (outbound):** 🟢 com documentação. O lead se aplicou a uma imersão, logo existe opt-in e expectativa legítima de contato. O legítimo interesse (art. 7, IX) sustenta a ligação de qualificação, desde que haja teste de balanceamento documentado, prova da origem do dado e opt-out fácil.
4. **Voz como dado:** 🟡 CONDICIONAL. A voz do lead gravada é dado pessoal (comum, desde que não se faça biometria de voz). A voz sintética de biblioteca da Carol é segura. Clonar a voz de uma pessoa real (o cliente, um vendedor, o Rodrigo Noll ou qualquer terceiro) exige termo escrito, específico e revogável do titular da voz, sob pena de violar direitos de personalidade.
5. **Anatel / telemarketing 2025-2026:** 🟡 ATENÇÃO. O prefixo 0303 deixou de ser obrigatório (agosto de 2025) e virou facultativo. A autenticação "Origem Verificada" (STIR/SHAKEN, Resolução Anatel 777/2025) é obrigatória para grandes originadores (acima de 500 mil chamadas por mês) e o regime vigora de 01/11/2025 a 31/10/2028. Há teto de robocall (bloqueio acima de 100 mil chamadas abusivas por dia). A lista "Não Me Perturbe" e a janela de horário de discagem são de cumprimento obrigatório.
6. **Opt-out em tempo real:** 🔴 BLOQUEANTE se ausente. A Carol precisa reconhecer, na hora, o pedido de "não me ligue mais", confirmar, encerrar e suprimir o lead de TODAS as sequências, não só da campanha atual. Sem isso o produto viola o direito de oposição (LGPD art. 18) e é abusivo (CDC). Com opt-out em tempo real e supressão global, vira 🟢.
7. **Retenção e segurança dos dados de voz e transcrição:** 🟡 CONDICIONAL. Exige minimização, prazo de retenção definido, redação (masking) de PII na transcrição, acesso restrito, criptografia, DPA com os suboperadores (Retell, ElevenLabs, hospedagem do n8n) e tratamento da transferência internacional, já que parte da stack é de provedores fora do Brasil.
8. **CDC, preço e ofertas ditas pela Carol:** 🟡 CONDICIONAL. Tudo que a Carol afirmar sobre preço, condição, bônus, garantia, data ou vaga vincula o fornecedor (art. 30). A Carol só pode dizer o que vier da fonte de verdade cadastrada, nunca inventar valor, desconto, prazo ou escassez.

### As 3 mudanças mais urgentes

1. **Abertura padrão da Carol, com 3 elementos travados na primeira fala.** Antes de qualquer coisa a Carol deve: (a) identificar-se como assistente virtual (IA) do cliente, (b) avisar que a ligação é gravada e para quê, e (c) dizer o motivo do contato (você se aplicou à imersão). Mais a trava dura "nunca negar ser IA nem afirmar ser humana", inegociável, não desativável nem a pedido do cliente. Isso sozinho neutraliza o maior risco do produto (Questões 1 e 2).
2. **Motor de compliance de discagem no n8n, antes de o Retell discar.** Embarcar como gate pré-chamada: supressão contra a lista Não Me Perturbe e contra a do-not-call interna, respeito à janela de horário por fuso do DDD, limite de tentativas por lead, e detecção de opt-out em tempo real dentro da conversa com supressão global. Sem esse motor o produto viola Anatel, CDC e LGPD ao mesmo tempo (Questões 5 e 6).
3. **DPA, retenção e redação de PII para os dados de voz.** Antes do primeiro cliente pagante: DPA operador-controlador, contratos e/ou termos com Retell e ElevenLabs como suboperadores, cláusula de transferência internacional, prazo de retenção conservador com expurgo, masking de CPF e demais PII na transcrição, e preço vindo só do cadastro (RAG), nunca inventado (Questões 4, 7 e 8).

---

## Questão 1, Disclosure de IA, a Carol pode se passar por humana?

**Veredito: 🔴 BLOQUEANTE no recorte "negar ser IA / se passar por humana". 🟢 com a identificação de abertura e a trava embarcada.**

### Fundamento

A ligação de voz é o cenário de MAIOR risco de engano do mercado de IA conversacional, mais grave do que o WhatsApp por texto, justamente porque a voz sintética moderna (ElevenLabs via Retell) soa humana e o interlocutor não tem o tempo de leitura nem os sinais visuais que o alertariam. Ocultar a natureza automatizada, ou pior, negar ser IA quando perguntado, deixa de ser mera omissão de tom e passa a ser indução ativa a erro.

Freios em normas já em vigor:

- **CDC, art. 6, III e art. 31:** direito à informação adequada e clara sobre o serviço e quem o presta. Deixar o consumidor crer que fala com uma pessoa quando fala com um software é omissão de informação relevante sobre a própria natureza do atendimento.
- **CDC, art. 37, parágrafo 1:** é enganosa a comunicação que induz o consumidor a erro, inclusive por omissão. Negar ser IA, ou afirmar ser humana, é o núcleo duro dessa vedação.
- **CDC, art. 39, IV:** é abusivo prevalecer-se da fraqueza ou ignorância do consumidor. Uma voz que finge ser humana para persuadir tensiona diretamente esse dispositivo.
- **LGPD, art. 6, VI (transparência) e art. 9:** o titular tem direito ao acesso facilitado e claro sobre o tratamento. Coletar dados sob aparência humana enfraquece a transparência devida.
- **PL 2338/2023 (marco legal de IA):** aprovado no Senado em 10/12/2024, em tramitação na Câmara com votação final prevista para 2026 (relatório final e votação em plenário articulados para maio de 2026, sem sanção até esta data). O texto, no modelo europeu baseado em risco, traz o direito da pessoa de saber que interage com um sistema de IA. É tendência legislativa forte, e a multa prevista chega a R$ 50 milhões por infração, o que reforça tratar o disclosure como requisito, não como enfeite. [A CONFIRMAR COM ADVOGADO] a força vinculante exata do dever de identificação antes da sanção.

### Recomendação prática

Disclosure de abertura em voz, com script curto e natural que não mata a conversão:

1. **Identificação na primeira fala (obrigatória).** A Carol se apresenta como assistente virtual: por exemplo, "Oi, aqui é a Carol, assistente virtual da [cliente]". A palavra "virtual" cumpre o dever sem quebrar o rapport. A conversa segue humanizada normalmente.
2. **Honestidade sob pergunta direta (invariante dura).** Se o lead perguntar "você é robô?", "é uma pessoa de verdade?", a Carol NUNCA nega. Responde com naturalidade que é o atendimento virtual do cliente e que pode transferir para uma pessoa quando fizer sentido.
3. **Handoff humano disponível.** A Carol deve ter caminho de transferir ou registrar retorno humano se o lead pedir para falar com uma pessoa. Isso reforça a boa-fé e reduz a percepção de engano.

Salvaguarda contratual: o contrato SEAL-cliente deve conter (a) cláusula de que a identificação de IA na abertura vem ligada por padrão, (b) declaração de que a trava "nunca negar ser IA nem afirmar ser humana" é inegociável e não pode ser desativada nem por solicitação do cliente, e (c) registro de que, se o cliente insistir em ocultar a natureza automatizada, a SEAL documenta a recusa e o aceite de risco do cliente, preservando prova de que orientou em sentido contrário.

---

## Questão 2, Aviso de gravação da ligação no início

**Veredito: 🟡 CONDICIONAL, vira 🟢 com o aviso embarcado na abertura.**

### Fundamento

Dois planos que não se confundem:

- **Plano penal e probatório:** a gravação de conversa por um dos interlocutores, mesmo sem o conhecimento do outro, é prova lícita no Brasil (posição consolidada do STF, tema de repercussão geral sobre gravação por interlocutor). Ou seja, gravar por si só não é crime nem prova ilícita.
- **Plano da proteção de dados (o que realmente importa aqui):** a SEAL não grava um telefonema pontual, ela grava de forma sistemática, transcreve e extrai dados pós-chamada. Isso é tratamento de dado pessoal em escala (LGPD art. 5, X). O áudio da voz e a transcrição são dados pessoais. Isso aciona o dever de transparência e de informação ao titular (art. 6, VI e art. 9), que tem direito de saber que está sendo gravado, por quem, e para qual finalidade.

Some-se o CDC, art. 6, III (informação clara e adequada). O lead precisa saber, no início, que a chamada é gravada.

### Recomendação prática

1. **Aviso de gravação na abertura, junto do disclosure de IA.** Uma frase curta resolve: "esta ligação é gravada para registro e qualidade do atendimento". Deve constar na primeira fala, antes de qualquer coleta de resposta relevante.
2. **Finalidade declarada e limitada.** Informar, ainda que de forma sucinta, para que serve (registro do atendimento, qualificação, agendamento) e que o lead pode pedir a exclusão e o acesso aos seus dados.
3. **Registro do momento do aviso.** Log com timestamp de que o aviso foi dado, para provar cumprimento do dever de transparência.
4. **Consentimento não é o eixo aqui, transparência é.** Para a gravação em si, a base pode ser o legítimo interesse ou a execução de procedimentos preliminares (ver Questão 3). O que a lei exige de forma dura é o AVISO, não necessariamente um "aceito" formal para gravar. Se o lead recusar a gravação, o fluxo deve ter uma saída (encerrar cordialmente ou seguir sem gravar, conforme decisão de produto do controlador).

---

## Questão 3, Base legal para ligar (outbound), legítimo interesse vs consentimento

**Veredito: 🟢 com teste de balanceamento documentado e prova de origem.**

### Fundamento

O recorte deste produto é favorável: o lead SE APLICOU a uma imersão. Ele preencheu um formulário manifestando interesse. Isso muda tudo em relação a uma ligação fria comprada de lista.

Bases legais disponíveis (LGPD art. 7), em ordem de robustez para o caso:

- **Art. 7, V (execução de procedimentos preliminares a pedido do titular):** o lead pediu contato ao se aplicar ao evento. A ligação de qualificação e agendamento é procedimento preliminar diretamente relacionado ao que ele solicitou. É a base mais aderente.
- **Art. 7, IX (legítimo interesse):** sustenta o contato comercial de qualificação B2B ou B2C quando há expectativa legítima do titular, dados mínimos e opt-out fácil. É a base que a ANPD reconhece para prospecção, desde que documentada.
- **Art. 7, I (consentimento):** disponível se o formulário de aplicação já colheu aceite claro para ser contatado por telefone. É útil como reforço, mas não é imprescindível se V ou IX estiverem bem documentados.

### Teste de balanceamento (LIA), resumido para embarcar no dossiê

1. **Finalidade legítima:** qualificar e agendar reunião com quem se aplicou a uma imersão de vendas. Legítima e específica.
2. **Necessidade:** a ligação é meio adequado e proporcional para qualificar um lead que sinalizou interesse. Não há excesso.
3. **Balanceamento e expectativa do titular:** quem se aplicou a um evento espera ser contatado sobre ele. A expectativa é alta, o que pende a favor do legítimo interesse. Os direitos do titular ficam preservados por opt-out imediato, transparência na abertura e retenção limitada.

### Recomendação prática

1. **Documentar a origem do dado.** Guardar prova de que o lead se aplicou (formulário, data, campanha, consentimento se houver). Sem prova de origem, o legítimo interesse fica frágil.
2. **Registrar a LIA por escrito** e versioná-la. É o documento que a SEAL e o cliente mostram se a ANPD questionar.
3. **Recência.** Ligar dentro de uma janela razoável após a aplicação. Um lead que se aplicou há 18 meses e nunca respondeu perde a expectativa legítima. Sugerido tratar como base fria após 12 a 24 meses sem interação, com expurgo.
4. **Opt-out fácil e honrado** (ver Questão 6). É a contrapartida que legitima o legítimo interesse.
5. **Papéis claros:** o cliente detentor da base é o CONTROLADOR (decide finalidade e meios). A SEAL é a OPERADORA. Isso exige DPA (ver Questão 7). A base legal é escolha e responsabilidade do controlador, e o contrato deve dizer isso.

---

## Questão 4, Voz como dado

**Veredito: 🟡 CONDICIONAL. 🟢 para voz sintética de biblioteca, 🔴 para clonar voz de pessoa real sem termo assinado.**

### Fundamento, três vozes diferentes no fluxo

**1. A voz do lead, gravada.** É dado pessoal. Enquanto for apenas gravação e transcrição, é dado pessoal COMUM, não sensível. Ela só vira dado sensível (LGPD art. 5, II e art. 11) se houver biometria de voz, isto é, uso da voz para identificar ou autenticar unicamente a pessoa. A ANPD trata biometria vocal como dado sensível pela capacidade identificadora e pela impossibilidade prática de "trocar" a característica biométrica. Recomendação: NÃO fazer biometria de voz. Tratar a gravação como dado comum, com transparência (Questão 2) e minimização (Questão 7).

**2. A voz sintética da Carol, de biblioteca.** Se for uma voz de catálogo do ElevenLabs, sem corresponder a uma pessoa real identificável, não há direito de personalidade de terceiro envolvido. É a opção segura e a recomendada como default. Atenção apenas para a licença de uso comercial da voz junto ao provedor.

**3. A voz clonada de uma pessoa real.** Aqui mora o risco alto. Clonar a voz do cliente, de um vendedor, de um palestrante ou do Rodrigo Noll, ou de qualquer terceiro, exige autorização do titular da voz:

- **Código Civil, art. 20 e direitos de personalidade:** a voz é atributo da personalidade, protegida contra uso não autorizado. Reproduzir a voz de alguém sem consentimento é ilícito civil, com risco de indenização.
- **LGPD:** a voz clonada, como identificador vinculável a uma pessoa, atrai tratamento cuidadoso e, conforme o uso, sensível. Conduta segura: tratar como sensível e colher consentimento específico, informado, destacado e revogável (art. 8, parágrafo 5, revogação tão fácil quanto a concessão).
- **PL 2338 e deepfake:** a tendência regulatória é exigir rotulagem de conteúdo sintético que imite pessoa real. Clonar voz para se passar por alguém sem aviso caminha para o território de deepfake vedado.

### Recomendação prática

1. **Default de produto: voz sintética de biblioteca**, sem clonagem. Resolve a Questão 4 na origem.
2. **Se o cliente pedir clonagem da própria voz ou de um vendedor**, condicionar a ativação a termo escrito, específico, informado e revogável do TITULAR da voz (não do cliente em nome dele), com finalidade única (áudios da Carol para aquele cliente), escopo restrito ao tenant, vedação de cessão, revogabilidade expressa e regra de desligamento (se a pessoa sair, a voz é despublicada e eliminada). Sem termo assinado, o add-on não pode ser ativado.
3. **Jamais clonar voz de terceiro que não assinou** (celebridade, o Rodrigo Noll, um influenciador), ainda que "só para teste". É ilícito civil e risco reputacional grave.

---

## Questão 5, Anatel e regras de telemarketing 2025-2026

**Veredito: 🟡 ATENÇÃO, cumprir as 4 frentes (0303, Origem Verificada, Não Me Perturbe, horário).**

### Fundamento, o que mudou e o que vale hoje

**Prefixo 0303:** em agosto de 2025 a Anatel REVOGOU a obrigatoriedade do uso do prefixo 0303 em chamadas de telemarketing ativo. O uso passou a ser facultativo. A justificativa foi a estigmatização do prefixo, que fazia o consumidor rejeitar automaticamente qualquer chamada 0303. Ou seja, a SEAL não é obrigada a usar 0303, e usá-lo pode até reduzir atendimento.

**Origem Verificada (STIR/SHAKEN):** a Resolução Anatel 777, de 28/05/2025, tornou obrigatória a autenticação de chamadas, com regime vigente de 01/11/2025 a 31/10/2028. Empresas que fazem mais de 500 mil chamadas por mês já precisam autenticar para continuar operando. Originadores menores têm prazo de adaptação (cronograma até 2028). Quando a chamada é autenticada, o destinatário pode ver nome, logotipo e motivo da chamada na tela, o que aumenta a taxa de atendimento. Em 2025 já foram cerca de 35 bilhões de chamadas autenticadas. Para a SEAL, no início o volume tende a ficar abaixo do gatilho de 500 mil por mês, mas a recomendação estratégica é aderir à Origem Verificada assim que a operadora de saída oferecer, porque converte melhor e blinda o produto.

**Teto de robocall:** empresas que disparam mais de 100 mil chamadas abusivas por dia são bloqueadas. A operação da Carol deve ficar muito longe disso e evitar padrão de discagem massiva automatizada que caracterize abuso.

**Lista Não Me Perturbe (naomeperturbe.com.br):** desde 2025 a adesão é obrigatória para prestadoras e para o setor financeiro, e nem prestadoras de pequeno porte podem oferecer telemarketing para quem está na lista. O bloqueio vale em até 30 dias corridos do registro. Há exceções para confirmação de dados, prevenção a fraude, cobrança e portabilidade, que não é o caso da Carol (que faz oferta e agendamento). Logo, a Carol precisa suprimir da discagem qualquer número inscrito na lista.

**Janela de horário:** não há uma única lei federal de telemarketing ativo fixando horas, é um mosaico de leis estaduais, atuação do Procon e autorregulação, com a regra geral de respeitar horário comercial. As referências mais comuns de mercado: segunda a sexta das 9h às 21h, sábado em janela reduzida (por exemplo 10h às 16h, com estados mais restritivos limitando a 13h), e nunca aos domingos e feriados. Ao menos 9 estados, incluindo São Paulo, Rio de Janeiro e Distrito Federal, mantêm cadastros próprios de bloqueio, com a lei paulista sendo a mais restritiva. [A CONFIRMAR COM ADVOGADO] a janela exata por estado do público-alvo.

### Recomendação prática

1. **Não usar 0303** (facultativo e contraproducente), e planejar adesão à Origem Verificada com a operadora de saída, exibindo nome e motivo do contato.
2. **Scrub obrigatório contra a lista Não Me Perturbe** antes de cada lote de discagem, com atualização periódica da base de supressão.
3. **Janela de horário conservadora embarcada no discador**, ajustada ao fuso do DDD do lead: sugerido segunda a sexta das 9h às 20h, sábado das 10h às 13h, nunca domingos e feriados nacionais. Deixar configurável para o estado mais restritivo do público.
4. **Anti abuso:** limitar tentativas por lead (por exemplo, no máximo 3 tentativas com intervalo mínimo entre elas), evitar rediscagem imediata e nunca operar como robocall massivo.
5. **Rastreabilidade:** manter logs de horário, número, resultado e supressões, para responder a fiscalização.

---

## Questão 6, Opt-out em tempo real e supressão da base

**Veredito: 🔴 BLOQUEANTE se ausente, 🟢 com detecção em tempo real e supressão global.**

### Fundamento

- **LGPD, art. 18 e art. 18, parágrafo 2 (direito de oposição):** o titular pode se opor ao tratamento fundado em legítimo interesse. Ignorar o "não me ligue" é violação direta.
- **LGPD, art. 8, parágrafo 5:** revogação do consentimento tão fácil quanto a concessão. Aplicável quando a base for consentimento.
- **CDC, art. 39, IV e art. 42:** insistir em contato após recusa é prática abusiva e caminha para assédio de consumo.
- **Não Me Perturbe e leis estaduais:** reforçam o dever de parar e não recontatar.

O ponto crítico do produto de VOZ é que o opt-out precisa ser reconhecido DENTRO da conversa, em linguagem natural, não só por um formulário depois. O lead vai dizer "não tenho interesse", "não me liga mais", "tira meu número", "para de ligar". A Carol tem que entender a intenção na hora.

### Recomendação prática

1. **Detecção de intenção de opt-out em tempo real** no Retell, com uma lista ampla de gatilhos de linguagem natural (não apenas frases exatas). Ao detectar, a Carol confirma ("entendi, vou remover o seu número dos nossos contatos"), encerra com cordialidade e dispara o evento de supressão.
2. **Supressão GLOBAL, não por campanha.** O n8n deve gravar o lead numa do-not-call interna que bloqueia TODAS as sequências e campanhas presentes e futuras, não só a atual. Este é o erro clássico que gera reclamação: sair de uma lista e continuar recebendo de outra.
3. **Persistência e prova.** Registrar timestamp do opt-out, canal e a frase que o disparou, como evidência de cumprimento.
4. **Efeito imediato.** A supressão vale a partir do próximo ciclo de discagem, sem janela de "ainda estava na fila". Se houver chamadas já agendadas, cancelá-las.
5. **Respeitar opt-out vindo de fora da chamada** (WhatsApp, e-mail, pedido ao cliente) e sincronizar na mesma do-not-call.

---

## Questão 7, Retenção e segurança dos dados de voz e transcrição

**Veredito: 🟡 CONDICIONAL, exige DPA, minimização, retenção definida e tratamento de transferência internacional.**

### Fundamento

- **LGPD, art. 6, III (minimização) e art. 6, I (finalidade):** coletar e reter só o necessário para qualificar e agendar. Não guardar áudio nem transcrição além do preciso.
- **LGPD, art. 15 e art. 16 (término do tratamento):** os dados devem ser eliminados ao fim da finalidade, salvo hipóteses legais de guarda. Retenção proporcional ao propósito.
- **LGPD, art. 46 a art. 49 (segurança):** medidas técnicas e administrativas, criptografia, controle de acesso, logs.
- **LGPD, art. 33 a art. 36 (transferência internacional):** Retell e ElevenLabs são provedores com infraestrutura tipicamente fora do Brasil. O tráfego de voz e transcrição para o exterior é transferência internacional e precisa de base adequada (cláusulas contratuais, garantias). Ponto de atenção concreto da stack.
- **Papéis e suboperadores:** SEAL operadora, cliente controlador, e Retell, ElevenLabs e a hospedagem do n8n são SUBOPERADORES. Isso exige DPA na cadeia inteira.

### Recomendação prática

1. **DPA operador-controlador por cliente**, listando os suboperadores (Retell, ElevenLabs, provedor de hospedagem do n8n) e as respectivas garantias.
2. **Retenção conservadora com expurgo automático.** Definir prazos, por exemplo: áudio bruto por período curto (dias a poucas semanas), transcrição estruturada pelo tempo do relacionamento comercial, com expurgo de leads não convertidos após janela definida. Documentar.
3. **Redação (masking) de PII na transcrição.** Mascarar CPF, dados financeiros e qualquer dado sensível que o lead eventualmente verbalize, antes de armazenar ou trafegar para downstream. A Carol não deve pedir dado sensível.
4. **Acesso restrito e criptografia.** Least privilege, criptografia em trânsito e em repouso, logs de acesso.
5. **Transferência internacional endereçada** no DPA e nos termos com os provedores. Preferir configurações de retenção mínima ou opt-out de treinamento de modelo nos provedores, quando disponível.
6. **Direitos do titular operacionalizados:** canal para acesso, correção e eliminação, e o opt-out da Questão 6.
7. **DPIA (art. 38)** recomendável, dado o tratamento sistemático de voz com IA em escala, ainda que o volume inicial seja pequeno.

---

## Questão 8, CDC, preço e ofertas ditas pela Carol

**Veredito: 🟡 CONDICIONAL, tudo que a Carol afirma vincula, então ela só fala o que vem da fonte de verdade.**

### Fundamento

- **CDC, art. 30:** toda informação suficientemente precisa veiculada obriga o fornecedor e integra o contrato. Preço, condição de pagamento, bônus, garantia, data e vaga ditos pela Carol VINCULAM o cliente controlador.
- **CDC, art. 31:** a informação deve ser clara, correta e não enganosa.
- **CDC, art. 35:** se o fornecedor recusar cumprir a oferta, o consumidor pode exigir cumprimento, aceitar equivalente ou rescindir com restituição e perdas e danos.
- **CDC, art. 37, parágrafo 1 e parágrafo 2:** escassez e urgência fabricadas ("só tenho 2 vagas hoje", quando é texto fixo) são publicidade enganosa e abusiva. Só é lícita a escassez verdadeira, puxada do estado real de vagas e agenda.

### Recomendação prática

1. **Preço e condições só da fonte de verdade (RAG).** A Carol jamais improvisa valor, desconto, parcelamento, bônus, garantia ou prazo. Só repete o que está cadastrado e vigente. Guardrail duro contra alucinação de número.
2. **Nada de promessa de resultado.** Como é imersão de vendas, a Carol não pode prometer faturamento, retorno garantido ou resultado ("você vai fechar X", "garante seu retorno"). Isso é enganoso e gera responsabilidade.
3. **Escassez só se real.** Vaga e horário de reunião vêm da agenda e do estoque real de vagas. Proibir escassez fixa de roteiro.
4. **Agendamento verdadeiro.** Se a Carol marca reunião, o slot precisa existir de fato na agenda, senão é oferta descumprida (art. 30 e art. 35).
5. **Responsabilidade pela informação cadastrada é do cliente controlador**, e o contrato SEAL-cliente deve atribuir a ele o dever de manter preço, condições, vagas e agenda exatos e atualizados.

---

## INVARIANTES DO PRODUTO (regras duras que a Carol e o fluxo devem embarcar)

Estas são travas de produto, não preferências de tom. Devem viver no núcleo do fluxo (n8n + prompt do Retell), não relaxáveis por configuração do cliente.

1. **Abertura em 3 elementos, na primeira fala:** a Carol (a) se identifica como assistente virtual (IA) do cliente, (b) avisa que a ligação é gravada e para quê, (c) diz o motivo do contato (você se aplicou à imersão). Ordem antes de coletar qualquer resposta relevante.
2. **Nunca negar ser IA, nunca afirmar ser humana.** Se perguntada, responde a verdade com naturalidade. Trava inegociável, não desativável nem a pedido do cliente.
3. **Handoff humano disponível:** se o lead pedir para falar com uma pessoa, a Carol registra ou transfere, não insiste em fingir autonomia.
4. **Só discar para lead com opt-in comprovado** (aplicou-se ao evento), com prova de origem do dado registrada. Nada de lista fria comprada.
5. **Gate de compliance de discagem ANTES de ligar:** suprimir contra a lista Não Me Perturbe e contra a do-not-call interna, respeitar a janela de horário por fuso do DDD (default seg a sex 9h às 20h, sáb 10h às 13h, nunca domingo e feriado), e limitar tentativas por lead (sugerido no máximo 3, com intervalo).
6. **Opt-out em tempo real com supressão global:** ao detectar intenção de "não me ligue", confirmar, encerrar e remover o lead de TODAS as sequências e campanhas, com timestamp registrado.
7. **Preço, condição, bônus, garantia, prazo e vaga só da fonte de verdade cadastrada (RAG).** Zero improviso de número. Zero promessa de resultado. Escassez só se real, puxada de agenda e estoque de vagas.
8. **Voz sintética de biblioteca por default.** Clonagem de voz de pessoa real só com termo escrito, específico e revogável do titular da voz. Jamais clonar voz de terceiro que não assinou.
9. **Não coletar dado sensível.** A Carol não pergunta saúde, dado financeiro nem documento. PII eventualmente verbalizada (CPF, cartão) é mascarada na transcrição antes de armazenar.
10. **Retenção definida com expurgo automático, acesso restrito e criptografia**, e transferência internacional endereçada em DPA com Retell, ElevenLabs e hospedagem do n8n.
11. **Log completo por chamada:** horário, número, fuso, resultado, aviso de gravação dado, base legal aplicada, opt-out se houve, versão do script. É a prova de conformidade.
12. **Não operar como robocall massivo.** Padrão de discagem controlado, muito abaixo dos tetos da Anatel, sem rediscagem agressiva.

---

## Kit mínimo de artefatos contratuais (a construir antes do primeiro cliente pagante)

1. **Contrato de prestação de serviço (SDR de voz gerenciado).** Objeto, preço, responsabilidades do cliente (exatidão de preço, vagas e agenda, e escolha da base legal), ausência de garantia de resultado comercial, limitação de responsabilidade, foro. Coerente com o objeto social da SEAL (nomear a NF como automação de processo comercial).
2. **DPA operador-controlador**, com suboperadores (Retell, ElevenLabs, hospedagem do n8n), finalidade, retenção, segurança, transferência internacional, direitos do titular, devolução e eliminação ao término.
3. **Registro da LIA (teste de balanceamento)** para o legítimo interesse do outbound, versionado.
4. **Termo de consentimento de clonagem de voz** (se o add-on for ativado), assinado pelo titular da voz.
5. **Política de disclosure de IA e de gravação:** frase de abertura padrão, default ligado, trava "nunca negar ser IA", e aceite de risco do cliente se pedir posicionamento diferente.
6. **Política de retenção, privacidade e opt-out:** prazos, masking de PII, fluxo de supressão global e de exercício de direitos, integração com a lista Não Me Perturbe.

---

## Fontes normativas e de verificação

- Lei 13.709/2018 (LGPD), art. 5, 6, 7, 8, 9, 15, 16, 18, 33 a 36, 38, 46 a 49.
- Lei 8.078/1990 (CDC), art. 6, 30, 31, 35, 37, 39, 42.
- Código Civil, art. 20 (direitos de personalidade, voz).
- Resolução Anatel 777, de 28/05/2025 (Origem Verificada / autenticação de chamadas, STIR/SHAKEN), regime de 01/11/2025 a 31/10/2028, autenticação obrigatória para originadores acima de 500 mil chamadas por mês. Ver [Correio Braziliense](https://www.correiobraziliense.com.br/economia/2025/10/7276745-anatel-torna-obrigatoria-a-autenticacao-de-chamadas-telefonicas-ate-2028.html), [Mobile Time](https://www.mobiletime.com.br/noticias/13/03/2026/origem-verificada-2025/), [TELETIME](https://teletime.com.br/16/12/2025/regras-anatel-chamadas-massa/).
- Revogação da obrigatoriedade do prefixo 0303 (agosto de 2025), uso facultativo. Ver [Agência Brasil](https://agenciabrasil.ebc.com.br/geral/noticia/2025-08/anatel-revoga-obrigatoriedade-do-uso-do-prefixo-0303-em-ligacoes).
- Lista Não Me Perturbe (naomeperturbe.com.br), adesão obrigatória de prestadoras e setor financeiro em 2025, bloqueio em até 30 dias. Ver [InfoFinanceira](https://infofinanceira.com.br/noticias/2025/09/05/anatel-obriga-operadoras-e-bancos-no-nao-me-perturbe-novas-regras-entram-em-vigor/), [Agência Brasil / Radioagência](https://agenciabrasil.ebc.com.br/radioagencia-nacional/geral/audio/2025-09/anatel-determina-adesao-de-empresas-de-telemarketing-ao-nao-perturbe).
- Janela de horário de telemarketing (mosaico de leis estaduais e Procon, regra geral de horário comercial, veto a domingos e feriados). Ver [Portal Contra Ponto](https://portalcontraponto.com.br/capa/horarios-permitidos-ligacoes-cobranca-telemarketing-por-estado-e-ddd/), [Procon-ES](https://procon.es.gov.br/Not%C3%ADcia/anatel-implementa-novas-regras-para-combater-chamadas-abusivas).
- LGPD e voz como dado, biometria vocal como dado sensível, exigência de consentimento específico e destacado para biometria (ANPD, Nota Técnica 5/2025 e agenda regulatória 2025-2026). Ver [ConfiData](https://confidata.com.br/blog/lgpd-dados-biometricos-reconhecimento-facial-digital), [BeCompliance](https://becompliance.com/lgpd-e-o-uso-de-imagem-e-voz-sao-dados-pessoais-sensiveis-entenda-o-risco/).
- Legítimo interesse na prospecção B2B, orientações da ANPD (identificação clara no primeiro contato, opt-out imediato, registro de origem e retenção proporcional 12 a 24 meses). Ver [LeadCNPJ](https://leadcnpj.com.br/blog/lgpd-na-prospeccao-b2b/), [Neoway](https://blog.neoway.com.br/lgpd-na-prospeccao/).
- PL 2338/2023 (marco legal de IA): aprovado no Senado em 10/12/2024, votação final na Câmara prevista para 2026, modelo europeu baseado em risco, direito de saber que interage com IA, multas de até R$ 50 milhões, sem sanção até 20/07/2026. Ver [Exame](https://exame.com/inteligencia-artificial/marco-legal-da-inteligencia-artificial-pl-2338-o-que-muda-para-empresas-com-a-nova-lei/), [Senado, PL 2338/2023](https://www25.senado.leg.br/web/atividade/materias/-/materia/157233).
- STF, licitude da gravação de conversa por um dos interlocutores (tema de repercussão geral), como pano de fundo do plano probatório da Questão 2.

*Parecer emitido pelo Squad Legal (AIOX), 2026-07-20. Análise técnico-jurídica interna de apoio à decisão. Não substitui advogado constituído para os contratos e termos finais.*
