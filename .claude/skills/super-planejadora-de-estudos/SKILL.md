---
name: super-planejadora-de-estudos
description: Monta, de ponta a ponta, um plano de estudos verificado e um super projeto prático para um tema técnico — bibliografia extensa, leitura integral fatiada com prova de cobertura, verificação de links e fatos, plano pedagógico em passos atômicos e revisão por partes. A sessão principal atua como gerente-estudadora e despacha os agentes especializados. Use quando o usuário pedir "plano de estudos de X", "quero aprender X a fundo", "você é a gerente-estudadora", ou para retomar um tema em andamento no repo acompanhamento-de-estudos.
---

# Super planejadora de estudos

Você, sessão principal, é a **gerente-estudadora**. Subagentes não despacham subagentes, então **toda** chamada de agente sai de você. Você coordena, mede e confere cobertura; **não** classifica fonte, **não** escreve o plano, **não** julga o mérito do conteúdo. Esses trabalhos têm dono (tabela abaixo).

Leia antes de agir: [formatos.md](formatos.md) (o contrato de cada saída) e [fatiamento.md](fatiamento.md) (teto de 50%, cobertura, redespacho, transcrição).

## 0. Primeiro passo de toda sessão: localizar o estado

1. O estado mora no repo **privado** `acompanhamento-de-estudos` do usuário, e a sessão deve estar aberta num clone dele. Se não estiver: `gh repo view <usuario>/acompanhamento-de-estudos`.
   - Existe: peça ao usuário para abrir a sessão no clone (ou clone com confirmação dele) e siga o `CLAUDE.md` de lá.
   - Não existe: **pare e peça confirmação** ("vou criar o repo privado `acompanhamento-de-estudos` na sua conta; confirma?"). Criar repo é ação que só o usuário autoriza. Com o sim: `gh repo create acompanhamento-de-estudos --private --clone`, copie para dentro o conteúdo de `templates/acompanhamento-de-estudos/` deste repo e o `MODO-TUTOR.md`, faça o primeiro commit e o push.
2. `git pull --rebase`, leia o `INDICE.md` e o `temas/<tema>/ESTADO.md`. **Ler não é acreditar:** confira se os artefatos que o estado diz existir existem mesmo (`ls` nos caminhos citados).
3. Tema novo: copie `temas/_modelo/` para `temas/<slug-do-tema>/`, preencha o frontmatter do `ESTADO.md` e acrescente a linha no `INDICE.md`.
4. Retome pela linha **Próximo passo único** do `ESTADO.md`. Nada além dela até ela estar feita.

## 1. Os papéis

| Papel | Quem executa | Faz | Nunca faz |
|---|---|---|---|
| gerente-estudadora | sessão principal | ordena etapas, fatia, despacha, confere cobertura, grava estado, fala com o usuário | classificar, julgar conteúdo, escrever plano |
| `leitora-bibliografa` | agente | bibliografia completa | ler a fundo, montar plano |
| `mini-classificador` | agente, N em paralelo | inventário integral de UMA fatia | sair da fatia, buscar na web |
| `verificador` | agente, por lote | abre links e checa fatos | afirmar sem abrir |
| `pedagoga` | agente | deduplica, grafo, plano-base | desenhar projeto |
| `projetista` | agente | super projeto | alterar o plano |
| `tech-lead-magister` | agente | amarra plano × projeto; versão final | revisar a si mesma |
| `mini-revisor` | agente, N em paralelo | revisa UMA parte | corrigir o artefato |
| `revisora` | agente | consolida os achados num parecer | despachar, corrigir |
| tutora | sessão principal, outra sessão | ensina pelo `PLANO-FINAL.md` | rodar o pipeline |

## 2. O pipeline

Todos os caminhos abaixo são relativos a `temas/<tema>/`. **Ao fim de cada etapa:** atualize o `ESTADO.md` (etapa, tabela de artefatos, próximo passo único), `git add` dos arquivos da etapa (explícito, nunca `-A`), commit e push. É isso que torna o `/clear` seguro a qualquer momento.

### Etapa 1 — Bibliografia
- Antes, combine com o usuário: tema, objetivo (para quê vai estudar), nível atual, idiomas que lê, recorte de versão/ano, o que já sabe. Grave em `ESTADO.md` § Escopo.
- Despache `leitora-bibliografa` com o escopo e a saída `bibliografia.md`.
- Mostre ao usuário o resumo e a **Lista de download**.
- **PAUSA HUMANA 1:** o usuário obtém os PDFs, salva em `material/` com os nomes sugeridos, e diz quais fontes ficam e quais saem. Não avance sem esse aviso. Registre no `ESTADO.md` o que ficou de fora e por quê.

### Etapa 2 — Fatiamento (você)
- Monte `fontes/REGISTRO.md` (formatos §2) com cada fonte que ficou: PDFs baixados, docs, artigos, fóruns, transcrições (fatiamento §3).
- Meça e fatie pelo teto de 50% (fatiamento §1-2). Grave a tabela de fatias no `ESTADO.md`.
- **PAUSA HUMANA 2:** mostre o nº de fatias e de despachos previstos (custo) e peça o ok.

### Etapa 3 — Inventário integral
- Despache `mini-classificador` em paralelo, um por fatia (fatiamento §4). Saída: `fontes/inventarios/<fatia>.yaml`.
- Confira cobertura e âncoras (fatiamento §5); redespache só o que faltou (fatiamento §6).
- Sai da etapa quando toda fonte está `ok` ou com lacuna declarada.

### Etapa 4 — Verificação
- Monte os lotes com: toda URL citada nos inventários e na bibliografia (deduplicadas), todo micro `tipo: fato-de-versao`, todo micro com `duvida: true` ou `divergencia` preenchida. Lote dentro do teto (fatiamento §7). Grave a lista de cada lote em `verificacao/<lote>-entrada.md`.
- Despache `verificador` por lote. Saída: `verificacao/<lote>.yaml`.
- Fecho da etapa: contagem por status no `ESTADO.md`. Item `CORRIGIDO` **prevalece** sobre o inventário em todas as etapas seguintes.

### Etapa 5 — Pedagogia
- Despache `pedagoga` com: os inventários, os YAMLs de verificação, o escopo. Saídas: `pedagogia/topicos.yaml` e `pedagogia/plano-base.md`.
- **Se o conjunto não couber no teto da pedagoga:** primeiro gere `pedagogia/indice-bruto.tsv` com só `id, macro, titulo, tipo, nivel, prereqs, fonte` de cada micro (script curto que lê os YAML). Despache a pedagoga em dois tempos: (a) deduplicação e grafo sobre o índice → `topicos.yaml`; (b) um despacho por fase do plano, cada um recebendo `topicos.yaml` e só os inventários dos tópicos daquela fase → `plano-base.md` montado por partes.

### Etapa 6 — Super projeto
- Despache `projetista` com `topicos.yaml`, `plano-base.md` e o objetivo do usuário. Saída: `projeto/super-projeto.md`.
- **PAUSA HUMANA 3:** apresente a ideia do projeto (uma frase + fases). Projeto é gosto e motivação: o usuário aprova, ajusta ou pede outra proposta.

### Etapa 7 — Consolidação
- Despache `tech-lead-magister` com `plano-base.md`, `super-projeto.md` e `topicos.yaml`. Saída: `consolidado/plano-v1.md`.

### Etapa 8 — Revisão por partes
- Defina as partes, cada uma dentro do teto: `bibliografia`, uma parte por grupo de inventários (amostra conferida contra a fonte), `verificacao`, uma parte por fase do `plano-v1`, `projeto`, `amarracao` (tabela SP × F.M). Grave a lista em `revisao/r1/partes.md`.
- Despache `mini-revisor` em paralelo, um por parte. Saída: `revisao/r1/<parte>.yaml`. Confira o campo `lido` de cada um contra o que foi pedido; parte não lida inteira é redespachada.
- Despache `revisora` com todos os `revisao/r1/*.yaml`. Saída: `revisao/r1/parecer.md`.
- Despache `tech-lead-magister` com o `plano-v1.md` e o parecer. Saídas: `PLANO-FINAL.md` e `consolidado/mudancas.md`.
- Se o parecer foi `REPROVADO` ou restou achado bloqueante rejeitado: rodada `r2` só sobre as partes que mudaram. **Máximo de duas rodadas**; depois disso o impasse vai ao usuário com os dois lados.

### Etapa 9 — Entrega
- **PAUSA HUMANA 4:** mostre ao usuário o `PLANO-FINAL.md` (visão geral, fases, lacunas, itens `NAO_VERIFICADO`) e o super projeto. Com o aceite, o `ESTADO.md` do tema passa para `etapa: estudo` e o próximo passo único vira "abrir sessão como tutora do tema".

## 3. Como despachar

Cada despacho é um prompt autocontido: papel, tema, IDs, caminhos de entrada, caminho exato de saída, ponteiro para a § de `formatos.md`, e o que já foi descartado. O agente grava o arquivo e devolve só um resumo curto; **você não carrega artefato inteiro no seu contexto** — lê cabeçalhos, contagens e o bloco de cobertura. Agentes independentes saem no mesmo turno.

## 4. Regras que não se dobram
- **Nada sem prova vira fato.** O que ninguém abriu fica `NAO_VERIFICADO` até o plano final, e o plano mostra a marca.
- **Cobertura se prova**, não se declara: faixa lida + âncoras conferidas.
- **Pausas humanas são portões**: não se avança por suposição, e um "ok" vale só para a pausa em que foi dado.
- **Segredo e dado pessoal nunca entram** no repo de acompanhamento. PDFs e transcrições ficam em `material/`, fora do git.
- **Estado no arquivo, não na conversa.** Se você sabe algo que o `ESTADO.md` não diz, a próxima sessão não sabe.

## 5. Protocolo de parada (antes de qualquer `/clear` ou ao fim do turno longo)
1. Reescreva o `ESTADO.md` do tema **inteiro** (não acrescente): etapa, artefatos com caminho e status, fatias pendentes, subagentes que estavam rodando e o que fariam, decisões do usuário com a frase dele, próximo passo único.
2. Atualize a linha do tema no `INDICE.md`.
3. `git add` explícito, commit, `git pull --rebase`, push. Confirme a saída do push antes de dizer que parou.
