# Como se localizar neste repo

Este repo guarda o **estado** dos estudos. Uma sessão nova recebe só o papel ("você é a gerente-estudadora", "você é a tutora do tema X") e descobre o resto pelos arquivos. Nada do que importa vive na conversa.

## 1. Primeiro, sempre
1. `git pull --rebase`.
2. Leia `INDICE.md`.
3. Identifique o **papel** e o **tema** pela frase do usuário. Faltou um dos dois: pergunte antes de agir. Tema com estado `ativo desde <data-hora>` no índice pode ter outra sessão nele: mostre a data-hora ao usuário e pergunte. Se ele confirmar que não há outra sessão (por exemplo, a anterior caiu sem o protocolo de parada), assuma o tema e registre no arquivo de estado `assumido de sessão anterior sem parada em <data-hora>`. Sem essa confirmação, não escreva no estado dela.
4. Leia o arquivo do papel (tabela abaixo) e confira contra o disco o que ele afirma (os artefatos citados existem?). **Ler não é acreditar.**
5. Comece pela linha **Próximo passo único** (gerente) ou **Próximo passo** (tutora). Marque o tema como `ativo desde <data-hora agora>` no `INDICE.md`, commit e push.

| Papel | Arquivo de estado | Guia |
|---|---|---|
| gerente-estudadora | `temas/<tema>/ESTADO.md` | skill `super-planejadora-de-estudos` |
| tutora | `temas/<tema>/PROGRESSO.md` | `MODO-TUTOR.md` + `temas/<tema>/PLANO-FINAL.md` |

Os agentes especializados (`estudos-leitora-bibliografa`, `estudos-mini-classificador`, `estudos-verificador`, `estudos-pedagoga`, `estudos-projetista`, `estudos-tech-lead-magister`, `estudos-mini-revisor`, `estudos-revisora`) **não** são papéis de sessão: só a gerente-estudadora os despacha.

## 2. Papel: gerente-estudadora
Carregue a skill `super-planejadora-de-estudos` e siga-a a partir da etapa registrada no `ESTADO.md`. Tema novo: copie `temas/_modelo/` para `temas/<slug>/` e registre no índice.

## 3. Papel: tutora
- Pré-condição: `PLANO-FINAL.md` do tema existe e o `ESTADO.md` está em `etapa: estudo`. Se não, diga ao usuário em que etapa o pipeline está e pare.
- Siga o `MODO-TUTOR.md`. A ordem é a do `PLANO-FINAL.md`: **um passo por vez**, começando pelo registrado em `PROGRESSO.md` → Próximo passo. Se ele ainda tiver o texto do modelo (`<ID do passo ...>`), comece pelo primeiro passo do `PLANO-FINAL.md` e grave-o lá. `repo_do_projeto` ainda `a criar`: o aluno cria o repo dele na fase SP1; a tutora anota a URL quando ele informar.
- Em cada passo: dê a ideia, a tarefa e a pista; **nunca** a resposta da checagem. O passo só fecha quando o critério "Pronto quando" aparece na saída que o aluno mostrou e a checagem foi respondida.
- O código é do aluno, no repo do super projeto (outro repo, dele). Lá valem as proibições do `MODO-TUTOR.md`: a tutora não edita arquivo nem roda comando, salvo comando de demonstração que o aluno peça explicitamente.
- **Este repo é da tutora:** aqui ela lê, escreve `PROGRESSO.md`/`INDICE.md` e roda `git pull`/`commit`/`push` sozinha (exceção declarada no `MODO-TUTOR.md`).
- Experimento: a hipótese é escrita pelo aluno **antes**; registre hipótese, comando, saída real resumida, resultado e lição. Número só entra se veio da execução.
- Achou erro no plano durante o estudo? Registre em `PROGRESSO.md` → Erros encontrados no plano. Corrigir o plano é trabalho da gerente-estudadora, não da tutora.

## 4. Protocolo de parada (todo papel, antes de qualquer `/clear`)
1. Reescreva **inteiro** o arquivo de estado do seu papel. Seção sem conteúdo diz "nenhum", não some.
2. Atualize a linha do tema no `INDICE.md` (estado `parado` ou `aguardando-usuario`).
3. `git add` dos arquivos alterados, um por um (nunca `-A`), commit com mensagem clara, `git pull --rebase`, `git push`. Só diga que parou depois de ver o push concluir.

## 5. Nunca
- Versionar PDF, transcrição, áudio, segredo ou dado pessoal de terceiros.
- Escrever no estado de um tema `ativo` de outra sessão.
- Avançar uma pausa humana sem a resposta do usuário.
