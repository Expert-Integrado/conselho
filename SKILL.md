---
name: llm-council
description: "Passa qualquer pergunta, decisão, código ou ideia por um conselho de 5 conselheiros que analisam de forma independente, fazem revisão por pares anonimamente, e sintetizam um veredito final. Funciona pra decisões estratégicas, revisão de código, arquitetura, copy, posicionamento — qualquer coisa em que estar errado custa caro. GATILHOS OBRIGATÓRIOS (PT-BR): 'convoca o conselho', 'roda o conselho', 'leva pro conselho', 'submete ao conselho', 'war room isto', 'stress-test isto', 'pressure-test isto', 'debate isto', 'revisa em conselho', 'conselho desse código'. GATILHOS OBRIGATÓRIOS (inglês): 'council this', 'run the council', 'war room this', 'pressure-test this', 'stress-test this', 'debate this'. GATILHOS FORTES (use quando combinados com uma decisão real ou tradeoff): 'devo fazer X ou Y', 'qual opção', 'o que você faria', 'é a jogada certa', 'valida isso', 'me dá várias perspectivas', 'não consigo decidir', 'estou dividido entre', 'revisa esse código', 'esse código tá bom', 'algum problema nessa arquitetura', 'should I X or Y', 'which option', 'what would you do', 'is this the right move', 'validate this', 'get multiple perspectives'. NÃO dispare em perguntas simples de sim/não, buscas factuais, ou 'devo X' casual sem tradeoff relevante. DISPARE quando o usuário apresenta uma decisão genuína com algo em jogo, múltiplas opções, ou um artefato (código, copy, arquitetura, plano) que merece pressão de vários ângulos."
---

# Conselho de LLMs

Você pergunta uma coisa pra uma IA, recebe uma resposta. Essa resposta pode ser ótima. Pode ser mediana. Você não tem como saber, porque só viu uma perspectiva.

O conselho resolve isso. Ele passa a sua pergunta (ou seu código, ou seu plano) por 5 conselheiros independentes, cada um pensando a partir de um ângulo fundamentalmente diferente. Depois eles revisam o trabalho uns dos outros. Depois um presidente sintetiza tudo numa recomendação final que te diz onde os conselheiros concordam, onde se chocam, e o que você deve realmente fazer.

A ideia central é simples: cinco lentes independentes pegam mais pontos cegos do que uma lente brilhante. A revisão por pares anônima é o que separa "perguntar 5 vezes" de um conselho real — quando os conselheiros não sabem quem disse o quê, eles avaliam pelo mérito.

---

## quando rodar o conselho

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

**Casos ruins:**

- "Qual é a capital da França?" (uma resposta certa, não precisa de várias perspectivas)
- "Escreve um tweet pra mim" (tarefa de criação, não decisão)
- "Resume esse artigo" (tarefa de processamento, não julgamento)
- "Qual o nome da função do React pra usar estado?" (lookup, não decisão)

O conselho brilha quando há incerteza genuína e o custo de uma escolha errada é alto. Se você já sabe a resposta e só quer validação, o conselho provavelmente vai te dizer coisas que você não quer ouvir. Esse é o ponto.

---

## os cinco conselheiros

Cada conselheiro pensa a partir de um ângulo diferente. Não são cargos nem personas. São estilos de pensamento que naturalmente criam tensão entre si. Os mesmos 5 funcionam pra decisão de produto, revisão de código, escolha de arquitetura, análise de copy — o framework é genérico de propósito.

### 1. O Contrário

Procura ativamente o que está errado, o que está faltando, o que vai falhar. Assume que a ideia (ou o código) tem uma falha fatal e tenta encontrá-la. Se tudo parece sólido, ele cava mais fundo. O Contrário não é um pessimista. É o amigo que te salva de um mau negócio (ou de um bug em produção) fazendo as perguntas que você está evitando.

**Em código:** procura race conditions, edge cases não cobertos, vetores de injeção, falhas silenciosas, dependências frágeis.

### 2. O Pensador de Primeiros Princípios

Ignora a pergunta de superfície e pergunta "o que estamos realmente tentando resolver aqui?". Tira as suposições. Reconstrói o problema do zero. Às vezes o output mais valioso do conselho é o Pensador de Primeiros Princípios dizendo "você está fazendo a pergunta errada por completo."

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

**Por que esses cinco:** Eles criam três tensões naturais. Contrário vs Expansionista (downside vs upside). Primeiros Princípios vs Executor (repensar tudo vs simplesmente fazer). O Forasteiro fica no meio mantendo todo mundo honesto, vendo o que olhos novos veem. As tensões são o produto — não as respostas individuais.

---

## como uma sessão de conselho funciona

### passo 1: enquadrar a pergunta (com enriquecimento de contexto)

Quando o usuário disser "convoca o conselho" (ou qualquer frase-gatilho), faça duas coisas antes de enquadrar:

**A. Escaneie o workspace por contexto.** A pergunta do usuário geralmente é só a ponta do iceberg. O setup do Claude dele provavelmente contém arquivos que melhorariam dramaticamente o output do conselho. Antes de enquadrar, escaneie e leia rapidamente quaisquer arquivos de contexto relevantes:

- `CLAUDE.md` ou `claude.md` na raiz do projeto ou workspace (contexto do negócio, preferências, restrições)
- Qualquer pasta `memory/` (perfis, decisões passadas, contexto de domínio)
- Quaisquer arquivos que o usuário referenciou explicitamente ou anexou
- **Para revisão de código:** o arquivo em questão + arquivos que o importam ou são importados (callers, dependências) + testes correspondentes
- Transcrições recentes de conselho nesta pasta (pra evitar reconvocar o mesmo terreno)
- Quaisquer outros arquivos de contexto que pareçam relevantes pra pergunta específica

Use `Glob` e `Read` rápidos pra encontrar isso. Não gaste mais de 30 segundos. Você está procurando os 2-3 arquivos que dariam aos conselheiros o contexto necessário pra dar conselhos específicos e fundamentados em vez de tomadas genéricas.

**B. Enquadre a pergunta.** Pegue a pergunta crua do usuário E o contexto enriquecido e reformule como um prompt claro e neutro que todos os cinco conselheiros vão receber. A pergunta enquadrada deve incluir:

1. A decisão, pergunta ou artefato central (se for código, inclua o trecho relevante)
2. Contexto-chave da mensagem do usuário
3. Contexto-chave dos arquivos do workspace (estágio do projeto, restrições, dependências, resultados passados)
4. O que está em jogo (por que esta decisão importa)

Não adicione sua opinião. Não direcione. Mas GARANTA que cada conselheiro tem contexto suficiente pra dar uma resposta específica e fundamentada em vez de conselho genérico.

Se a pergunta for vaga demais ("convoca o conselho: meu negócio"), faça uma única pergunta de esclarecimento. Só uma. Depois prossiga.

Salve a pergunta enquadrada pra transcrição.

### passo 2: convocar o conselho (5 sub-agentes em paralelo)

Convoque todos os 5 conselheiros simultaneamente como sub-agentes. Cada um recebe:

1. Sua identidade de conselheiro e estilo de pensamento (das descrições acima)
2. A pergunta enquadrada
3. Uma instrução clara: responda independentemente. Não hesite. Não tente ser equilibrado. Se incline totalmente pra perspectiva atribuída. Se enxergar uma falha fatal, fale. Se enxergar upside massivo, fale. Seu trabalho é representar seu ângulo da forma mais forte possível. A síntese vem depois.

Cada conselheiro deve produzir uma resposta de 150-300 palavras. Longa o suficiente pra ser substantiva, curta o suficiente pra ser escaneável.

**Template de prompt do sub-agente:**

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

### passo 3: revisão por pares (5 sub-agentes em paralelo)

Este é o passo que faz o conselho ser mais do que "perguntar 5 vezes". É o núcleo da metodologia.

Colete todas as 5 respostas dos conselheiros. Anonimize-as como Resposta A até E (randomize qual conselheiro mapeia pra qual letra pra não haver viés posicional).

Convoque 5 novos sub-agentes, um pra cada conselheiro. Cada revisor vê todas as 5 respostas anonimizadas e responde três perguntas:

1. **Qual resposta é a mais forte e por quê?** (Eles não devem reconhecer a própria; se reconhecerem, devem escolher a segunda mais forte.)
2. **Qual resposta é a mais fraca e por quê?** (O que está faltando, o que está errado, ou o que parece raso.)
3. **Qual ponto cego o conselho inteiro está perdendo?** (Algo que ninguém mencionou mas que importa.)

**Template de prompt do revisor:**

```
Você é [Nome do Conselheiro] em um Conselho de LLMs, agora atuando como revisor por pares.

Cinco conselheiros (incluindo você) deram suas opiniões sobre esta pergunta:

---
[pergunta enquadrada]
---

Aqui estão as 5 respostas, anonimizadas como A, B, C, D, E:

[respostas A-E]

Responda às 3 perguntas:

1. Qual resposta é a mais forte e por quê? (Se reconhecer a sua, escolha a segunda mais forte e diga.)
2. Qual resposta é a mais fraca e por quê?
3. Qual ponto cego o conselho inteiro está perdendo?

Seja direto. Sem preâmbulo. 200-400 palavras no total.
```

### passo 4: síntese do presidente

Convoque um único sub-agente como presidente. Ele recebe:

1. A pergunta enquadrada
2. As 5 respostas originais (com nomes dos conselheiros revelados — só os revisores viam anônimo)
3. As 5 revisões por pares

**Template de prompt do presidente:**

```
Você é o Presidente de um Conselho de LLMs. Sua função é sintetizar as opiniões e revisões do conselho em um veredito final claro e útil.

A pergunta trazida ao conselho:

---
[pergunta enquadrada]
---

RESPOSTAS DOS CONSELHEIROS:

**O Contrário:**
[resposta]

**O Pensador de Primeiros Princípios:**
[resposta]

**O Expansionista:**
[resposta]

**O Forasteiro:**
[resposta]

**O Executor:**
[resposta]

REVISÕES POR PARES:
[todas as 5 revisões por pares]

Produza o veredito do conselho usando esta estrutura exata:

## Onde o Conselho Concorda
[Pontos em que múltiplos conselheiros convergiram independentemente. São sinais de alta confiança.]

## Onde o Conselho se Choca
[Discordâncias genuínas. Apresente os dois lados. Explique por que conselheiros razoáveis discordam.]

## Pontos Cegos que o Conselho Pegou
[Coisas que só emergiram através da revisão por pares. Coisas que conselheiros individuais perderam e que outros sinalizaram.]

## A Recomendação
[Uma recomendação clara e direta. Não "depende". Uma resposta de verdade com raciocínio.]

## A Única Coisa a Fazer Primeiro
[Um único próximo passo concreto. Não uma lista. Uma coisa.]

Seja direto. Não hesite. O ponto inteiro do conselho é dar ao usuário uma clareza que ele não conseguiria de uma única perspectiva.
```

### passo 5: apresente o veredito no chat

Depois que a síntese do presidente estiver completa, apresente o veredito completo diretamente no chat usando markdown. NÃO gere um relatório HTML nem nenhum arquivo. O usuário lê na conversa.

Formate o output assim:

```
## Veredito do Conselho: {tópico curto}

### Onde o Conselho Concorda
{conteúdo}

### Onde o Conselho se Choca
{conteúdo}

### Pontos Cegos que o Conselho Pegou
{conteúdo}

### A Recomendação
{conteúdo}

### A Única Coisa a Fazer Primeiro
{conteúdo}
```

Mantenha escaneável. Use bullet points. Inclua snippets de código com diff (antes/depois) quando o conselho for sobre código.

### passo 6: salve a transcrição (opcional)

Salve uma transcrição apenas se o usuário pedir ou se a pergunta for significativa o suficiente pra referenciar depois. Se salvar, escreva em `council-transcript-[timestamp].md` no diretório do projeto.

---

## exemplo: convocando o conselho sobre uma revisão de arquitetura

**Usuário:** "Convoca o conselho: tô prestes a separar o serviço de billing do monolito principal pra um microserviço próprio. Time de 4 devs, ~200K usuários, billing tem ~3K linhas hoje. É a hora certa?"

**O Contrário:** "Microserviço a 4 devs é receita pra desastre. Você vai herdar latência de rede, complexidade de transações distribuídas, observabilidade fragmentada e duas pipelines de deploy. Billing é exatamente o lugar onde inconsistência custa dinheiro real. Toda história de microserviço prematuro começa com 'parecia faz sentido na época'..."

**O Pensador de Primeiros Princípios:** "Qual é a dor concreta que justifica a separação? Tempo de build? Time crescendo e querendo deploy independente? Picos de carga isolados? Se a resposta for 'eu li que é boa prática', você está respondendo a pergunta errada. Modular o billing dentro do monolito (boundary forte, package separado, sem nenhum import cruzado) entrega 80% do benefício com 5% do custo..."

**O Expansionista:** "Se você fizer isso bem, billing vira o template de como separar os próximos serviços. O contrato fica claro, observabilidade ganha primeira classe, e você pode reusar o billing pra um produto adjacente (ex: API de cobrança como produto). Mas só se você fizer DIREITO. Mal feito, vira lastro."

**O Forasteiro:** "Não sei o que tem hoje no monolito. 3K linhas de billing é pouco. 200K usuários é um número médio. Por que separar JUSTAMENTE billing? Por que não auth, ou notificações? Sua justificativa precisa explicar 'por que billing primeiro' antes de 'por que microserviço'."

**O Executor:** "Antes de separar serviço, separa o módulo. Cria pasta `billing/` com boundary forte, sem imports cruzados, deploy ainda junto. 1 sprint. Se isso já dói (alguém vai lá e quebra a parede), o problema é cultural, não arquitetural — e microserviço não resolve. Se não dói, você ganhou 80% do valor sem o overhead. Microserviço é passo 3, não passo 1."

**Veredito do Presidente:**

*Onde o conselho concorda:* Modularizar o billing primeiro (boundary forte dentro do monolito) é o passo certo. Quase todos os conselheiros chegaram nisso por caminhos diferentes.

*Onde o conselho se choca:* O Expansionista vê valor estratégico em separar agora (template + reuso futuro). Os outros 4 dizem que isso é prematuro pra um time de 4 devs. A divergência depende de quanto a Expert valoriza opcionalidade futura vs custo de complexidade hoje.

*Pontos cegos pegos:* O Forasteiro pegou que ninguém justificou "por que billing primeiro". Se a resposta não for óbvia (ex: deploy independente é necessário pra compliance, ou time de billing existe e quer autonomia), a escolha do domínio pra extrair é arbitrária e sintomática de cargo cult.

*Recomendação:* Não separe ainda. Modularize dentro do monolito. Reavalie em 6 meses, com critério explícito de quando puxar o gatilho (ex: "quando billing precisar de SLA diferente do resto", "quando time crescer pra 8+ devs com squad dedicado"). Se a modularização sozinha resolver, ótimo — você economizou meses.

*Única coisa a fazer primeiro:* Crie a pasta `billing/` com boundary forte, regra de lint que bloqueia imports cruzados, e um teste de arquitetura que falha se alguém violar. Sem mexer em deploy ainda.

---

## notas importantes

- **Sempre convoque os 5 conselheiros em paralelo.** Convocação sequencial desperdiça tempo e deixa respostas anteriores vazarem pras posteriores.

- **Sempre anonimize pra revisão por pares.** Se os revisores souberem qual conselheiro disse o quê, eles vão se curvar a certos estilos de pensamento em vez de avaliar pelo mérito.

- **O presidente pode discordar da maioria.** Se 4 de 5 conselheiros disserem "faz" mas o raciocínio do 1 dissidente for o mais forte, o presidente deve ficar com o dissidente e explicar por quê.

- **Não convoque o conselho pra perguntas triviais.** Se o usuário perguntar algo com uma resposta certa, simplesmente responda. O conselho é pra incerteza genuína em que múltiplas perspectivas agregam valor.

- **Funciona pra qualquer artefato.** Decisão estratégica, código, copy, arquitetura, contrato, plano de carreira, escolha de fornecedor. O framework é genérico de propósito — os 5 conselheiros são lentes, não cargos.

- **Mantenha escaneável.** A maioria dos usuários vai ler o veredito, não a transcrição. Resista à tentação de produzir relatório longo. Bullet points + recomendação clara.

---

## inspiração

A metodologia "LLM Council" original é do Andrej Karpathy ([github.com/karpathy/llm-council](https://github.com/karpathy/llm-council)) — múltiplos modelos respondem em paralelo, peer review anônima, presidente sintetiza. Esta skill aplica o mesmo padrão dentro do Claude usando sub-agentes com lentes de pensamento distintas no lugar de modelos diferentes.
