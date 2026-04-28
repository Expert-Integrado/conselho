---
name: llm-council
description: "Passa qualquer pergunta, decisão, código ou ideia por um conselho de 5 conselheiros que analisam de forma independente, fazem revisão por pares anonimamente, e sintetizam um veredito final. Funciona pra decisões estratégicas, revisão de código, arquitetura, copy, posicionamento — qualquer coisa em que estar errado custa caro. CUSTO: cada convocação dispara ~11 sub-agentes (5 conselheiros + 5 revisores + 1 presidente). No plano Pro do Claude Code, espera 2-5 convocações antes de bater rate limit. Plano Max recomendado pra uso recorrente. GATILHOS OBRIGATÓRIOS (PT-BR): 'convoca o conselho', 'roda o conselho', 'leva pro conselho', 'submete ao conselho', 'debate isto', 'revisa em conselho', 'conselho desse código'. GATILHOS OBRIGATÓRIOS (inglês): 'council this', 'run the council', 'debate this'. GATILHOS FORTES (use quando combinados com uma decisão real ou tradeoff): 'devo fazer X ou Y', 'qual opção', 'o que você faria', 'é a jogada certa', 'me dá várias perspectivas', 'não consigo decidir', 'estou dividido entre', 'revisa esse código', 'algum problema nessa arquitetura', 'should I X or Y', 'which option', 'what would you do', 'is this the right move', 'get multiple perspectives'. NÃO dispare em perguntas casuais como 'valida esse título', 'devo usar markdown', 'tá bom esse texto?' — esses são contextos de baixo risco e o conselho é overhead. DISPARE quando o usuário apresenta uma decisão genuína com algo em jogo, múltiplas opções, ou um artefato (código, copy, arquitetura, plano) que merece pressão de vários ângulos."
---

# Conselho de LLMs

Você pergunta uma coisa pra uma IA, recebe uma resposta. Essa resposta pode ser ótima. Pode ser mediana. Você não tem como saber, porque só viu uma perspectiva.

O conselho resolve isso. Ele passa a sua pergunta (ou seu código, ou seu plano) por 5 conselheiros, cada um pensando a partir de um ângulo fundamentalmente diferente. Depois eles revisam o trabalho uns dos outros. Depois um presidente sintetiza tudo numa recomendação final que te diz onde os conselheiros concordam, onde se chocam, e o que você deve realmente fazer.

## Honestidade epistêmica logo de cara

Os 5 conselheiros são sub-agentes que rodam o **mesmo modelo** (Claude). A "independência" entre eles vem do prompt, não da arquitetura — não são cinco mentes diferentes, são uma mente forçada a usar cinco lentes diferentes. O Karpathy original usa modelos distintos pra obter independência de pesos; aqui é um substituto pragmático.

O valor real, então, NÃO é "5 mentes votam". É **anti-viés-de-confirmação estruturado**: o conselho impede o modelo de convergir prematuramente numa única resposta, força a ver o problema por ângulos que ele tenderia a pular, e a revisão por pares anônima evita deferência ao tom de cada conselheiro. Use o conselho com essa expectativa — não como oráculo de cinco oráculos. É overhead útil pra decisões caras, exagero pra decisões pequenas.

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

**O Pergunta-Por-Quê:**
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

## exemplo: convocando o conselho sobre uma decisão de produto

**Usuário:** "Convoca o conselho: tô em dúvida entre lançar um workshop pago de R$ 97 ou um aulão grátis pra construir lista. Minha audiência tem ~8K seguidores no Instagram, lista de e-mail de 1,2K. Objetivo: testar um novo produto de R$ 1.997. Qual jogada faz mais sentido?"

**O Contrário:** "Aulão grátis pra 8K seguidores parece de graça, mas custa caro. Você vai atrair curiosos que nunca compram, vai inflar lista com gente que abriu pelo isca, e a métrica de conversão pro produto de R$ 1.997 vai vir baixa porque o público não pagou nem R$ 97 pra entrar. Workshop pago filtra: quem paga R$ 97 demonstra disposição. Lista pequena de pagantes converte mais que lista grande de curiosos. O risco do grátis é parecer movimento sem virar receita."

**O Pergunta-Por-Quê:** "Você está validando o quê exatamente? Demanda pelo conteúdo, ou disposição a pagar? Se é demanda, o grátis serve melhor (mais volume = sinal mais limpo). Se é disposição a pagar, o workshop de R$ 97 é o teste real — só faz sentido lançar o R$ 1.997 se gente paga R$ 97. A pergunta certa não é 'qual lançar', é 'que sinal eu preciso ver agora pra autorizar o R$ 1.997?'. Sem isso definido, qualquer escolha vira teatro de validação."

**O Expansionista:** "Faz os dois — sequenciais. Aulão grátis primeiro pra puxar 800-1500 inscritos da audiência atual. Termina o aulão com oferta do workshop de R$ 97. Quem paga vira público pré-qualificado pro R$ 1.997 daqui a 60 dias. Funil de 3 níveis ataca curiosos, mornos e prontos no mesmo cohort. O caro é descartar 80% do funil indo direto pro pago — o grátis é a porta que multiplica o pipeline."

**O Forasteiro:** "Eu não te conheço. Vejo um anúncio de R$ 97 pra workshop seu — não compro porque não sei se você entrega valor. Vejo um aulão grátis com promessa concreta — entro porque é zero risco. Saio do aulão tendo experimentado seu jeito. Aí seu R$ 1.997 vira possível porque eu já consumi e gostei. A barreira do R$ 97 é alta pra alguém que ainda não tem prova. Lembra: você está visível pra 8K seguidores e tem lista de 1,2K, mas a fração que já consumiu seu conteúdo a fundo é menor — e é só essa fração que paga R$ 97 sem prova prévia."

**O Executor:** "Anuncia o aulão grátis pra semana que vem. 7 dias pra criar landing simples, sequência de 3 e-mails, conteúdo do aulão. Workshop pago precisa de mais infraestrutura (página de venda, recibo, suporte, garantia). Você ganha 30 dias entre validar o aulão e lançar o pago — usando o resultado do aulão como termômetro pra ajustar o pago. Se 8K seguidores virarem 1K inscritos no grátis, segue pro pago. Se virarem 200, o problema é audiência, não preço."

**Veredito do Presidente:**

*Onde o conselho concorda:* Sequenciar (grátis primeiro, pago depois) é a jogada com melhor relação informação/custo. 4 dos 5 chegaram nisso por caminhos diferentes.

*Onde o conselho se choca:* O Contrário diz que aulão grátis polui a lista. Os outros 4 dizem que poluição é tolerável quando a audiência ainda é fria. A divergência se resolve pelo *grau de aquecimento atual* — se a lista de 1,2K já comprou algo seu antes, vai direto pro pago. Se nunca comprou, aulão grátis primeiro.

*Pontos cegos pegos:* O Pergunta-Por-Quê pegou o problema de método: ninguém definiu o critério de sucesso ANTES de escolher o formato. Sem critério ("se 200 pessoas pagarem R$ 97, eu lanço o R$ 1.997"), qualquer resultado vira interpretação.

*Recomendação:* Aulão grátis primeiro, depois workshop de R$ 97 pros que ficaram, depois R$ 1.997 pra quem fechou o R$ 97. MAS: antes de mexer em qualquer coisa, escreve em uma linha qual número (inscritos no grátis, conversão pro R$ 97, conversão pro R$ 1.997) autoriza ou cancela o próximo passo. Sem esse critério escrito, você vai racionalizar qualquer resultado.

*Única coisa a fazer primeiro:* Escrever em uma linha: "se ___ pessoas pagarem R$ 97 dentro de 7 dias após o aulão, lanço o R$ 1.997". Decidir o número antes de rodar a campanha. Sem isso, qualquer resultado vai parecer aceitável.

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
