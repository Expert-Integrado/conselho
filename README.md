# Conselho de LLMs

Skill do Claude Code que transforma uma pergunta, decisão ou artefato em **cinco perspectivas independentes + revisão por pares + veredito final**.

> **Versão:** 1.2.0

## O que é

Quando você pergunta uma coisa pra IA, recebe uma resposta. Pode ser ótima, pode ser mediana — você não sabe, porque viu uma só. O Conselho convoca cinco conselheiros com estilos de pensamento distintos, faz peer review anônima entre eles, e um presidente sintetiza. O resultado é um veredito que mostra onde há convergência (alta confiança), onde há choque (decisão real), pontos cegos pegos pelo grupo, e a única coisa pra fazer primeiro.

Funciona pra qualquer coisa em que estar errado custa caro: contratar ou esperar, lançar ou validar antes, refatorar agora ou depois, demitir ou dar mais 3 meses, mudar pricing, escolher fornecedor, revisar copy, aprovar arquitetura.

## Os 5 conselheiros

1. **O Contrário** — procura o que vai falhar (riscos ignorados, falhas fatais)
2. **O Pergunta-Por-Quê** — questiona se a pergunta é a certa, tira premissas
3. **O Expansionista** — vê o upside escondido, oportunidade não-óbvia
4. **O Forasteiro** — sem contexto, pega o que é confuso pra quem está de fora
5. **O Executor** — só liga pro que funciona segunda-feira de manhã

---

## ⚠️ Pré-requisitos antes de instalar

### 1. Você precisa do Claude Code instalado

**Atenção:** "Claude Code" é diferente do Claude.ai (web/celular). É um aplicativo que roda no terminal do seu computador.

Se você ainda não tem: instale primeiro seguindo https://docs.claude.com/en/docs/claude-code/setup

### 2. Plano do Claude Code

Cada convocação dispara cerca de **11 sub-agentes** (5 conselheiros + 5 revisores + 1 presidente). Tradução prática:

| Plano | Quantas convocações por dia |
|---|---|
| **Free** | Inviável — estoura nas primeiras 1-2 |
| **Pro** | 2-5 antes de bater rate limit |
| **Max** | Recomendado pra uso recorrente |

Se você tem Pro, use só pra decisões reais — não pra cada dúvida do dia.

---

## Glossário rápido

- **Skill:** um arquivo `SKILL.md` que ensina o Claude Code a fazer algo específico quando você diz uma frase-gatilho
- **Frase-gatilho:** o que você digita pra ativar a skill (ex: "convoca o conselho")
- **Rate limit:** limite de uso por janela de tempo do seu plano. Quando estoura, espera (geralmente 5h) ou sobe de plano

---

## Instalação

> **Os comandos abaixo só baixam um arquivo de texto e copiam pra uma pasta dentro do seu próprio usuário.** Não mexem em nada do sistema, não pedem senha de admin, não instalam programa.

### Passo 1 — baixe os arquivos deste repo

Opções:
- **Download direto (mais simples):** clique em "Code" → "Download ZIP" no GitHub, descompacte em algum lugar (ex: `Downloads`)
- **Git clone:** `git clone <URL deste repositório>` na pasta da sua escolha

Depois desse passo você tem uma pasta com o `SKILL.md` dentro.

### Passo 2 — copie o `SKILL.md` pra pasta de skills do Claude Code

#### 🪟 Windows (PowerShell)

> **Como abrir o PowerShell:** aperte tecla Windows, digite `PowerShell`, abra. Você verá uma tela azul/preta — cole os comandos e aperte Enter.

```powershell
# 1. Vá até a pasta onde você descompactou (ajuste se baixou em outro lugar)
cd "$env:USERPROFILE\Downloads\conselho-main"

# 2. Cria a pasta da skill (não erra se já existe)
New-Item -ItemType Directory -Force -Path "$env:USERPROFILE\.claude\skills\llm-council" | Out-Null

# 3. Copia o SKILL.md
Copy-Item -Path ".\SKILL.md" -Destination "$env:USERPROFILE\.claude\skills\llm-council\SKILL.md" -Force

# 4. Confirma
Get-Item "$env:USERPROFILE\.claude\skills\llm-council\SKILL.md"
```

> **Atenção OneDrive:** se sua pasta de usuário está sendo redirecionada pelo OneDrive corporativo, o comando pode salvar em `OneDrive\.claude` em vez de `.claude` — e o Claude Code não lê de lá. Sintoma: skill não aparece em `/help` mesmo após instalar. Solução: pause sincronização do OneDrive antes ou rode `cd $HOME\.claude` antes — se cair em OneDrive, mova manualmente pra `C:\Users\SEU_USUARIO\.claude`.

#### 🍎 macOS / 🐧 Linux

> **Como abrir o Terminal (Mac):** `Cmd+Espaço`, digite `Terminal`, abra. Cole, Enter.

```bash
# 1. Vá até a pasta onde você descompactou (ajuste se baixou em outro lugar)
cd ~/Downloads/conselho-main

# 2. Cria a pasta + copia o SKILL.md
mkdir -p ~/.claude/skills/llm-council
cp ./SKILL.md ~/.claude/skills/llm-council/SKILL.md

# 3. Confirma
ls -la ~/.claude/skills/llm-council/SKILL.md
```

### Passo 3 — reinicie o Claude Code

Feche e abra de novo (`Ctrl+C` e depois `claude`). Sem reiniciar, a skill nova não carrega.

### Passo 4 — confirme que carregou

Rode `/help` no Claude Code. Se a skill carregou, ela aparece na lista. Se não aparecer, veja [Quando algo der errado](#quando-algo-der-errado) abaixo.

---

## Teste de fumaça (1 minuto)

Cole isso no Claude Code pra ver o conselho funcionando pela primeira vez:

```
convoca o conselho: devo lançar workshop pago de R$ 97 ou aulão grátis pra construir lista? Audiência de 8K, lista de 1,2K. Objetivo: testar produto de R$ 1.997.
```

Vai demorar 1-3 minutos. Você verá o Claude convocar 5 conselheiros, depois 5 revisores, depois um presidente. No final, um veredito estruturado em markdown com onde concordam, onde se chocam, recomendação e única coisa pra fazer primeiro.

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

- **Decisão de produto:** "convoca o conselho: devo lançar workshop pago de R$ 97 ou aulão grátis?"
- **Contratação:** "convoca o conselho: contrato mais um vendedor agora ou espero 6 meses?"
- **Pricing:** "convoca o conselho: subo o ticket do meu serviço de R$ 5K pra R$ 12K, ou foco em fechar mais clientes no preço atual? (não são opostos — quero saber qual move melhor receita+margem nos próximos 90 dias)"
- **Filial:** "debate isso: fechar a filial de São Paulo ou dar mais 12 meses de runway?"
- **Pós-mortem:** "convoca o conselho: contratei o João como Head Comercial em janeiro, sem PIP em 90d ele saiu em abril. O que eu deixei de ver no processo?"
- **Revisão de código:** "revisa em conselho esse arquivo X.ts" (com o código colado)
- **Copy:** "convoca o conselho: qual desses 3 ângulos de posicionamento é o mais forte? <cole>"

## Quando NÃO usar

- Perguntas factuais com resposta certa
- Tarefas de criação simples ("escreva um tweet")
- Lookup técnico ("qual o método pra X?")
- Decisões triviais ("devo usar markdown")

---

## Quando algo der errado

| Sintoma | O que tentar |
|---|---|
| **Skill não aparece em `/help`** | Verifique o caminho exato: tem que ser `~/.claude/skills/llm-council/SKILL.md` (Windows: `C:\Users\SEU_USUARIO\.claude\skills\llm-council\SKILL.md`). Reinicie o Claude Code. Se está no OneDrive, mova pra fora. |
| **`Cannot find path '.\SKILL.md'`** | Você não fez `cd` pra dentro da pasta descompactada. Veja onde está o arquivo (ex: `Downloads\llm-council-main\`) e rode `cd` pra lá antes do Copy-Item. |
| **PowerShell: `cannot be loaded because running scripts is disabled`** | Os comandos deste README NÃO precisam mudar essa política — usam só built-ins (`New-Item`, `Copy-Item`). Se mesmo assim travar, abra PowerShell como administrador e rode `Set-ExecutionPolicy -Scope CurrentUser RemoteSigned`. |
| **`mkdir: command not found` (Windows)** | Você está no `cmd.exe`, não no PowerShell. Abra PowerShell (tecla Windows → digite "PowerShell"). |
| **Defender / SmartScreen bloqueia o ZIP** | Clique em "Mais informações" → "Executar mesmo assim". O ZIP só contém arquivos de texto (SKILL.md + README.md). |
| **Falei "convoca o conselho" e nada disparou** | Tente uma frase mais explícita: `usa a skill llm-council pra avaliar X` ou `quero o conselho de LLMs sobre X`. |
| **Erro "rate limit" no meio da execução** | Você bateu o limite do seu plano. Aguarde a janela renovar (geralmente 5h) ou suba pra plano Max. |
| **Sub-agente travou e não respondeu** | Roda de novo. Se persistir 2x seguidas, pode ser instabilidade do Claude Code naquele momento — espera 10 min e tenta de novo. |
| **Quero desinstalar** | Apague a pasta `~/.claude/skills/llm-council` (Mac/Linux) ou `%USERPROFILE%\.claude\skills\llm-council` (Windows). Reinicie o Claude Code. |

---

## Suporte

- **Issues no repositório:** abra issue se algo não funciona ou tem sugestão. Inclua: SO, plano do Claude Code, frase-gatilho que usou, mensagem de erro completa.
- **Aluno da Expert Integrado:** comunidade da Expert ou canal de suporte do seu programa específico.

---

## Como funciona por dentro (pra quem quer saber)

Os 5 conselheiros rodam o mesmo modelo (Claude) com prompts diferentes — não são 5 mentes independentes, é uma mente forçada a usar 5 lentes. O valor é **anti-viés-de-confirmação estruturado**, não "votação de cinco oráculos". Use com essa expectativa.

A peer review anônima é o mecanismo que faz a diferença vs. um simples "me dê 5 perspectivas e debata": ela impede que o conselheiro mais "convincente" arraste os outros pro tom dele.

## Inspiração

Metodologia "LLM Council" original do Andrej Karpathy ([github.com/karpathy/llm-council](https://github.com/karpathy/llm-council)). A versão original usa múltiplos modelos respondendo em paralelo. Aqui usamos sub-agentes do Claude com lentes distintas — mesmo padrão, dentro de uma única chamada.
