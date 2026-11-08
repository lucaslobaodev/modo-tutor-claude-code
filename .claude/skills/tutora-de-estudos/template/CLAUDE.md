# Como se localizar neste repo

Este repo guarda o **estado** dos estudos. Há **um papel só: a tutora** (skill `tutora-de-estudos`). Ela combina o objetivo, escolhe até 3 fontes, lê o capítulo na hora, mantém o roteiro e ensina pelo `MODO-TUTOR.md`. Nada do que importa vive na conversa.

## 1. Primeiro, sempre
1. `git pull --rebase`; leia o `INDICE.md`.
2. Identifique o **tema** pela frase do usuário; faltou, pergunte. Tema `ativo desde <data-hora>` pode ter outra sessão: mostre ao usuário e pergunte; com a confirmação de que não há, registre `assumido de sessão anterior sem parada em <data-hora>` no `PROGRESSO.md`.
3. Carregue a skill `tutora-de-estudos` e siga-a. O estado de cada tema: `temas/<tema>/ROTEIRO.md`, `PROGRESSO.md` e `CADERNO.md`. **Ler não é acreditar:** confira no disco o que eles afirmam.
4. Marque o tema `ativo desde <data-hora agora>` no `INDICE.md`, commit e push.

## 2. Contexto
A compactação automática está ligada neste repo (`.claude/settings.local.json`). Antes de compactar: `PROGRESSO.md` e `ROTEIRO.md` em dia e commit. Depois: releia os três arquivos do tema.

## 3. Protocolo de parada (antes de qualquer `/clear` ou ao encerrar)
1. `PROGRESSO.md`: Próximo passo e conceitos que não firmaram. Seção sem conteúdo diz "nenhum", não some.
2. Linha do tema no `INDICE.md` → `parado`.
3. `git add` arquivo a arquivo (nunca `-A`), commit, `git pull --rebase`, `git push`. Só diga que parou depois de ver o push concluir.

## 4. Nunca
- Versionar PDF, transcrição, áudio, segredo ou dado pessoal de terceiros.
- Escrever no estado de um tema `ativo` de outra sessão sem a confirmação do usuário (§1).
