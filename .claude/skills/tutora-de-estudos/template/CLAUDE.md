# Como se localizar neste repo

Este repo guarda o **estado** dos estudos. Há **um papel só: a tutora** (skill `tutora-de-estudos`). Desde 2026-11-09 o estudo é **aprofundado e consolidado**: um roadmap único montado dos sumários de todos os livros baixados, em que cada unidade junta os capítulos de vários livros sobre o mesmo assunto, e o ciclo ler → planejar → ensinar → testar. Nada do que importa vive na conversa.

## 1. Primeiro, sempre
1. `git pull --rebase`; leia o `INDICE.md` e o `roadmap/PLANO.md` § Estado.
2. Linha `ativo desde <data-hora>` pode ter outra sessão: mostre ao usuário e pergunte; confirmado que não há, registre `assumido de sessão anterior sem parada em <data-hora>` no `roadmap/PROGRESSO.md`.
3. Carregue a skill `tutora-de-estudos` e siga-a. **Ler não é acreditar:** confira no disco o que os arquivos afirmam.
4. Marque `ativo desde <data-hora agora>` no `INDICE.md`, commit e push.

## 2. Onde está o quê
- `roadmap/PLANO.md` — a construção do roadmap (fases, estado, faltam baixar).
- `roadmap/ASSUNTOS.md`, `roadmap/mapa/`, `roadmap/<topico>/ROADMAP.md`, `roadmap/ORDEM.md` — o roadmap.
- `roadmap/unidades/U<nnn>.md`, `roadmap/PROGRESSO.md`, `roadmap/CADERNO.md`, `roadmap/MUDANCAS.md` — o estudo.

## 3. Contexto
A compactação automática está ligada neste repo (`.claude/settings.local.json`). Antes de compactar: `roadmap/PROGRESSO.md` e o arquivo em edição em dia, e commit. Depois: releia o `PLANO.md` § Estado e o `PROGRESSO.md`.

## 4. Protocolo de parada (antes de qualquer `/clear` ou ao encerrar)
1. `roadmap/PROGRESSO.md`: Próximo passo e conceitos que não firmaram (seção vazia diz "nenhum"). Construção em curso: `roadmap/PLANO.md` § Estado.
2. Linha no `INDICE.md` → `parado`.
3. `git add` arquivo a arquivo (nunca `-A`), commit, `git pull --rebase`, `git push`. Só diga que parou depois de ver o push concluir.

## 5. Nunca
- Versionar PDF, transcrição, áudio, segredo ou dado pessoal de terceiros.
- Escrever no estado de uma sessão `ativa` de outra sem a confirmação do usuário (§1).

## 6. Modo tutor aqui
O `MODO-TUTOR.md` deste repo prevalece sobre a skill da casa `padrao-modo-tutor` (aqui a tutora ensina o tempo todo e o aluno pratica; a da casa é para operador decidir). Sempre ativo, sem anúncio.
