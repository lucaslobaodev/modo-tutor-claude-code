# Construir o roadmap consolidado

O estado desta construção mora em `roadmap/PLANO.md` § Estado — atualize-o ao fim de cada fase e antes de parar.

## F0 · Material (do aluno)
O aluno baixa os livros da lista (`auditoria-roadmaps/RE-DOWNLOAD.md` e `auditoria-roadmaps/unidades/livros-*.txt`) para `temas/<frente>/material/`. Edição diferente serve. Você confere o disco (`ls temas/*/material/`), marca o que chegou e mantém `roadmap/PLANO.md` § Faltam baixar. **Não espere a lista inteira**: livro da lista ainda não baixado entra no mapa e no roadmap pelo sumário que já existe, marcado `não baixado`; o tópico pode passar no portão assim, e o PDF só é pedido quando a unidade chegar à vez de ensinar (decisão do aluno, 2026-11-10). Mantenha `biblioteca/` (hardlinks de todo `temas/*/material/`, fora do git) em dia ao triar.

## F1 · Sumários
- Cada livro no disco tem sumário em `auditoria-roadmaps/sumarios/<id>.md` (índice: `_RESUMO.tsv`). Livro novo sem sumário: extraia o sumário do PDF (páginas iniciais, `Read` com `pages`) e grave no mesmo formato. Registre o offset página impressa → página do PDF no topo.
- `sem-sumario`: monte o sumário pelos títulos de capítulo do próprio texto.

## F2 · Mapa de assuntos (a consolidação)
- Para cada capítulo de cada sumário, atribua **um assunto** (e, se o capítulo for largo, divida por seção). Grave em `roadmap/mapa/<topico>.tsv`: `assunto · livro · capítulo/seção · páginas · nível (intro|canônico|avançado|referência)`.
- **Mesmo assunto = mesmo nome**, mesmo vindo de frentes diferentes. Antes de criar um assunto novo, procure um existente em `roadmap/ASSUNTOS.md` (a lista mestra: tópico → assuntos, com sinônimos).
- Capítulo de livro **todo coberto por outro mais fundo** continua no mapa (é a visão alternativa ou a porta de entrada); nada é descartado em silêncio. Fora do escopo do estudo: marque `fora` com o motivo.
- Faça F2 por tópico, com subagente `leitor` por lote de livros quando o volume passar de ~15 livros; você confere a amostra e unifica os nomes.

## F3 · Roadmap do tópico
`roadmap/<topico>/ROADMAP.md`, gerado do mapa:
- **Módulos** = assuntos (ou grupos de assuntos pequenos), em ordem de pré-requisito.
- **Unidades** = cortes de 2–4 h de estudo dentro do módulo. Cada unidade lista:
  - objetivo em uma frase, no nível de domínio ("explica e implementa X; sabe quando X falha");
  - **capítulos de TODOS os livros do mapa para esse assunto**, ordenados intro → avançado, com papel (`base`, `aprofunda`, `referência`, `outra visão`), arquivo e páginas;
  - pré-requisitos (unidades);
  - exercícios dos livros disponíveis (capítulo e números) — insumo do Testar.
- Unidade com um livro só é aceita só quando o mapa não tem outro; diga isso na unidade (`fonte única`).

- **Mapa × sumário:** a cobertura só vale se o mapa tiver todo o sumário. `python3 auditoria-roadmaps/scripts/lacunas_do_mapa.py` lista as entradas de sumário (níveis 0–1) sem linha no mapa; cada uma vira linha do mapa (conteúdo, com tópico/módulo da `TRIAGEM-MODULOS.md`) ou entra em `roadmap/DESCARTES-SUMARIO.tsv` com motivo (prefácio, índice, exercícios, resumo, referências, vazio, estrutura). Meta: 0 lacunas. Rode depois de mapear qualquer livro novo.
- **Cobertura (decisão do aluno, 2026-11-10): 100% dos capítulos de todos os livros, cada assunto numa unidade só.** Cite nas unidades o título do capítulo **como está no mapa** (o script casa por título). Depois de montar ou editar o módulo, rode `python3 auditoria-roadmaps/scripts/cobertura.py` (gera `roadmap/COBERTURA.md` e `.tsv`). O módulo só vai ao portão com **0 pendentes** (todo capítulo do mapa daquele módulo está em alguma unidade), **0 repetidos** (capítulo em 2+ unidades) e **0 citações órfãs**. Capítulo de outro módulo puxado para cá aparece em "Capítulos de outros módulos já cobertos aqui": quando montar o módulo dele, não o repita.
- **Varredura cruzada (decisão do aluno, 2026-11-10: "não quero depois ter que revisar todos os livros em busca de temas e capítulos"):** o mapa classificou cada capítulo num módulo só, e às vezes erra o módulo. Antes do portão, rode `python3 auditoria-roadmaps/scripts/varredura_cruzada.py "<módulo>" <termos do assunto>` (termos em inglês e português, os do título dos capítulos) e decida cada candidato: **puxa** (cita na unidade, mesmo vindo de outro tópico) ou **fica** no módulo dele por ser outro assunto. Registre no ROADMAP.md, antes do módulo seguinte, um parágrafo "Varredura cruzada do Mn" com o que foi puxado e o que ficou (e para onde). Módulo sem esse parágrafo não vai ao portão.
- Livro baixado depois: entra no mapa (remapear o livro) e a cobertura aponta os capítulos novos como pendentes no módulo dele.

### Regras de montagem que a revisão de Arquitetura (2026-11-10) mostrou necessárias
Cada regra abaixo nasceu de um erro real achado pela revisora; não pule nenhuma.
1. **Página conferida no PDF, não só no sumário.** Toda citação "capítulo + página" passa por `python3 auditoria-roadmaps/scripts/verifica_paginas.py roadmap/<tópico>/ROADMAP.md` (abre a página no PDF e confere o título; `--corrigir` aplica). Meta: 0 erradas. Página é sempre a **do PDF**: sumário impresso (TAOCP, Smith & Nair) dá página impressa — medir o deslocamento e converter. Livro no disco não leva "?". Livro escaneado sem texto: conferir com OCR (tesseract) e registrar o deslocamento.
2. **Livro-texto grande é citado por SEÇÃO, nunca pelo capítulo inteiro** (H&P, COD, CS:APP, MOS, OSPP e afins): capítulo inteiro numa unidade esconde seções de outro assunto (ex.: H&P cap. 5 inteiro na coerência levou junto sincronização e consistência). Se o mapa tem só o capítulo, mapeie as seções antes (BRIEF4) e distribua cada seção a uma unidade só.
3. **A base ensina cada verbo do objetivo.** Antes de fechar a unidade, confira no texto da base que cada coisa prometida no objetivo está lá; se não está, troque a base (ex.: U7.5 prometia preditores de 2 bits e a base era `__builtin_expect`).
4. **Base de unidade não pode ser livro não baixado.** Livro fora do disco entra só como `aprofunda`/`outra visão`; se ele é a única base, a unidade fica marcada "abrir só depois de baixar" e o livro vai para o topo da lista do aluno.
5. **Assunto de módulo posterior não vira dependência.** "Outra visão" que usa algo ensinado depois leva `(adiar até Uxx)`; a unidade não depende dela. Promessa de referência cruzada ("volta no módulo X", "fica no módulo Y") é conferida por grep no ROADMAP antes do commit — o capítulo tem de estar lá.
6. **Rejulgar o que o mapa mandou embora.** Na varredura cruzada, além dos termos, abra `fora-do-topico.md` (capítulos dos livros do tópico que o mapa pôs em outro tópico) e julgue pelo CONTEÚDO, com o sumário da seção ao lado (ex.: COD 6.2, 4.9 e 5.7 e MOS 1.3, 5.1 e 9.6 eram de arquitetura e estavam em SO/Segurança).
7. **Checklist de currículo.** Ao fechar o tópico, compare as unidades com um currículo de referência do assunto (ex.: ACM/IEEE CS2023 para os tópicos de computação) e liste o que falta: assunto sem unidade → procurar capítulo no acervo; sem fonte → "sem fonte no acervo" (ex.: faltavam E/S no hardware, TLB e segurança microarquitetural).
8. **NAO_VERIFICADO é específico.** O texto diz o que não foi verificado e por quê, para AQUELA citação; não copie a justificativa de uma unidade para outra.
12. **Título e página se copiam do mapa, nunca de memória** (erro da própria tutora no Linux M8, 2026-11-10: números de página do How Linux Works digitados de cabeça). Para escrever uma linha de unidade, copie o capítulo do `roadmap/mapa/*.tsv` (ou do `COBERTURA.tsv`) e a página do PDF conferida; se o título do mapa traz pontilhado e número do sumário impresso, corte o pontilhado inteiro (o casamento é por prefixo). Citação órfã no `cobertura.py` é sinal disso.
10. **Sumário plano também é conferido** (achado na montagem de Linux M6, 2026-11-10): `lacunas_do_mapa.py` pulava sumários sem árvore e escondeu 554 entradas (ex.: SSH Mastery caps. 0 e 15). Hoje toda linha de sumário plano conta como nível 0. Se aparecer outro formato de sumário, confira que o script o lê antes de confiar no "0 lacunas".
11. **Livro central de um assunto não é repartido pelo mapa sem razão de conteúdo**: se o mapa espalhou capítulos do livro-base do módulo por outros tópicos (ex.: SSH Mastery entre Linux, Segurança e Redes), traga de volta os que ensinam o assunto do módulo e deixe nos outros tópicos só o que é de fato de lá.
9. **Contagens e rótulos verdadeiros.** Cabeçalhos ("Livros dos módulos M1–Mn", nº de unidades) e o rótulo de edição batem entre ROADMAP, mapa e catálogo.

### Regras que a revisão de Linux (2026-11-10) mostrou necessárias
13. **Livro-base inteiro ou justificado, conferido ao fechar cada módulo** (a varredura por termos não acha assunto sem módulo com aquele nome: o capítulo de processos do MUS, o TLCL 10 e o PSL 8 ficaram fora do Linux e nenhuma unidade ensinava `ps`/`kill`/sinais). Para cada livro-base do tópico, liste por script todas as seções/capítulos (`COBERTURA.tsv` filtrado pelo arquivo) e exija que cada um esteja numa unidade do tópico ou tenha a razão de ficar fora escrita no parágrafo de varredura. Assunto do dia a dia de um sênior sem unidade = unidade nova.
14. **Deslocamento de página medido em vários pontos, não num só**; em livro de OCR ruim, cada citação é aberta e casada com o título. Página interna (cabeçalho corrido "NN Título-do-livro" no topo) nunca é abertura de capítulo. Quando o título casa primeiro na lista "In this chapter, we will cover" da abertura do capítulo, a seção é a ocorrência seguinte, como cabeçalho (erro do MUS "Locking down sudo", p.498 → 539).
15. **Afirmação sobre exercícios só por contagem no texto, capítulo a capítulo** (`pdftotext | grep -c "Exercise"`); sem contagem → NAO_VERIFICADO. Nunca generalizar de um capítulo para o livro (erros: AWK "120 exercícios" eram 83 e os caps. 1–2 não têm; o TLCL não tem lista de exercícios).
16. **"Aprovado em" só no commit que registra o aval do aluno**; cabeçalho de módulos e lista de estado atualizados no mesmo commit (montar ≠ aprovar).
17. **Repetição por assunto, não só por capítulo**: antes de apresentar o módulo, cruze os assuntos do objetivo (grep de termos-chave: FHS, links, timer…) com os objetivos das unidades já escritas; o assunto fica numa unidade só, e a outra fonte vira "outra visão" lá.
18. **Pré-requisito por unidade e entre tópicos**: cada unidade que usa algo (rsync, sinais, C, grafos) aponta onde aquilo é ensinado; se é depois no mesmo tópico, reordene ou mova; se é em outro tópico, declare. **Uma unidade = um assunto**: capítulo que sobra vira unidade própria ou entra como "anexo declarado".

### Revisão de fim de tópico (obrigatória; decisão do aluno, 2026-11-10)
Tópico só é dado como concluído depois desta revisão — e erro achado nela vira regra nesta seção (corrigir na raiz, não só o sintoma).
1. `python3 auditoria-roadmaps/scripts/cobertura.py` (0 pendentes/repetidos/órfãs), `lacunas_do_mapa.py` (0) e `verifica_paginas.py` (0 erradas).
2. `python3 auditoria-roadmaps/scripts/revisao_topico.py <pasta> <termos do tópico>` → gera `fora-do-topico.md`, `varredura-ampla.md` e o brief (`roadmap/_modelos/BRIEF-REVISAO-TOPICO.md`).
3. Despache a **revisora em contexto limpo, modelo de julgamento (opus)**, com o brief; ela amostra citações no PDF e procura A faltou encaixar · B assunto sem unidade · C lugar errado · D repetição · E ordem · F citação errada · G afirmação sem prova, e escreve "Causas".
4. Corrija todos os achados (ou registre por que não), rode o passo 1 de novo, e **leve cada Causa para as regras acima**. Só então o tópico vai ao aluno como concluído.

- **HTML único para o aluno (decisão de 2026-11-10):** depois de montar ou corrigir qualquer módulo, rode `cobertura.py` e `python3 auditoria-roadmaps/scripts/roadmap_html_geral.py` — gera `roadmap/ROADMAP.html` com TODOS os tópicos (índice lateral, estado de cada módulo, cobertura por tópico, busca). É esse arquivo que o aluno abre para aprovar.

## F4 · Portão e ordem
- Mostre ao aluno o ROADMAP do tópico (módulos, nº de unidades, livros que entraram e quais deles estão `não baixado`). Aprovado → entra na `roadmap/ORDEM.md` (ordem global, intercalando tópicos por pré-requisito), commit.
- Livro baixado depois: volte a F1–F3 só nos assuntos dele e registre em `roadmap/MUDANCAS.md`; unidade já estudada não se reabre, ganha "aprofundar depois" no PROGRESSO.

## Insumos antigos (não refazer o que presta)
- `auditoria-roadmaps/sumarios/` (448 sumários), `temas/*/fontes/inventarios/` (leituras verificadas), `auditoria-roadmaps/unidades/montadas/` (U00–U18): servem de insumo. As U antigas foram montadas para "aluno do zero" com 1 livro: **não se copiam**, se refazem pelo F3.
- O `estudo-antecipado` (U00–U18) está encerrado desde 2026-11-09; o que o aluno fechou lá entra no PROGRESSO novo como "já visto" (revisão, não reestudo).
