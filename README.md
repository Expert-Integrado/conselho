# Conselho de LLMs

Skill do Claude Code que transforma uma pergunta, decisão ou artefato em **cinco perspectivas independentes + revisão por pares + veredito final**.

> **Versão:** 1.0.0

## O que é

Quando você pergunta uma coisa pra IA, recebe uma resposta. Pode ser ótima, pode ser mediana — você não sabe, porque viu uma só. O Conselho convoca cinco conselheiros com estilos de pensamento distintos, faz peer review anônima entre eles, e um presidente sintetiza. O resultado é um veredito que mostra onde há convergência (alta confiança), onde há choque (decisão real), pontos cegos pegos pelo grupo, e a única coisa pra fazer primeiro.

Funciona pra qualquer coisa em que estar errado custa caro: decisão estratégica, revisão de código, escolha de arquitetura, copy de landing, contrato, posicionamento, plano de pivot.

## Os 5 conselheiros

1. **O Contrário** — procura o que vai falhar (bugs, falhas fatais, edge cases, riscos ignorados)
2. **O Pergunta-Por-Quê** — questiona se a pergunta é a certa, tira premissas, reconstrói o problema
3. **O Expansionista** — vê o upside escondido / oportunidade de generalizar
4. **O Forasteiro** — sem contexto, pega problema de manutenibilidade e maldição do conhecimento
5. **O Executor** — só liga pro que funciona segunda-feira de manhã

Mesmos 5 funcionam pra decisão de produto, revisão de código, escolha de stack — porque são lentes de pensamento, não cargos.

---

## ⚠️ Antes de instalar — sobre planos

Cada convocação dispara cerca de **11 sub-agentes do Claude Code** (5 conselheiros + 5 revisores em paralelo + 1 presidente). Tradução prática:

| Plano | Quantas convocações por dia |
|---|---|
| **Free** | Inviável — estoura nas primeiras 1-2 |
| **Pro** | 2-5 antes de bater rate limit |
| **Max** | Recomendado pra uso recorrente |

Se você tem Pro, dá pra usar — só não use pra cada dúvida do dia. Reserve pra decisões reais.

---

## Instalação

### 🪟 Windows (PowerShell)

```powershell
# 1. Cria a pasta
New-Item -ItemType Directory -Force -Path "$env:USERPROFILE\.claude\skills\llm-council"

# 2. Baixa o SKILL.md (ajuste o URL se você baixou de outro lugar)
Invoke-WebRequest -Uri "https://raw.githubusercontent.com/expertintegrado/conselho-llm/main/SKILL.md" -OutFile "$env:USERPROFILE\.claude\skills\llm-council\SKILL.md"

# 3. Confirma que o arquivo está lá
Get-Item "$env:USERPROFILE\.claude\skills\llm-council\SKILL.md"
```

### 🍎 macOS / 🐧 Linux

```bash
# 1. Cria a pasta + baixa o SKILL.md
mkdir -p ~/.claude/skills/llm-council
curl -L https://raw.githubusercontent.com/expertintegrado/conselho-llm/main/SKILL.md \
  -o ~/.claude/skills/llm-council/SKILL.md

# 2. Confirma que está lá
ls -la ~/.claude/skills/llm-council/SKILL.md
```

### 4. Reinicie o Claude Code

Feche e abra de novo (`Ctrl+C` e `claude`). Sem reiniciar, a skill nova não carrega.

### 5. Confirme que carregou

Roda `/help` no Claude Code. Se a skill carregou, ela aparece na lista. Se não aparecer, veja a seção [Quando algo der errado](#quando-algo-der-errado) abaixo.

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

O Claude vai:

1. Escanear o workspace por contexto (`CLAUDE.md`, `memory/`, arquivos referenciados)
2. Enquadrar a pergunta de forma neutra
3. Convocar os 5 conselheiros em paralelo
4. Anonimizar e fazer peer review (cada conselheiro avalia A-E sem saber quem é quem)
5. Sintetizar via Presidente num veredito estruturado
6. Apresentar no chat (sem gerar arquivo, a menos que você peça)

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
| Skill não aparece em `/help` | Verifique o caminho exato: tem que ser `~/.claude/skills/llm-council/SKILL.md` (no Windows: `C:\Users\SEU_USUARIO\.claude\skills\llm-council\SKILL.md`). Reinicie o Claude Code. |
| Falei "convoca o conselho" e nada disparou | Tente uma frase mais explícita: `usa a skill llm-council pra avaliar X` ou `quero o conselho de LLMs sobre X`. O matching de gatilhos pode falhar se a frase tiver muito ruído. |
| Erro "rate limit" no meio da execução | Você bateu o limite do seu plano. Aguarde a janela renovar (geralmente 5h) ou suba pra plano Max. |
| Sub-agente travou e não respondeu | Roda de novo. Se persistir 2x seguidas, pode ser instabilidade do Claude Code naquele momento — espera 10 min e tenta de novo. |
| Sou aluno do Eric e tô travado | Veja a seção [Suporte](#suporte) abaixo. |

---

## Suporte

- **Issues no GitHub:** https://github.com/expertintegrado/conselho-llm/issues — abra issue se algo não funciona ou tem sugestão. Inclua: SO, plano do Claude Code, frase-gatilho que usou, mensagem de erro.
- **Aluno da Expert Integrado:** comunidade da Expert ou canal de suporte do seu programa específico (verifique seu acesso).

---

## Inspiração

Metodologia "LLM Council" original do Andrej Karpathy ([github.com/karpathy/llm-council](https://github.com/karpathy/llm-council)). A versão original usa múltiplos modelos respondendo em paralelo. Aqui usamos sub-agentes do Claude com lentes distintas — mesmo padrão, dentro de uma única chamada. **Honestidade epistêmica:** os 5 sub-agentes rodam o mesmo modelo (Claude), então a "independência" entre eles é parcial — vem do prompt, não da arquitetura. O valor real é forçar o modelo a não convergir prematuramente, o que combate viés de confirmação. Use o Conselho como ferramenta anti-viés-de-confirmação estruturado, não como "5 mentes de verdade".
