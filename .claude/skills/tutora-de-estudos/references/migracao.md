# Migração: tema do pipeline antigo para o loop da tutora única

Até 08/11/2026 os temas nasciam no pipeline da `super-planejadora-de-estudos` (9 etapas, 8 agentes). Ele foi substituído por esta skill por decisão do usuário (*"A mesma que lê, tria, monta o plano de ensino e ensina propriamente"*; *"Pode aplicar 100% os planos pra mudança da skill estudadora"*). O material produzido por ele **fica**: nada se apaga; muda o que é pré-requisito.

## Qual é o caso do tema

| O tema tem | Caso | O que fazer antes de ensinar |
|---|---|---|
| `ROTEIRO.md` | já migrado | nada; siga a skill |
| `PLANO-FINAL.md` | plano pronto do pipeline | **A** |
| só bibliografia (`bibliografia.md`, `CONSOLIDADO-v1.md`, `fontes/`) | parado na etapa 1 | **B** |
| é o `estudo-antecipado` | unidades provisórias | **C** |

## A — tema com `PLANO-FINAL.md`
1. Gere o `ROTEIRO.md` a partir dos cabeçalhos do plano (`## F<nn>.M<n> — …` vira um módulo), com um ponteiro por módulo para a faixa de linhas dele no `PLANO-FINAL.md`.
2. **Os passos do plano antigo são insumo, não obrigação.** No passo 2 do loop, a tutora lê a seção do módulo no `PLANO-FINAL.md` (por faixa de linhas, nunca o arquivo inteiro: ele pode passar de 1 MB) e escolhe de 3 a 8 passos dali, adaptados ao aluno.
3. As fontes do Escopo são as que o módulo cita; mais de 3 no tema é herança do pipeline. Na primeira sessão, a tutora pergunta ao aluno qual é a espinha.
4. O `PROGRESSO.md` continua: o Próximo passo antigo (`F01.M1.P1`) vale como o primeiro passo do módulo `F01.M1`.

## B — tema parado na bibliografia
1. A abertura (§1 da skill) escolhe até 3 fontes **da bibliografia que já existe**, sem busca nova.
2. Os inventários em `fontes/inventarios/`, se houver, são consulta: a tutora pode usá-los para achar o capítulo, nunca como etapa a completar.
3. O `ESTADO.md` do pipeline fica como histórico; o estado passa a ser `ROTEIRO.md` + `PROGRESSO.md` + `CADERNO.md`.

## C — o estudo antecipado
Continua como está (`auditoria-roadmaps/unidades/`): a ordem em `ORDEM.md`, as unidades em `montadas/`, o progresso e o caderno lá. Muda uma coisa: **a tutora corrige a unidade seguinte ela mesma**, registrando no `MUDANCAS.md`, em vez de esperar a gerente-estudadora, que deixou de existir.

## O corte do pipeline antigo
Feito na migração de 08/11/2026, com nenhuma sessão viva (confirmado pelo usuário: *"Eu fechei todas as outras sessões"*): a skill antiga e os 8 agentes `estudos-*` foram para `arquivo/` no repo da skill (fora de `.claude/`, então não carregam), e o `CLAUDE.md` do acompanhamento passou a ter um papel só. Para consultar como era: `arquivo/super-planejadora-de-estudos/SKILL.md`.
