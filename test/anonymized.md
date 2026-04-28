# Respostas anonimizadas para peer review

## RESPOSTA A
A skill instrui o Claude a convocar 5 sub-agentes em paralelo, depois mais 5 em paralelo, depois 1 presidente. São 11 sub-agentes por sessão. Pra um aluno do Eric usando Claude Code com plano normal, isso queima rate limit em 2-3 perguntas. Em "100 alunos rodando em paralelo" você tem 1.100 sub-agentes simultâneos. A skill não menciona custo, latência, nem fallback se um sub-agente falhar. "Sempre convoque os 5 conselheiros em paralelo" — sem plano B quando 2 dos 5 estourarem timeout.

O anonimato é teatro. "Anonimize-as como Resposta A até E". Os 5 estilos têm vozes assinadas — o Executor sempre fala "segunda-feira de manhã", o Forasteiro sempre confessa não ter contexto. Qualquer revisor identifica quem é quem em 10 segundos. A regra "se reconhecer a sua, escolha a segunda mais forte" confia que o LLM vai admitir reconhecimento — não vai. Vai confabular.

Convergência LLM. Os 5 "conselheiros" são o mesmo modelo com 5 prompts. Mode collapse é real. O exemplo já mostra: "Quase todos os conselheiros chegaram nisso por caminhos diferentes" — vendido como força, é exatamente o sintoma. Karpathy usa modelos diferentes por isso; a skill admite que usa "sub-agentes com lentes" como substituto. Não é equivalente. É echo chamber com 5 chapéus.

Falha 4: gatilhos como "valida isso" e "o que você faria" vão disparar em conversas casuais. Aluno pergunta "valida esse título" e leva 4 minutos + 11 sub-agentes pra escolher emoji.

Falha 5: zero tratamento de prompt injection via arquivos lidos no passo 1A. Vetor clássico.

Não publica como está.

---

## RESPOSTA B
README diz "coloque SKILL.md em ~/.claude/skills/llm-council/SKILL.md". Não diz COMO. Aluno Windows não sabe o que é ~. Aluno Mac não acha a pasta no Finder (oculta). Falta comando literal pra cada SO. Não há instrução pra Windows com PowerShell. Não diz que precisa REINICIAR o Claude Code. Não menciona versão mínima do Claude Code com suporte a skills.

Tempo git clone → primeiro veredito: otimista 5 min, realista 15-30 min com 2-3 tentativas. Pessimista: nunca, abandona.

Sem Claude Code instalado: README NÃO explica nada. Sem link de instalação, sem pré-requisito declarado. Buraco grande.

Exemplo billing/monolito: narrativo, não executável. Aluno não tem o monolito, não tem o código. Funciona como ilustração, não como "tente você". Falta exemplo trivial e replicável tipo "convoca o conselho: devo usar Postgres ou MongoDB pro meu SaaS de 50 usuários?".

100 alunos rodando amanhã: chuto 30-50 abrindo ticket. Pontos de falha: caminho da pasta no Windows, reiniciar o Claude Code, frase-gatilho não disparou.

Primeiro uso bem-sucedido em 5 min: NÃO existe hoje. Falta frase de teste pronta tipo: "cole isso e veja funcionar".

Faltam, em ordem de impacto: (1) pré-requisito explícito com versão e link, (2) bloco de comandos copiável por SO, (3) aviso pra reiniciar, (4) teste de fumaça, (5) troubleshooting de 3 erros comuns, (6) GIF de 20s.

Sem isso, repo vira gerador de mensagem no WhatsApp do Eric.

---

## RESPOSTA C
Esse ativo pode virar mais do que skill. É sistema operacional de pensamento crítico embalado como ferramenta. O upside real não é "ajudar aluno a decidir melhor" — é fazer cada aluno seu terminar o programa pensando em 5 vozes simultâneas mesmo offline. Cognição estruturada com cara de plugin. Vira citação ("o framework dos 5 conselheiros do Eric Luciano") e gera atribuição de marca por anos.

Casos de uso que o SKILL.md está deixando dinheiro na mesa: cura de tag de campanha, revisão de contrato, análise de funil quando métrica trava, escolha de fornecedor SaaS, decisão de hire/fire, revisão de pricing, pré-mortem de evento, escolha de pauta, briefing de Voice AI. Adicione "vinte gatilhos do mundo Expert" com exemplos reais Pipedrive/ClickUp/Outlook.

Posicionamento: "Na Expert, a gente não usa AI pra responder. A gente usa AI pra discordar de si mesma." Meme-able, palco G4, capa de carrossel. Versão sua dos "Six Thinking Hats" — nativo de IA e atribuível a você.

Empacotar em cima: workshop 90min como upsell, certificação "Conselheiro Expert", marketplace de conselhos verticais (Jurídico, M&A, Marketing, Vet) a R$47-197 cada, comunidade que vira corpus, API white-label pra consultorias.

Presets: Startup (Founder, CFO, Growth, Eng, Investidor cético). Comercial (SDR, Head, Cliente cético, Financeiro, RevOps). Produto. Liderança. Cada preset é SKU.

Framework mental sem AI é o jogo final. Caderno físico, camiseta, adesivo. Quando aluno pensa "o que o Contrário diria?" no chuveiro, virou ritual cognitivo — fidelidade vitalícia.

---

## RESPOSTA D
Olha, fui obrigado a ler duas vezes. Tem coisa que SÓ entendi por contexto.

Entende o que faz? Mais ou menos. "Cinco IAs com personalidades diferentes opinam sobre uma decisão sua, depois discutem, depois um chefe resume." Mas o nome "llm-council" não me diz nada. O que é "LLM"? Coloca "Conselho de IAs" e ganha 30% mais gente entendendo no primeiro segundo.

Instalação? TRAVEI. "Coloque em ~/.claude/skills/" — o til (~) é o quê? Onde fica no meu computador? O SKILL.md NÃO TEM seção de instalação. Pra um aluno que comprou curso de IA, isso é o primeiro tropeço.

Os gatilhos? "Convoca o conselho" é OK, soa dramático. Mas "war room isto", "stress-test isto", "pressure-test isto" — jargão de startup. Eu nunca falaria isso. Já "leva pro conselho" e "debate isso" são naturais.

Os nomes dos conselheiros? Acertaram. Contrário, Forasteiro, Executor, Expansionista — entendo de cara. Só "Pensador de Primeiros Princípios" me quebrou. Tem 4 palavras. Chama de "O Cético da Raiz" ou "O Que Pergunta Por Quê".

Exemplo do billing? ME PERDEU. "Microserviço", "monolito", "race conditions", "transações distribuídas", "boundary forte", "lint". Se o público inclui não-devs, esse exemplo grita "não é pra você". Troca por decisão de NEGÓCIO: "devo lançar produto X ou Y", "demito esse vendedor ou dou mais 3 meses".

Palavras que precisaria pesquisar: LLM, sub-agente, peer review, stress-test, pressure-test, war room, microserviço, monolito, race condition, boundary, lint, PRD, SLA, refactor, snippet, frontmatter.

A IDEIA é genial. Mas o documento foi escrito por programador pra programador.

---

## RESPOSTA E
Bem feita como artefato. Mas a pergunta certa não é "essa skill funciona?" — é "esse padrão deveria existir?".

Cerimônia ou substância? "As tensões são o produto — não as respostas individuais." Honesto. Mas se conflito estruturado é o produto, todo o resto (peer review anônimo, presidente, transcrição) é teatro caro pra forçar Claude a não convergir prematuramente. Você pode obter 80% disso com "me dê a versão mais hostil dessa decisão, depois a mais ambiciosa, depois resolva."

Múltiplas perspectivas paralelas vs cadeia única? Não há evidência de que 5 chamadas independentes batem 1 cadeia bem orquestrada do mesmo modelo. Karpathy usou modelos diferentes — diversidade real de pesos. A skill troca isso por "lentes de pensamento distintas no mesmo modelo". Downgrade epistêmico não reconhecido. Cinco Claudes 4.7 com prompts diferentes não são cinco mentes — são uma mente fingindo cinco vozes.

As 5 lentes são as certas? Contrário, Expansionista, Executor são lentes genuínas. Forasteiro e Primeiros Princípios se sobrepõem perigosamente — ambos questionam premissas, só mudam o sotaque. Faltam lentes que importam mais pro aluno: Lente do Cliente (quem paga?), Lente Temporal (e em 18 meses?), Custo de Oportunidade (o que NÃO está fazendo?). Escolha tem cara de framework de produto, não de epistemologia.

Peer review anônimo é independência real? Não. Mesmo modelo, mesmo training, mesma sessão, contexto compartilhado da pergunta enquadrada. Anonimização remove viés posicional — não remove correlação de erros.

O que o aluno realmente ganha? Processo forçado de não-convergência. Tem valor pra quem confia demais na primeira resposta. Mas o produto deveria ser vendido como "anti-viés-de-confirmação estruturado", não como "5 mentes". Honestidade epistêmica > marketing de framework.

Veredito: distribua, mas reescreva o frame.
