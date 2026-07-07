---
description: Convoca o Conselho de LLMs pra avaliar uma decisão, código ou ideia. 5 conselheiros + 1 presidente.
argument-hint: <sua pergunta com contexto>
---

Use a skill `conselho` pra avaliar a pergunta abaixo seguindo a metodologia completa (5 conselheiros em paralelo + presidente sintetizando o veredito final):

$ARGUMENTS

Antes de convocar, escaneie rapidamente o workspace por contexto relevante (`CLAUDE.md`, `memory/`, arquivos referenciados) — não gaste mais de 30 segundos. Use isso pra enriquecer a pergunta enquadrada que vai pros conselheiros.

Se a pergunta acima estiver vaga demais (ex: "convoca o conselho: meu negócio"), faça UMA única pergunta de esclarecimento antes de prosseguir.

Apresente o veredito final estruturado no chat: Onde o Conselho Concorda / Onde se Choca / Pontos Cegos / A Recomendação / A Única Coisa a Fazer Primeiro.
