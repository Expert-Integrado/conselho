---
name: conselho
description: "Use quando o usuário traz uma decisão genuína com algo em jogo, múltiplas opções, ou um artefato (código, copy, arquitetura, plano) que merece pressão de vários ângulos. CUSTO: cada convocação dispara 6 sub-agentes — no plano Pro do Claude Code dá pra fazer 4-10 antes de bater rate limit. GATILHOS OBRIGATÓRIOS (PT-BR): 'convoca o conselho', 'roda o conselho', 'leva pro conselho', 'submete ao conselho', 'debate isto', 'revisa em conselho', 'conselho desse código'. GATILHOS OBRIGATÓRIOS (inglês): 'council this', 'run the council', 'debate this'. GATILHOS FORTES (só com decisão/tradeoff real): 'devo fazer X ou Y', 'qual opção', 'o que você faria', 'é a jogada certa', 'me dá várias perspectivas', 'não consigo decidir', 'estou dividido entre', 'revisa esse código', 'problema nessa arquitetura', 'should I X or Y', 'which option', 'what would you do'. NÃO dispare em pedidos casuais de baixo risco ('valida esse título', 'tá bom esse texto?') — vira overhead."
allowed-tools: Task, Glob, Grep, Read, Write, Bash
---

# Conselho de LLMs

Passa uma pergunta, decisão, código ou artefato por 5 conselheiros com estilos de pensamento distintos (rodando em paralelo como sub-agentes reais) e um presidente que sintetiza tudo num veredito acionável: onde há convergência (alta confiança), onde há choque (a decisão real), pontos cegos do grupo e a única coisa pra fazer primeiro. Serve pra quebrar a primeira resposta plausível da IA — use quando errar custa caro.

## NUNCA

- NUNCA simule as 5 vozes inline na sua própria cabeça. Dispare sub-agentes REAIS via `Task` — encenar as 5 perspectivas na mesma cabeça anula o mecanismo anti-viés e não vale nada.
- NUNCA convoque os conselheiros em sequência. As 5 chamadas `Task` vão numa ÚNICA mensagem (mesmo bloco) pra rodarem em paralelo; sequencial vaza resposta anterior pras posteriores.
- NUNCA gere relatório HTML nem nenhum arquivo pro veredito — apresente direto no chat em markdown (exceção: transcrição opcional do passo 5).
- NUNCA convoque o conselho pra pergunta trivial: factual com resposta certa, lookup técnico, tarefa de criação ("escreve um tweet"), decisão de baixo risco ("devo usar markdown"). Responda direto. **Precedência:** se o usuário usou uma frase-gatilho OBRIGATÓRIA junto com conteúdo trivial, siga a regra de "Precedência" em "Quando disparar" (comando explícito vence; só faz UMA pergunta antes de convocar quando o conteúdo é claramente trivial).
- NUNCA adicione sua opinião nem direcione ao enquadrar a pergunta — enquadre neutro.
- NUNCA re-adicione o passo de peer review entre conselheiros. Foi removido DE PROPÓSITO (ver Notas de design).
- NUNCA salve transcrição sem o usuário pedir ou sem a pergunta bater o critério de "significativa" do Passo 5 (passo 5 é opcional). Na dúvida — nenhum critério do Passo 5 dispara de forma inequívoca — NÃO salve.

## SEMPRE

- SEMPRE 5 conselheiros em paralelo + 1 presidente (6 sub-agentes no total).
- SEMPRE escaneie o "workspace" (= o cwd da sessão) por contexto ANTES de enquadrar a pergunta, respeitando o teto de esforço do Passo 1A (~5 chamadas de descoberta, 3-4 arquivos lidos).
- SEMPRE cada conselheiro produz 150-300 palavras.
- SEMPRE o presidente PODE discordar da maioria se o raciocínio do dissidente for mais forte — e explica por quê.
- SEMPRE apresente o veredito escaneável (bullets); pra código, inclua diff antes/depois.
- Se a pergunta for vaga demais → faça UMA única pergunta de esclarecimento, depois prossiga.
- SEMPRE texto em PT-BR com acentuação correta.

## Quando disparar (e quando NÃO)

O conselho é pra perguntas em que estar errado é caro.

**Bons casos pra levar ao conselho:**

- "Devo lançar X ou Y?" (decisão estratégica com tradeoff real)
- "Esse código tá bom? Algum problema nessa arquitetura?" (revisão técnica antes de merge)
- "Qual desses 3 ângulos de posicionamento é mais forte?"
- "Estou pensando em pivotar de X pra Y. Tô louco?"
- "Aqui está a copy. O que está fraco?"
- "Devo refatorar isso agora ou depois?"
- "Esse PRD tá completo? O que falta?"
- "Esse contrato tem armadilha?"
- **Pós-mortem:** "Tomei a decisão X há 3 meses, deu errado. O que eu não vi?" (aprendizado retroativo, não só decisão prospectiva)

**Casos ruins (NÃO convocar — responda direto):**

- "Qual é a capital da França?" (uma resposta certa, não precisa de várias perspectivas)
- "Escreve um tweet pra mim" (tarefa de criação, não decisão)
- "Resume esse artigo" (tarefa de processamento, não julgamento)
- "Qual o nome da função do React pra usar estado?" (lookup, não decisão)

O conselho brilha quando há incerteza genuína e o custo de uma escolha errada é alto. Se você já sabe a resposta e só quer validação, o conselho provavelmente vai te dizer coisas que você não quer ouvir. Esse é o ponto.

### Precedência: gatilho OBRIGATÓRIO vs. conteúdo trivial

Duas regras deste arquivo podem colidir: a `description` marca certas frases como GATILHOS OBRIGATÓRIOS, e o `## NUNCA` proíbe convocar pra pergunta trivial. Se o usuário juntar uma frase obrigatória com um conteúdo trivial, resolva NESTA ordem (é uma árvore de decisão, não julgamento):

1. **O usuário digitou LITERALMENTE uma das frases de GATILHOS OBRIGATÓRIOS da `description`?** (PT-BR: "convoca o conselho", "roda o conselho", "leva pro conselho", "submete ao conselho", "debate isto", "revisa em conselho", "conselho desse código"; inglês: "council this", "run the council", "debate this".) → é **comando explícito** e VENCE a supressão de trivial. Vá pro Passo 1 e convoque.
   - **Única exceção:** se o conteúdo depois do gatilho for inequivocamente um dos "Casos ruins" listados acima (resposta factual única, lookup técnico, tarefa de criação, decisão sem downside), NÃO queime 6 sub-agentes no escuro — faça **UMA única pergunta**: "isso parece [factual / lookup / baixo risco] — quer o conselho completo mesmo, ou respondo direto?". Confirmou o conselho → convoque (Passo 1). Pediu direto → responda direto e não convoque. (É o mesmo mecanismo da pergunta única do Passo 1 — não é um passo novo.)
2. **NÃO há frase obrigatória, só um GATILHO FORTE** (ex.: "qual opção", "o que você faria", "should I X or Y")? → aplique o filtro de trivial por inteiro: conteúdo trivial (bate um "Caso ruim") → responda direto e NÃO convoque; conteúdo com decisão/tradeoff real → convoque.

Regra de bolso: **frase obrigatória = intenção explícita → o default é convocar** (com a pergunta única só no caso raro de conteúdo claramente trivial); **gatilho forte = intenção inferida → o trivial suprime.**

## Pré-requisitos

- **Claude Code** (não claude.ai web/celular) com suporte a sub-agentes (`Task`). A skill dispara 6 sub-agentes por convocação.
- Tools usadas: `Task` (conselheiros + presidente), `Glob` + `Grep` + `Read` (escanear contexto), `Write` (só pra transcrição opcional do passo 5), `Bash` (uso único: gerar o timestamp da transcrição no Passo 5 — nenhum outro uso).
- **Custo/plano** (cada convocação = 6 sub-agentes): Free ~1-2 convocações antes de rate limit; Pro ~4-10; Max sem preocupação prática.

## Os 5 conselheiros

Cada conselheiro pensa a partir de um ângulo diferente. Não são cargos nem personas. São estilos de pensamento que naturalmente criam tensão entre si. Os mesmos 5 funcionam pra decisão de produto, revisão de código, escolha de arquitetura, análise de copy — o framework é genérico de propósito. As descrições abaixo são o payload que vai LITERAL pra cada sub-agente no passo 2.

### 1. O Contrário

Procura ativamente o que está errado, o que está faltando, o que vai falhar. Assume que a ideia (ou o código) tem uma falha fatal e tenta encontrá-la. Se tudo parece sólido, ele cava mais fundo. O Contrário não é um pessimista. É o amigo que te salva de um mau negócio (ou de um bug em produção) fazendo as perguntas que você está evitando.

**Em código:** procura race conditions, edge cases não cobertos, vetores de injeção, falhas silenciosas, dependências frágeis.

### 2. O Pergunta-Por-Quê

Ignora a pergunta de superfície e pergunta "o que estamos realmente tentando resolver aqui?". Tira as suposições. Reconstrói o problema do zero. Às vezes o output mais valioso do conselho é o Pergunta-Por-Quê dizendo "você está fazendo a pergunta errada por completo."

**Em código:** questiona a abstração. "Por que essa classe existe? Resolve um problema real ou é cerimônia?". Reconstrói o módulo no nível mais simples possível.

### 3. O Expansionista

Procura o upside que todo mundo está perdendo. O que poderia ser maior? Que oportunidade adjacente está escondida? O que está sendo subvalorizado? O Expansionista não liga pro risco (esse é o trabalho do Contrário). Ele liga pro que acontece se isso funcionar até melhor do que o esperado.

**Em código:** vê a oportunidade de generalizar. "Esse padrão poderia virar um util reutilizável. Esse hook resolve mais 3 casos no codebase."

### 4. O Forasteiro

Não tem contexto nenhum sobre você, sua área, ou sua história. Responde puramente ao que está na frente dele. Este é o conselheiro mais subestimado. Especialistas desenvolvem pontos cegos. O Forasteiro pega a maldição do conhecimento: coisas que são óbvias pra você mas confusas pra todo mundo.

**Em código:** lê como se fosse um dev novo no projeto. "Esse nome de variável não me diz nada. Esse fluxo tem 4 saltos pra entender — daria pra explicar de forma linear?". Pega problemas de manutenibilidade que o autor não vê.

### 5. O Executor

Só liga pra uma coisa: isso pode ser feito de fato, e qual é o caminho mais rápido pra fazer? Ignora teoria, estratégia e pensamento de panorama. O Executor olha cada ideia pela lente de "OK, mas o que você faz na segunda-feira de manhã?". Se uma ideia parece brilhante mas não tem um primeiro passo claro, o Executor vai dizer.

**Em código:** quer enviar incremental. "Esse PR tá grande demais — quebra em 3. Esse refactor pode esperar, primeiro faz funcionar."

**Por que esses cinco:** Eles criam três tensões naturais. Contrário vs Expansionista (downside vs upside). Pergunta-Por-Quê vs Executor (repensar tudo vs simplesmente fazer). O Forasteiro fica no meio mantendo todo mundo honesto, vendo o que olhos novos veem. As tensões são o produto — não as respostas individuais.

## Passos

### Passo 1 — Escanear contexto + enquadrar a pergunta

**Pré-condição:** gatilho reconhecido e o caso passou pela regra de "Quando disparar" — incluindo a "Precedência: gatilho OBRIGATÓRIO vs. conteúdo trivial" (comando explícito convoca; gatilho forte + conteúdo trivial não convoca).

**A. Escaneie o "workspace" por contexto.** Aqui "workspace" = o **diretório de trabalho atual da sessão (o cwd)** — o diretório onde `Glob`/`Grep`/`Read` buscam por padrão quando você NÃO passa um path explícito. Não é "a raiz do repo git", não é "a pasta do projeto aberto no editor": é literalmente o cwd da sessão. A pergunta do usuário geralmente é só a ponta do iceberg. Use `Glob`/`Grep`/`Read` pra achar os 2-3 arquivos que dariam aos conselheiros contexto pra dar conselho específico em vez de genérico:

- `CLAUDE.md` ou `claude.md` no cwd (contexto do negócio, preferências, restrições)
- Qualquer pasta `memory/` sob o cwd (perfis, decisões passadas, contexto de domínio)
- Quaisquer arquivos que o usuário referenciou explicitamente ou anexou nesta conversa
- **Para revisão de código:** o arquivo em questão + arquivos que o importam ou são importados (callers, dependências — use `Grep` pra achar callers) + testes correspondentes
- Transcrições recentes de conselho no cwd (`council-transcript-*.md`) pra evitar reconvocar o mesmo terreno. **Se encontrar uma sobre o mesmo tema:** faça `Read` nela e extraia 1-2 linhas resumindo o veredito anterior (a "Recomendação" e a "Única Coisa a Fazer Primeiro"). Inclua esse resumo no enquadramento (Passo 1B) como contexto — para os conselheiros saberem o que já foi decidido, NÃO pra repetir. A convocação NÃO é pulada: os 5 conselheiros + presidente são convocados de novo normalmente, agora cientes do terreno já coberto.

**Teto de esforço (verificável — não é cronômetro).** O "≤30s" é um limite de esforço, não um tempo de relógio que você consegue medir. Traduza em contagem observável: no máximo **~5 chamadas de descoberta** (`Glob` + `Grep` + `Read` somadas) e no máximo **3-4 arquivos lidos**. Atingiu qualquer um dos dois limites, OU já achou os 2-3 arquivos úteis → PARE de escanear e siga com o que tem. Nenhum arquivo de contexto encontrado → siga só com a mensagem do usuário (não bloqueie por falta de contexto).

**B. Enquadre a pergunta.** Pegue a pergunta crua do usuário E o contexto enriquecido e reformule como um prompt claro e neutro que todos os 5 conselheiros vão receber. A pergunta enquadrada deve incluir os 4 componentes:

1. A decisão, pergunta ou artefato central (se for código, inclua o trecho relevante)
2. Contexto-chave da mensagem do usuário
3. Contexto-chave dos arquivos do workspace (estágio do projeto, restrições, dependências, resultados passados)
4. O que está em jogo (por que esta decisão importa)

Não adicione sua opinião. Não direcione. Mas GARANTA que cada conselheiro tem contexto suficiente.

**Validação:** a pergunta enquadrada tem os 4 componentes e está neutra.

**Se falhar (pergunta vaga demais, ex: "convoca o conselho: meu negócio"):** faça UMA única pergunta de esclarecimento, depois prossiga. Só uma.

Guarde a pergunta enquadrada — ela é reusada nos passos 2, 3 e na transcrição.

### Passo 2 — Convocar o conselho (5 sub-agentes em paralelo)

Dispare os 5 conselheiros como sub-agentes REAIS via `Task`, em UMA única mensagem (5 chamadas `Task` no mesmo bloco) pra rodarem em paralelo. Cada uma das 5 chamadas usa `subagent_type="general-purpose"` — o tipo genérico do Claude Code, que dá a cada conselheiro um contexto limpo e independente. NÃO simule as 5 vozes inline você mesmo — sub-agentes de verdade são o que garante independência e quebra o viés de confirmação; encenar as 5 perspectivas na mesma cabeça anula o mecanismo. Cada sub-agente recebe:

1. Sua identidade de conselheiro e estilo de pensamento (da seção "Os 5 conselheiros", literal)
2. A pergunta enquadrada
3. Uma instrução clara: responda independentemente. Não hesite. Não tente ser equilibrado. Se incline totalmente pra perspectiva atribuída. Se enxergar uma falha fatal, fale. Se enxergar upside massivo, fale. Seu trabalho é representar seu ângulo da forma mais forte possível. A síntese vem depois.

Cada conselheiro deve produzir uma resposta de 150-300 palavras. Longa o suficiente pra ser substantiva, curta o suficiente pra ser escaneável.

**Template de prompt do sub-agente** (preencha `[Nome do Conselheiro]` e `[descrição do conselheiro]` com o conselheiro certo; a chamada `Task` correspondente leva `subagent_type="general-purpose"`):

```
Você é [Nome do Conselheiro] em um Conselho de LLMs.

Seu estilo de pensamento: [descrição do conselheiro acima]

Um usuário trouxe esta pergunta ao conselho:

---
[pergunta enquadrada]
---

Responda a partir da sua perspectiva. Seja direto e específico. Não hesite nem tente ser equilibrado. Incline-se totalmente pro ângulo atribuído. Os outros conselheiros vão cobrir os ângulos que você não está cobrindo.

Mantenha sua resposta entre 150-300 palavras. Sem preâmbulo. Vá direto pra análise.
```

**Validação:** 5 respostas retornaram, cada uma 150-300 palavras e inclinada ao seu ângulo.

**Se um sub-agente travar / não responder:** rode aquele `Task` de novo. Se persistir 2x seguidas, é instabilidade do Claude Code no momento — espere ~10min e tente de novo. NÃO simule inline a voz que faltou.

### Passo 3 — Síntese do presidente (1 sub-agente)

Convoque um único sub-agente como presidente via `Task`, também com `subagent_type="general-purpose"` (contexto limpo — ele só conhece as respostas dos conselheiros pelo que você passa no prompt abaixo). Ele recebe:

1. A pergunta enquadrada
2. As 5 respostas dos conselheiros (com nomes revelados)

**Template de prompt do presidente:**

```
Você é o Presidente de um Conselho de LLMs. Sua função é sintetizar as opiniões dos 5 conselheiros em um veredito final claro e útil.

A pergunta trazida ao conselho:

---
[pergunta enquadrada]
---

RESPOSTAS DOS CONSELHEIROS:

**O Contrário:**
[resposta]

**O Pergunta-Por-Quê:**
[resposta]

**O Expansionista:**
[resposta]

**O Forasteiro:**
[resposta]

**O Executor:**
[resposta]

Produza o veredito do conselho usando esta estrutura exata:

## Onde o Conselho Concorda
[Pontos em que múltiplos conselheiros convergiram independentemente. São sinais de alta confiança.]

## Onde o Conselho se Choca
[Discordâncias genuínas. Apresente os dois lados. Explique por que conselheiros razoáveis discordam.]

## Pontos Cegos
[Coisas que conselheiros individuais perderam e que outros sinalizaram. Ângulos importantes que apareceram em apenas 1-2 conselheiros.]

## A Recomendação
[Uma recomendação clara e direta. Não "depende". Uma resposta de verdade com raciocínio. O presidente PODE discordar da maioria se o raciocínio do dissidente for mais forte — explique por quê.]

## A Única Coisa a Fazer Primeiro
[Um único próximo passo concreto. Não uma lista. Uma coisa.]

Seja direto. Não hesite. O ponto inteiro do conselho é dar ao usuário uma clareza que ele não conseguiria de uma única perspectiva.
```

**Validação:** o veredito tem as 5 seções na estrutura exata.

### Passo 4 — Apresentar o veredito no chat

Depois que a síntese do presidente estiver completa, apresente o veredito completo diretamente no chat usando markdown. NÃO gere um relatório HTML nem nenhum arquivo. O usuário lê na conversa.

Formate o output assim:

```
## Veredito do Conselho: {tópico curto}

### Onde o Conselho Concorda
{conteúdo}

### Onde o Conselho se Choca
{conteúdo}

### Pontos Cegos
{conteúdo}

### A Recomendação
{conteúdo}

### A Única Coisa a Fazer Primeiro
{conteúdo}
```

Mantenha escaneável. Use bullet points. Inclua snippets de código com diff (antes/depois) quando o conselho for sobre código.

### Passo 5 — Salvar a transcrição (opcional)

**Condição (verificável — salve se QUALQUER uma das duas bater):**

1. **Pedido explícito:** o usuário usou uma palavra de salvar/registrar referida a ESTA convocação — "salva", "guarda", "registra a transcrição", "documenta isso", "quero isso escrito". → salve.
2. **Significativa o suficiente pra referenciar depois:** conta como significativa quando o veredito do presidente tem algo que será revisitado no tempo — precisa disparar **pelo menos UM** destes sinais concretos:
   - a "Única Coisa a Fazer Primeiro" ou a "Recomendação" define um checkpoint / limiar / prazo futuro (ex.: "se X pessoas pagarem em 7 dias, então Y");
   - a decisão é irreversível ou de alto custo (dinheiro, contratação, arquitetura cara de desfazer, compromisso público);
   - o usuário sinalizou que vai voltar a isso ("depois eu decido", "vou pensar", "me lembra disso").

**Default explícito (resolve a zona cinzenta):** se o usuário NÃO pediu (critério 1) E nenhum sinal do critério 2 disparou de forma inequívoca → **NÃO salve**. "Opcional" aqui significa que o default é não salvar; só salve quando um critério acima bate claramente. Não invente um motivo pra salvar.

**Ação (se a condição bater):** `Write` em `council-transcript-[timestamp].md`, onde:
- `[timestamp]` = data-hora no formato `AAAA-MM-DD-HHmm`, fuso America/Sao_Paulo. Gere lendo o relógio com uma única chamada `Bash`: `TZ='America/Sao_Paulo' date +%Y-%m-%d-%H%M` (nunca invente ou estime a hora — sempre leia do sistema). Ex.: `council-transcript-2026-07-05-1432.md`.
- **Diretório:** o mesmo "workspace" do Passo 1 — o **cwd da sessão**. Isso vale INCLUSIVE quando não há repo/projeto de código (ex.: decisão de negócio pura, sem pasta de projeto óbvia): o cwd sempre existe, então grave nele. Se o usuário indicou explicitamente outra pasta, use a que ele indicou.
- **Conteúdo:** a pergunta enquadrada (Passo 1) + as 5 respostas dos conselheiros (Passo 2) + o veredito completo do presidente (Passo 4), nesta ordem.

**Se nenhuma condição bater:** não salve nada (nem pergunte).

## Validação final (checklist)

- [ ] O gatilho era uma decisão/artefato real com algo em jogo (não trivial/lookup/criação).
- [ ] Contexto do workspace escaneado (≤30s) e pergunta enquadrada neutra com os 4 componentes.
- [ ] 5 conselheiros disparados em PARALELO via `Task` (não simulados inline), cada um 150-300 palavras.
- [ ] Presidente sintetizou com as 5 seções na estrutura exata.
- [ ] Veredito apresentado no chat em markdown — nenhum HTML/arquivo gerado.
- [ ] Transcrição salva SÓ se pedida OU significativa pelos critérios do Passo 5 (default = não salvar); se salva, em `council-transcript-AAAA-MM-DD-HHmm.md` no cwd.
- [ ] Todo texto em PT-BR com acentuação correta.

## Erros comuns e recovery

| Sintoma | O que fazer |
|---|---|
| Pergunta trivial/factual/lookup disparou o gatilho | Não convoque o conselho — responda direto. É overhead. |
| Pergunta vaga demais ("convoca o conselho: meu negócio") | UMA única pergunta de esclarecimento, depois prossiga. |
| Tentação de simular as 5 vozes inline pra "economizar" sub-agentes | NÃO. Dispare `Task` reais — inline anula o mecanismo anti-viés. |
| Um sub-agente travou / não respondeu | Rode aquele `Task` de novo. Se persistir 2x seguidas, espere ~10min (instabilidade) e tente de novo. |
| Conselheiro fora de 150-300 palavras, ou presidente sem as 5 seções na estrutura exata | 1 retry daquele `Task` com a instrução de formato reforçada (repita o limite de palavras / as 5 seções obrigatórias). Se persistir, use a resposta como está e sinalize o desvio na saída final (ex.: "conselheiro X veio com N palavras" / "presidente omitiu a seção Y"). Não trave a convocação por causa de formato. |
| Rate limit no meio da execução | Bateu o limite do plano. Aguarde a janela renovar (geralmente ~5h) ou suba pra plano Max. |
| Convocação sequencial (respostas vazando) | Reenvie as 5 chamadas `Task` numa única mensagem/bloco pra rodarem em paralelo. |

## Exemplo: convocando o conselho sobre uma decisão de produto

**Usuário:** "Convoca o conselho: tô em dúvida entre lançar um workshop pago de R$ 97 ou um aulão grátis pra construir lista. Minha audiência tem ~8K seguidores no Instagram, lista de e-mail de 1,2K. Objetivo: testar um novo produto de R$ 1.997. Qual jogada faz mais sentido?"

**O Contrário:** "Aulão grátis pra 8K seguidores parece de graça, mas custa caro. Você vai atrair curiosos que nunca compram, vai inflar lista com gente que abriu pelo isca, e a métrica de conversão pro produto de R$ 1.997 vai vir baixa porque o público não pagou nem R$ 97 pra entrar. Workshop pago filtra: quem paga R$ 97 demonstra disposição. Lista pequena de pagantes converte mais que lista grande de curiosos. O risco do grátis é parecer movimento sem virar receita."

**O Pergunta-Por-Quê:** "Você está validando o quê exatamente? Demanda pelo conteúdo, ou disposição a pagar? Se é demanda, o grátis serve melhor (mais volume = sinal mais limpo). Se é disposição a pagar, o workshop de R$ 97 é o teste real — só faz sentido lançar o R$ 1.997 se gente paga R$ 97. A pergunta certa não é 'qual lançar', é 'que sinal eu preciso ver agora pra autorizar o R$ 1.997?'. Sem isso definido, qualquer escolha vira teatro de validação."

**O Expansionista:** "Faz os dois — sequenciais. Aulão grátis primeiro pra puxar 800-1500 inscritos da audiência atual. Termina o aulão com oferta do workshop de R$ 97. Quem paga vira público pré-qualificado pro R$ 1.997 daqui a 60 dias. Funil de 3 níveis ataca curiosos, mornos e prontos no mesmo cohort. O caro é descartar 80% do funil indo direto pro pago — o grátis é a porta que multiplica o pipeline."

**O Forasteiro:** "Eu não te conheço. Vejo um anúncio de R$ 97 pra workshop seu — não compro porque não sei se você entrega valor. Vejo um aulão grátis com promessa concreta — entro porque é zero risco. Saio do aulão tendo experimentado seu jeito. Aí seu R$ 1.997 vira possível porque eu já consumi e gostei. A barreira do R$ 97 é alta pra alguém que ainda não tem prova. Lembra: você está visível pra 8K seguidores e tem lista de 1,2K, mas a fração que já consumiu seu conteúdo a fundo é menor — e é só essa fração que paga R$ 97 sem prova prévia."

**O Executor:** "Anuncia o aulão grátis pra semana que vem. 7 dias pra criar landing simples, sequência de 3 e-mails, conteúdo do aulão. Workshop pago precisa de mais infraestrutura (página de venda, recibo, suporte, garantia). Você ganha 30 dias entre validar o aulão e lançar o pago — usando o resultado do aulão como termômetro pra ajustar o pago. Se 8K seguidores virarem 1K inscritos no grátis, segue pro pago. Se virarem 200, o problema é audiência, não preço."

**Veredito do Presidente:**

*Onde o conselho concorda:* Sequenciar (grátis primeiro, pago depois) é a jogada com melhor relação informação/custo. 4 dos 5 chegaram nisso por caminhos diferentes.

*Onde o conselho se choca:* O Contrário diz que aulão grátis polui a lista. Os outros 4 dizem que poluição é tolerável quando a audiência ainda é fria. A divergência se resolve pelo *grau de aquecimento atual* — se a lista de 1,2K já comprou algo seu antes, vai direto pro pago. Se nunca comprou, aulão grátis primeiro.

*Pontos cegos:* O Pergunta-Por-Quê pegou o problema de método: ninguém definiu o critério de sucesso ANTES de escolher o formato. Sem critério ("se 200 pessoas pagarem R$ 97, eu lanço o R$ 1.997"), qualquer resultado vira interpretação.

*Recomendação:* Aulão grátis primeiro, depois workshop de R$ 97 pros que ficaram, depois R$ 1.997 pra quem fechou o R$ 97. MAS: antes de mexer em qualquer coisa, escreve em uma linha qual número (inscritos no grátis, conversão pro R$ 97, conversão pro R$ 1.997) autoriza ou cancela o próximo passo. Sem esse critério escrito, você vai racionalizar qualquer resultado.

*Única coisa a fazer primeiro:* Escrever em uma linha: "se ___ pessoas pagarem R$ 97 dentro de 7 dias após o aulão, lanço o R$ 1.997". Decidir o número antes de rodar a campanha. Sem isso, qualquer resultado vai parecer aceitável.

## Notas de design

- **Mesmo modelo, 5 lentes.** Os 5 conselheiros rodam o mesmo modelo (Claude) com prompts diferentes — a independência vem do prompt, não da arquitetura. O valor NÃO é "5 mentes votam", é **anti-viés-de-confirmação estruturado**: impede a IA de convergir prematuramente e força ver o problema por 5 ângulos que ela pularia. O presidente faz a síntese forçada — em vez de "depende", entrega decisão sob a tensão das 5 vozes.
- **Peer review omitido DE PROPÓSITO — não re-adicione.** A metodologia original (Andrej Karpathy, [github.com/karpathy/llm-council](https://github.com/karpathy/llm-council)) usa modelos diferentes (GPT, Claude, Gemini, Grok), onde peer review vale porque cada modelo tem viés distinto. Aqui é 1 modelo com 5 prompts: peer review entre eles é teatro epistêmico (compartilham o mesmo viés latente) e custa 5 sub-agentes a mais sem diversidade real.
- Use o conselho como ferramenta pra estressar uma decisão antes de cravar. Não é oráculo. É processo.
