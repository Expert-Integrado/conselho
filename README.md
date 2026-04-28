# Conselho de LLMs

Skill do Claude Code que transforma uma pergunta, decisão ou artefato em **cinco perspectivas independentes + revisão por pares + veredito final**.

> **Versão:** 1.1.0

## O que é

Quando você pergunta uma coisa pra IA, recebe uma resposta. Pode ser ótima, pode ser mediana — você não sabe, porque viu uma só. O Conselho convoca cinco conselheiros com estilos de pensamento distintos, faz peer review anônima entre eles, e um presidente sintetiza. O resultado é um veredito que mostra onde há convergência (alta confiança), onde há choque (decisão real), pontos cegos pegos pelo grupo, e a única coisa pra fazer primeiro.

Funciona pra qualquer coisa em que estar errado custa caro: contratar ou esperar, lançar ou validar antes, refatorar agora ou depois, demitir ou dar mais 3 meses, mudar pricing, escolher fornecedor, revisar copy, aprovar arquitetura.

**Honestidade epistêmica:** os 5 conselheiros rodam o mesmo modelo (Claude) com prompts diferentes — não são 5 mentes independentes, é uma mente forçada a usar 5 lentes. O valor é **anti-viés-de-confirmação estruturado**, não "votação de cinco oráculos". Use com essa expectativa.

## Os 5 conselheiros

1. **O Contrário** — procura o que vai falhar (bugs, falhas fatais, edge cases, riscos ignorados)
2. **O Pergunta-Por-Quê** — questiona se a pergunta é a certa, tira premissas, reconstrói o problema
3. **O Expansionista** — vê o upside escondido, oportunidade de generalizar
4. **O Forasteiro** — sem contexto, pega manutenibilidade e maldição do conhecimento
5. **O Executor** — só liga pro que funciona segunda-feira de manhã

Mesmos 5 funcionam pra decisão de produto, revisão de código, escolha de stack, contratação, pricing — porque são lentes de pensamento, não cargos.

---

## ⚠️ Antes de instalar — sobre planos do Claude Code

Cada convocação dispara cerca de **11 sub-agentes do Claude Code** (5 conselheiros + 5 revisores + 1 presidente). Tradução prática:

| Plano | Quantas convocações por dia |
|---|---|
| **Free** | Inviável — estoura nas primeiras 1-2 |
| **Pro** | 2-5 antes de bater rate limit |
| **Max** | Recomendado pra uso recorrente |

Se você tem Pro, dá pra usar — só não use pra cada dúvida do dia. Reserve pra decisões reais.

---

## Glossário rápido (pra quem é novo)

- **Claude Code:** o aplicativo CLI da Anthropic em que esta skill roda. Se você não tem, instale primeiro em https://docs.claude.com/en/docs/claude-code/setup
- **Skill:** um arquivo `SKILL.md` que ensina o Claude a fazer algo específico quando você diz uma frase-gatilho
- **Frase-gatilho:** o que você digita pra ativar a skill (ex: "convoca o conselho")
- **Sub-agente:** uma "instância" do Claude que ele dispara em paralelo pra fazer uma sub-tarefa
- **Rate limit:** limite de uso por janela de tempo do seu plano. Quando estoura, espera (geralmente 5h) ou sobe de plano
- **Markdown:** formato de texto simples com `#` pra título e `**` pra negrito. Os arquivos `.md` deste repo são markdown
- **Frontmatter:** as primeiras linhas entre `---` no topo do `SKILL.md` que carregam metadata pro Claude

---

## Instalação

> **Importante:** os comandos abaixo só **baixam um arquivo de texto e copiam pra uma pasta dentro do seu próprio usuário**. Não mexem em nada do sistema, não pedem senha de admin, não instalam programa.

### Passo 1 — baixe os arquivos deste repo

Opções:
- **Download direto (mais simples):** clique em "Code" → "Download ZIP" no GitHub → descompacte
- **Git clone:** `git clone <URL_DESTE_REPO>` na pasta da sua escolha

Depois desse passo, você tem `SKILL.md` num lugar do seu computador.

### Passo 2 — copie o `SKILL.md` pra pasta de skills do Claude Code

#### 🪟 Windows (PowerShell)

> Como abrir o PowerShell: aperte tecla Windows, digite `PowerShell`, abra. Você verá uma tela azul/preta — cole o comando e aperte Enter.

```powershell
# Cria a pasta da skill (não erra se já existe)
New-Item -ItemType Directory -Force -Path "$env:USERPROFILE\.claude\skills\llm-council" | Out-Null

# Copia o SKILL.md (ajuste o caminho de origem se você baixou em outro lugar)
Copy-Item -Path ".\SKILL.md" -Destination "$env:USERPROFILE\.claude\skills\llm-council\SKILL.md" -Force

# Confirma
Get-Item "$env:USERPROFILE\.claude\skills\llm-council\SKILL.md"
```

#### 🍎 macOS / 🐧 Linux

> Como abrir o Terminal (Mac): `Cmd+Espaço`, digite `Terminal`, abra. Cole, Enter.

```bash
# Cria a pasta + copia o SKILL.md (ajuste o caminho de origem se necessário)
mkdir -p ~/.claude/skills/llm-council
cp ./SKILL.md ~/.claude/skills/llm-council/SKILL.md

# Confirma
ls -la ~/.claude/skills/llm-council/SKILL.md
```

### Passo 3 — reinicie o Claude Code

Feche e abra de novo (`Ctrl+C` e depois `claude`). Sem reiniciar, a skill nova não carrega.

### Passo 4 — confirme que carregou

Rode `/help` no Claude Code. Se a skill carregou, ela aparece na lista. Se não aparecer, veja a seção [Quando algo der errado](#quando-algo-der-errado) abaixo.

---

## Teste de fumaça (1 minuto)

Cole isso no Claude Code pra ver o conselho funcionando pela primeira vez:

```
convoca o conselho: devo cobrar mensal ou anual no meu produto?
```

Vai demorar 1-3 minutos. Você verá o Claude convocar 5 sub-agentes, depois mais 5 pra peer review, depois um presidente. No final, recebe um veredito estruturado em markdown.

Se isso funcionou, está tudo certo.

---

## Como usar de verdade

Diga uma das frases-gatilho seguida da sua pergunta ou contexto:

- `convoca o conselho: devo lançar X ou Y?`
- `roda o conselho sobre essa arquitetura: <cole o código>`
- `revisa em conselho esse PR`
- `debate isso: <decisão>`
- `council this: <question>`

### Exemplos do mundo real

- **Decisão de produto:** "convoca o conselho: devo lançar workshop pago de R$ 97 ou aulão grátis pra construir lista? Audiência de 8K, lista de 1,2K"
- **Contratação:** "convoca o conselho: devo contratar mais um vendedor agora ou esperar 6 meses até consolidar o time atual?"
- **Pricing:** "convoca o conselho: subo o ticket do meu serviço de R$ 5K pra R$ 12K ou foco em volume?"
- **Filial:** "debate isso: fechar a filial de São Paulo ou dar mais 12 meses de runway?"
- **Revisão de código:** "revisa em conselho esse arquivo X.ts" (com o código colado ou referenciado)
- **Copy:** "convoca o conselho: qual desses 3 ângulos de posicionamento é o mais forte? <cole>"

## Quando NÃO usar

- Perguntas factuais com resposta certa
- Tarefas de criação simples ("escreva um tweet")
- Lookup técnico ("qual o método pra X?")
- Decisões triviais ("devo usar markdown")

O Conselho é overhead — vale quando há incerteza genuína e o erro custa caro.

---

## Quando algo der errado

| Sintoma | O que tentar |
|---|---|
| **Skill não aparece em `/help`** | Verifique o caminho exato: tem que ser `~/.claude/skills/llm-council/SKILL.md` (no Windows: `C:\Users\SEU_USUARIO\.claude\skills\llm-council\SKILL.md`). Reinicie o Claude Code. |
| **404 ao baixar / repo não encontrado** | Confirme que copiou o URL exato do repositório. Se você baixou pelo "Download ZIP", siga o caminho do arquivo descompactado, sem URL. |
| **PowerShell: `cannot be loaded because running scripts is disabled`** | Política de execução restrita. Os comandos deste README NÃO precisam mudar essa política — eles usam só `New-Item` e `Copy-Item` que são built-ins. Se mesmo assim travar, abra PowerShell como administrador e rode `Set-ExecutionPolicy -Scope CurrentUser RemoteSigned`. |
| **`mkdir: command not found` (Windows)** | Você está usando `cmd.exe`, não PowerShell. Abra PowerShell (tecla Windows → digite "PowerShell"). |
| **Falei "convoca o conselho" e nada disparou** | Tente uma frase mais explícita: `usa a skill llm-council pra avaliar X` ou `quero o conselho de LLMs sobre X`. O matching de gatilhos pode falhar se a frase tiver muito ruído. |
| **Erro "rate limit" no meio da execução** | Você bateu o limite do seu plano. Aguarde a janela renovar (geralmente 5h) ou suba pra plano Max. |
| **Sub-agente travou e não respondeu** | Roda de novo. Se persistir 2x seguidas, pode ser instabilidade do Claude Code naquele momento — espera 10 min e tenta de novo. |
| **Quero desinstalar** | Apague a pasta `~/.claude/skills/llm-council` (Mac/Linux) ou `%USERPROFILE%\.claude\skills\llm-council` (Windows). Reinicie o Claude Code. |

---

## Suporte

- **Issues no repositório:** abra issue se algo não funciona ou tem sugestão. Inclua: SO, plano do Claude Code, frase-gatilho que usou, mensagem de erro completa.
- **Aluno da Expert Integrado:** comunidade da Expert ou canal de suporte do seu programa específico.

---

## Inspiração

Metodologia "LLM Council" original do Andrej Karpathy ([github.com/karpathy/llm-council](https://github.com/karpathy/llm-council)). A versão original usa múltiplos modelos respondendo em paralelo. Aqui usamos sub-agentes do Claude com lentes distintas — mesmo padrão, dentro de uma única chamada.
