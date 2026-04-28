# Conselho de LLMs (LLM Council)

Skill do Claude Code que transforma uma pergunta, decisão ou artefato (código, copy, arquitetura, plano) em **cinco perspectivas independentes + revisão por pares + veredito final** — em uma única chamada.

## O que é

Quando você pergunta uma coisa pra IA, recebe uma resposta. Pode ser ótima, pode ser mediana — você não sabe, porque viu uma perspectiva só. O Conselho convoca cinco conselheiros com estilos de pensamento distintos, faz peer review anônima entre eles, e um presidente sintetiza. O resultado é um veredito que mostra onde há convergência (alta confiança), onde há choque (decisão real), pontos cegos pegos pelo grupo, e a única coisa pra fazer primeiro.

Funciona pra qualquer coisa em que estar errado custa caro: decisão estratégica, revisão de código, escolha de arquitetura, copy de landing, contrato, posicionamento, plano de pivot. O framework é genérico de propósito.

## Os 5 conselheiros

1. **O Contrário** — procura o que vai falhar (bugs, falhas fatais, edge cases)
2. **O Pensador de Primeiros Princípios** — questiona se a pergunta é a certa
3. **O Expansionista** — vê o upside escondido / oportunidade de generalizar
4. **O Forasteiro** — sem contexto, pega problema de manutenibilidade e maldição do conhecimento
5. **O Executor** — só liga pro que funciona segunda-feira de manhã

Os mesmos 5 funcionam pra decisão de produto, revisão de código, escolha de stack — porque são lentes de pensamento, não cargos.

## Como instalar

### Claude Code (recomendado)

Coloque `SKILL.md` em uma das pastas que o Claude Code carrega como skill:

- **Global (todas as sessões):** `~/.claude/skills/llm-council/SKILL.md`
- **Por projeto:** `<projeto>/.claude/skills/llm-council/SKILL.md`

Reinicie o Claude Code. Pronto.

### Claude Desktop

Copie `SKILL.md` pra:

- **macOS:** `~/Library/Application Support/Claude/skills/llm-council/`
- **Linux:** `~/.config/Claude/skills/llm-council/`
- **Windows:** `%APPDATA%\Claude\skills\llm-council\`

Reinicie o app.

## Como usar

Diga uma das frases-gatilho seguida da sua pergunta ou contexto:

- `convoca o conselho: devo lançar X ou Y?`
- `roda o conselho sobre essa arquitetura: <cole/anexe o código>`
- `revisa em conselho esse PR`
- `pressure-test esse posicionamento`
- `war room isso: <decisão>`
- `council this: <question>`

O Claude vai:

1. **Escanear o workspace** por contexto relevante (CLAUDE.md, memory/, arquivos referenciados)
2. **Enquadrar a pergunta** de forma neutra
3. **Convocar os 5 conselheiros** em paralelo (cada um responde isoladamente)
4. **Anonimizar e fazer peer review** (cada conselheiro avalia A-E sem saber quem é quem)
5. **Sintetizar via Presidente** num veredito estruturado
6. **Apresentar no chat** (não gera arquivo, a menos que você peça)

Tempo típico: 1-3 minutos.

## Quando NÃO usar

- Perguntas factuais com resposta certa ("qual a capital da França?")
- Tarefas de criação simples ("escreva um tweet")
- Lookup técnico ("qual o método pra X em Y?")
- Decisões triviais sem custo real de errar

O Conselho é overhead — vale quando há incerteza genuína e o erro é caro.

## Inspiração

Metodologia "LLM Council" original do Andrej Karpathy ([github.com/karpathy/llm-council](https://github.com/karpathy/llm-council)). A versão original usa múltiplos modelos respondendo em paralelo. Aqui usamos sub-agentes do Claude com lentes distintas — mesma mecânica, dentro de uma única chamada.
