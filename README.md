# Conselho de LLMs

**[→ Como funciona o Conselho](https://expert-integrado.github.io/conselho/)** — a página do projeto, com o sistema explicado visualmente.

Skill do Claude Code que transforma uma pergunta, decisão ou artefato em **cinco perspectivas independentes + veredito final**.

> **Versão:** 2.1.1

## O que é

Quando você pergunta uma coisa pra IA, recebe uma resposta. Pode ser ótima, pode ser mediana — você não sabe, porque viu uma só. O Conselho convoca cinco conselheiros com estilos de pensamento distintos pra responderem em paralelo, e um presidente sintetiza num veredito final. O resultado mostra onde há convergência (alta confiança), onde há choque (decisão real), pontos cegos do grupo, e a única coisa pra fazer primeiro.

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

Cada convocação dispara **6 sub-agentes** (5 conselheiros + 1 presidente). Tradução prática:

| Plano | Quantas convocações por dia |
|---|---|
| **Free** | 1-2 antes de bater rate limit |
| **Pro** | 4-10 convocações |
| **Max** | Sem preocupação prática |

Da v1 pra v2 cortamos o passo de peer review entre conselheiros. Razão: Karpathy original usa modelos diferentes pra obter peer review legítimo (cada modelo tem viés distinto). Aqui rodamos um modelo só (Claude) com 5 prompts — peer review entre prompts do mesmo modelo é teatro epistêmico (todos compartilham o viés latente do Claude). O presidente continua sintetizando, que é o passo que entrega valor real: decisão sob tensão das 5 vozes.

---

## Glossário rápido

- **Skill:** um arquivo `SKILL.md` que ensina o Claude Code a fazer algo específico quando você diz uma frase-gatilho
- **Frase-gatilho:** o que você digita pra ativar a skill (ex: "convoca o conselho")
- **Rate limit:** limite de uso por janela de tempo do seu plano. Quando estoura, espera (geralmente 5h) ou sobe de plano

---

## Instalação

### Instalação assistida (recomendada)

Abra o Claude Code e cole o prompt abaixo; o Claude conduz a instalação inteira, inclusive as etapas de navegador se você deixar.

```text
Quero instalar a skill "Conselho de LLMs" do repositório https://github.com/Expert-Integrado/conselho e você vai conduzir a instalação inteira pra mim, etapa por etapa, confirmando cada resultado antes de seguir. Protocolo:

1. Pré-requisitos: confirme que estamos no Claude Code (aplicativo de terminal, não o Claude.ai). Me avise que cada convocação do conselho dispara 6 sub-agentes: no plano Free dá 1-2 convocações antes do rate limit, no Pro 4-10, no Max sem preocupação prática. Se eu estiver no Free, pergunte se quero seguir mesmo assim.

2. Baixe os arquivos: rode git clone https://github.com/Expert-Integrado/conselho.git numa pasta temporária. Se o git não estiver disponível, use o ZIP: https://github.com/Expert-Integrado/conselho/archive/refs/heads/main.zip

3. Instale a skill: crie a pasta ~/.claude/skills/conselho (Windows: %USERPROFILE%\.claude\skills\conselho) e copie skills/conselho/SKILL.md pra lá. No Windows, verifique se a pasta .claude não caiu dentro do OneDrive (o Claude Code não lê de lá); se caiu, corrija antes de seguir, conforme a seção de instalação do README.

4. Instale também o slash command: copie commands/conselho.md pra ~/.claude/commands/conselho.md (Windows: %USERPROFILE%\.claude\commands\conselho.md).

5. Para QUALQUER etapa que aconteça em navegador (por exemplo, baixar o ZIP pelo site do GitHub ou abrir a página do projeto), pergunte antes com AskUserQuestion (botões) qual rota eu prefiro: (a) você faz por mim via Playwright MCP, e se ele não estiver instalado ofereça instalar com claude mcp add playwright -- npx -y @playwright/mcp@latest; (b) você faz via Claude in Chrome; (c) eu faço manualmente com você me guiando. Logins são sempre meus: nunca digite credenciais por mim.

6. Esta skill não usa chave de API nem segredo. Se alguma etapa pedir credencial, ela nunca deve aparecer no chat: me oriente a colocá-la direto no destino final e depois valide com uma chamada real de teste.

7. Valide: confirme que os arquivos existem nos caminhos finais, me peça pra reiniciar o Claude Code (fechar e abrir de novo) e, na nova sessão, conferir se a skill aparece em /help e se /conselho aparece ao digitar /.

8. Teste ponta a ponta: proponha rodar o teste de fumaça do README (uma convocação real do conselho) e confirme que saiu o veredito estruturado.

9. Termine com um resumo do que foi configurado: caminhos dos arquivos instalados, frases-gatilho e como usar o /conselho.

Se algo falhar, consulte a tabela "Quando algo der errado" do README antes de improvisar. Não invente comandos que não estão no README.
```

### Instalação manual (alternativa)

> **Os comandos abaixo só baixam um arquivo de texto e copiam pra uma pasta dentro do seu próprio usuário.** Não mexem em nada do sistema, não pedem senha de admin, não instalam programa.

### Passo 1 — baixe os arquivos deste repo

Opções:
- **Download direto (mais simples):** clique em "Code" → "Download ZIP" no GitHub, descompacte em algum lugar (ex: `Downloads`)
- **Git clone:** `git clone <URL deste repositório>` na pasta da sua escolha

Depois desse passo você tem uma pasta com a skill em `skills/conselho/SKILL.md`.

### Passo 2 — copie o `SKILL.md` pra pasta de skills do Claude Code

#### 🪟 Windows (PowerShell)

> **Como abrir o PowerShell:** aperte tecla Windows, digite `PowerShell`, abra. Você verá uma tela azul/preta — cole os comandos e aperte Enter.

```powershell
# 1. Vá até a pasta onde você descompactou (ajuste se baixou em outro lugar)
cd "$env:USERPROFILE\Downloads\conselho-main"

# 2. Cria a pasta da skill (não erra se já existe)
New-Item -ItemType Directory -Force -Path "$env:USERPROFILE\.claude\skills\conselho" | Out-Null

# 3. Copia o SKILL.md
Copy-Item -Path ".\skills\conselho\SKILL.md" -Destination "$env:USERPROFILE\.claude\skills\conselho\SKILL.md" -Force

# 4. Confirma
Get-Item "$env:USERPROFILE\.claude\skills\conselho\SKILL.md"
```

> **Atenção OneDrive (corporativo ou pessoal):** se sua pasta de usuário está sendo redirecionada pelo OneDrive, o comando pode salvar em `OneDrive\.claude` em vez de `.claude` — e o Claude Code não lê de lá. Sintoma: skill não aparece em `/help` mesmo após instalar.
>
> **Como pausar a sincronização do OneDrive (1 min):**
> 1. Encontre o ícone azul de nuvem na bandeja do sistema (perto do relógio, canto inferior direito). Pode estar dentro do "^" — clique pra expandir.
> 2. Clique no ícone → ⚙️ engrenagem (Configurações/Help) → "Pausar sincronização" → escolha "2 horas".
> 3. Rode os comandos da skill, reinicie o Claude Code, confirme em `/help`.
> 4. Pode reativar OneDrive depois — a pasta `.claude` já estará criada no lugar certo.
>
> Se mesmo assim a pasta foi pra dentro do OneDrive: rode `Test-Path "$env:USERPROFILE\OneDrive\.claude\skills\conselho\SKILL.md"` no PowerShell. Se retornar `True`, mova manualmente: clique-direito → recortar → cole em `C:\Users\SEU_USUARIO\.claude\skills\conselho\` (cria a pasta se não existir).

#### 🍎 macOS / 🐧 Linux

> **Como abrir o Terminal (Mac):** `Cmd+Espaço`, digite `Terminal`, abra. Cole, Enter.

```bash
# 1. Vá até a pasta onde você descompactou (ajuste se baixou em outro lugar)
cd ~/Downloads/conselho-main

# 2. Cria a pasta + copia o SKILL.md
mkdir -p ~/.claude/skills/conselho
cp ./skills/conselho/SKILL.md ~/.claude/skills/conselho/SKILL.md

# 3. Confirma
ls -la ~/.claude/skills/conselho/SKILL.md
```

### Passo 3 (opcional) — instale o slash command `/conselho`

Pra ter um atalho mais direto (`/conselho devo X ou Y?` em vez de `convoca o conselho: ...`), copie o `commands/conselho.md` pra pasta de comandos do Claude Code:

#### 🪟 Windows (PowerShell)

```powershell
New-Item -ItemType Directory -Force -Path "$env:USERPROFILE\.claude\commands" | Out-Null
Copy-Item -Path ".\commands\conselho.md" -Destination "$env:USERPROFILE\.claude\commands\conselho.md" -Force
```

#### 🍎 macOS / 🐧 Linux

```bash
mkdir -p ~/.claude/commands
cp ./commands/conselho.md ~/.claude/commands/conselho.md
```

Depois disso, basta digitar `/conselho` no Claude Code e a sua pergunta. Ex: `/conselho devo lançar workshop pago ou aulão grátis?`

### Passo 4 — reinicie o Claude Code

Feche e abra de novo (`Ctrl+C` e depois `claude`). Sem reiniciar, a skill nova não carrega.

### Passo 5 — confirme que carregou

Rode `/help` no Claude Code. Se a skill carregou, ela aparece na lista. Se você instalou o slash command, digite `/` e veja se `/conselho` aparece. Se não aparecer, veja [Quando algo der errado](#quando-algo-der-errado) abaixo.

---

## Teste de fumaça (1 minuto)

Cole isso no Claude Code pra ver o conselho funcionando pela primeira vez:

```
convoca o conselho: devo lançar workshop pago de R$ 97 ou aulão grátis pra construir lista? Audiência de 8K, lista de 1,2K. Objetivo: testar produto de R$ 1.997.
```

Ou, se instalou o slash command, mais curto:

```
/conselho devo lançar workshop pago de R$ 97 ou aulão grátis pra construir lista? Audiência de 8K, lista de 1,2K. Objetivo: testar produto de R$ 1.997.
```

Vai demorar 1-2 minutos. Você verá o Claude convocar 5 conselheiros em paralelo e depois um presidente. No final, um veredito estruturado em markdown com onde concordam, onde se chocam, recomendação e única coisa pra fazer primeiro.

Se isso funcionou, está tudo certo.

---

## Como usar de verdade

Diga uma das frases-gatilho seguida da sua pergunta ou contexto:

- `convoca o conselho: devo lançar X ou Y?`
- `roda o conselho sobre essa arquitetura: <cole o código>`
- `revisa em conselho esse PR`
- `debate isso: <decisão>`
- `council this: <question>`

### Como enquadrar a pergunta pra ter veredito útil

Sua pergunta puxa 6 sub-agentes — vale gastar 30 segundos enquadrando ela bem. Use este modelo:

```
convoca o conselho: <pergunta principal>

Contexto: <2-4 linhas sobre seu negócio, momento, números relevantes>
Opções na mesa: <A, B, C — sem juízo, só liste>
O que está em jogo: <o que você ganha se acertar e perde se errar>
Restrições: <orçamento, prazo, time, contratos, qualquer coisa que limita>
```

Exemplo bem enquadrado:

```
convoca o conselho: contrato mais um vendedor agora ou espero 6 meses?

Contexto: empresa de SaaS B2B, MRR R$ 80K, time atual 2 vendedores entregando 6 reuniões/dia, ticket médio R$ 4K, conversão 18%.
Opções: (A) contratar júnior agora — custo R$ 5K/mês + 3 meses pra rampar, (B) esperar 6 meses pra consolidar processos com o time atual.
O que está em jogo: se contrato cedo e processo não tá maduro, queimo CAC e o cara vai embora frustrado. Se espero demais, perco janela competitiva.
Restrições: caixa pra 12 meses no atual ritmo. Não tenho head de vendas ainda.
```

Pergunta vaga ("devo contratar?") gera veredito vago. Pergunta enquadrada gera diagnóstico cirúrgico.

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
| **Skill não aparece em `/help`** | Verifique o caminho exato: tem que ser `~/.claude/skills/conselho/SKILL.md` (Windows: `C:\Users\SEU_USUARIO\.claude\skills\conselho\SKILL.md`). Reinicie o Claude Code. Se está no OneDrive, mova pra fora. |
| **`Cannot find path '.\SKILL.md'`** | Você não fez `cd` pra dentro da pasta descompactada, ou descompactou em outro lugar. Substitua `Downloads` pelo caminho real onde está o arquivo. Exemplos: descompactou no Desktop → `cd "$env:USERPROFILE\Desktop\conselho-main"`; em Documentos → `cd "$env:USERPROFILE\Documents\conselho-main"`; ou clique com botão direito no arquivo `SKILL.md` → "Copiar como caminho" pra ver o path completo. |
| **PowerShell: `cannot be loaded because running scripts is disabled`** | Os comandos deste README NÃO precisam mudar essa política — usam só built-ins (`New-Item`, `Copy-Item`). Se mesmo assim travar, abra PowerShell como administrador e rode `Set-ExecutionPolicy -Scope CurrentUser RemoteSigned`. |
| **`mkdir: command not found` (Windows)** | Você está no `cmd.exe`, não no PowerShell. Abra PowerShell (tecla Windows → digite "PowerShell"). |
| **Defender / SmartScreen bloqueia o ZIP** | Clique em "Mais informações" → "Executar mesmo assim". O ZIP só contém arquivos de texto (SKILL.md + README.md). |
| **Falei "convoca o conselho" e nada disparou** | Tente uma frase mais explícita: `usa a skill conselho pra avaliar X` ou `quero o conselho de LLMs sobre X`. |
| **Erro "rate limit" no meio da execução** | Você bateu o limite do seu plano. Aguarde a janela renovar (geralmente 5h) ou suba pra plano Max. |
| **Sub-agente travou e não respondeu** | Roda de novo. Se persistir 2x seguidas, pode ser instabilidade do Claude Code naquele momento — espera 10 min e tenta de novo. |
| **Quero desinstalar** | Apague a pasta `~/.claude/skills/conselho` (Mac/Linux) ou `%USERPROFILE%\.claude\skills\conselho` (Windows). Reinicie o Claude Code. |

---

## Suporte

- **Issues no repositório:** abra issue se algo não funciona ou tem sugestão. Inclua: SO, plano do Claude Code, frase-gatilho que usou, mensagem de erro completa.
- **Aluno da Expert Integrado:** comunidade da Expert ou canal de suporte do seu programa específico.

---

## Como funciona por dentro (pra quem quer saber)

Os 5 conselheiros rodam o mesmo modelo (Claude) com prompts diferentes — não são 5 mentes independentes, é uma mente forçada a usar 5 lentes. O valor é **anti-viés-de-confirmação estruturado**, não "votação de cinco oráculos". Use com essa expectativa.

O presidente é o mecanismo que faz a diferença vs. um simples "me dê 5 perspectivas e debata": ele força síntese e decisão sob a tensão das 5 vozes, em vez de produzir 5 textos longos pro empresário ler e decidir sozinho.

## Inspiração

Metodologia "LLM Council" original do Andrej Karpathy ([github.com/karpathy/llm-council](https://github.com/karpathy/llm-council)). A versão original usa múltiplos modelos respondendo em paralelo. Aqui usamos sub-agentes do Claude com lentes distintas — mesmo padrão, dentro de uma única chamada.
