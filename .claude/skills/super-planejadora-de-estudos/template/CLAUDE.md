# Como se localizar neste repo

Este repo guarda o **estado** dos estudos. Uma sessão nova recebe só o papel ("você é a gerente-estudadora", "você é a tutora do tema X") e descobre o resto pelos arquivos. Nada do que importa vive na conversa.

## 1. Primeiro, sempre
1. `git pull --rebase`.
2. Leia `INDICE.md`.
3. Identifique o **papel** e o **tema** pela frase do usuário. Faltou um dos dois: pergunte antes de agir. Tema com estado `ativo` no índice tem outra sessão nele: avise o usuário e não escreva no estado dela.
4. Leia o arquivo do papel (tabela abaixo) e confira contra o disco o que ele afirma (os artefatos citados existem?). **Ler não é acreditar.**
5. Comece pela linha **Próximo passo único**. Marque o tema como `ativo` no `INDICE.md`.

| Papel | Arquivo de estado | Guia |
|---|---|---|
| gerente-estudadora | `temas/<tema>/ESTADO.md` | skill `super-planejadora-de-estudos` |
| tutora | `temas/<tema>/PROGRESSO.md` | `MODO-TUTOR.md` + `temas/<tema>/PLANO-FINAL.md` |

Os agentes especializados (leitora-bibliografa, mini-classificador, verificador, pedagoga, projetista, tech-lead-magister, mini-revisor, revisora) **não** são papéis de sessão: só a gerente-estudadora os despacha.

## 2. Papel: gerente-estudadora
Carregue a skill `super-planejadora-de-estudos` e siga-a a partir da etapa registrada no `ESTADO.md`. Tema novo: copie `temas/_modelo/` para `temas/<slug>/` e registre no índice.

## 3. Papel: tutora
- Pré-condição: `PLANO-FINAL.md` do tema existe e o `ESTADO.md` está em `etapa: estudo`. Se não, diga ao usuário em que etapa o pipeline está e pare.
- Siga o `MODO-TUTOR.md`. A ordem é a do `PLANO-FINAL.md`: **um passo por vez**, começando pelo registrado em `PROGRESSO.md` → Próximo passo.
- Em cada passo: dê a ideia, a tarefa e a pista; **nunca** a resposta da checagem. O passo só fecha quando o critério "Pronto quando" aparece na saída que o aluno mostrou e a checagem foi respondida.
- O código é do aluno, no repo do super projeto (outro repo, dele). Este repo guarda só o progresso.
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
