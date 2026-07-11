# CLAUDE.md

Instruções para o Claude Code ao trabalhar neste repositório.

Este repo contém a skill **Conselho de LLMs**: 5 conselheiros (sub-agentes com lentes de pensamento distintas) + 1 presidente que sintetiza um veredito acionável. A skill vive em `skills/conselho/SKILL.md` e o slash command opcional em `commands/conselho.md`. Documentação de uso: `README.md`. Página do projeto: https://expert-integrado.github.io/conselho/

## Onboarding assistido (instruções para o Claude)

**Oferta ativa:** se o usuário chegar a este repositório sem a skill instalada (ela não aparece em `/help`, ou ele pergunta "como instalo", "como uso", "o que é isso"), ofereça conduzir a instalação: "Quer que eu conduza a instalação pra você, etapa por etapa?". Não espere ele pedir.

### Protocolo geral

- Conduza uma etapa por vez e confirme o resultado real antes de seguir para a próxima.
- Para cada etapa que aconteceria em navegador, pergunte antes com AskUserQuestion (botões) qual rota o usuário prefere:
  1. **Playwright MCP** (rota padrão): se não estiver instalado, ofereça instalar com `claude mcp add playwright -- npx -y @playwright/mcp@latest`.
  2. **Claude in Chrome**: navegador do próprio usuário, com as sessões dele.
  3. **Manual**: o usuário faz e você orienta passo a passo.
- Login é sempre do usuário: nunca digite, cole nem armazene credenciais por ele.
- Segredos nunca aparecem no chat. Esta skill não exige credencial nenhuma; se alguma etapa futura exigir, oriente o usuário a inseri-la direto no destino final e valide depois com uma chamada real de teste (nunca aceite o valor colado na conversa).
- Nunca declare a instalação concluída sem verificar: arquivo existente no caminho final, skill visível em `/help` após reinício e teste ponta a ponta executado.
- Não invente comandos, caminhos ou URLs: use apenas o que está documentado no README deste repositório.

### Etapas específicas deste repositório

1. **Pré-requisitos:** Claude Code instalado (guia oficial: https://docs.claude.com/en/docs/claude-code/setup). Avisar o custo real: cada convocação dispara 6 sub-agentes; no plano Free dá 1-2 convocações antes do rate limit, no Pro 4-10, no Max sem preocupação prática.
2. **Obter os arquivos:** `git clone https://github.com/Expert-Integrado/conselho.git` ou download do ZIP (https://github.com/Expert-Integrado/conselho/archive/refs/heads/main.zip). Baixar o ZIP pelo site do GitHub é etapa de navegador: aplicar o protocolo de rotas acima.
3. **Instalar a skill:** copiar `skills/conselho/SKILL.md` para `~/.claude/skills/conselho/SKILL.md` (Windows: `%USERPROFILE%\.claude\skills\conselho\SKILL.md`). No Windows, checar o redirecionamento do OneDrive descrito no README (seção de instalação): se a pasta `.claude` cair dentro do OneDrive, o Claude Code não a lê.
4. **Instalar o slash command (opcional, recomendado):** copiar `commands/conselho.md` para `~/.claude/commands/conselho.md` (Windows: `%USERPROFILE%\.claude\commands\conselho.md`), habilitando `/conselho`.
5. **Reiniciar e conferir:** pedir ao usuário para fechar e reabrir o Claude Code, e confirmar em `/help` que a skill carregou (e que `/conselho` aparece ao digitar `/`, se instalado).
6. **Teste ponta a ponta:** rodar o teste de fumaça do README (`convoca o conselho: ...` ou `/conselho ...`) e confirmar que saiu o veredito estruturado: Onde o Conselho Concorda / Onde se Choca / Pontos Cegos / A Recomendação / A Única Coisa a Fazer Primeiro.
7. **Resumo final:** listar o que foi instalado, em quais caminhos, e como usar (frases-gatilho do README e `/conselho`).
8. **Problemas:** consultar a tabela "Quando algo der errado" do README antes de improvisar qualquer solução.
