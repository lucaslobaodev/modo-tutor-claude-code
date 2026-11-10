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

- **Cobertura (decisão do aluno, 2026-11-10): 100% dos capítulos de todos os livros, cada assunto numa unidade só.** Cite nas unidades o título do capítulo **como está no mapa** (o script casa por título). Depois de montar ou editar o módulo, rode `python3 auditoria-roadmaps/scripts/cobertura.py` (gera `roadmap/COBERTURA.md` e `.tsv`). O módulo só vai ao portão com **0 pendentes** (todo capítulo do mapa daquele módulo está em alguma unidade), **0 repetidos** (capítulo em 2+ unidades) e **0 citações órfãs**. Capítulo de outro módulo puxado para cá aparece em "Capítulos de outros módulos já cobertos aqui": quando montar o módulo dele, não o repita.
- **Varredura cruzada (decisão do aluno, 2026-11-10: "não quero depois ter que revisar todos os livros em busca de temas e capítulos"):** o mapa classificou cada capítulo num módulo só, e às vezes erra o módulo. Antes do portão, rode `python3 auditoria-roadmaps/scripts/varredura_cruzada.py "<módulo>" <termos do assunto>` (termos em inglês e português, os do título dos capítulos) e decida cada candidato: **puxa** (cita na unidade, mesmo vindo de outro tópico) ou **fica** no módulo dele por ser outro assunto. Registre no ROADMAP.md, antes do módulo seguinte, um parágrafo "Varredura cruzada do Mn" com o que foi puxado e o que ficou (e para onde). Módulo sem esse parágrafo não vai ao portão.
- Livro baixado depois: entra no mapa (remapear o livro) e a cobertura aponta os capítulos novos como pendentes no módulo dele.

## F4 · Portão e ordem
- Mostre ao aluno o ROADMAP do tópico (módulos, nº de unidades, livros que entraram e quais deles estão `não baixado`). Aprovado → entra na `roadmap/ORDEM.md` (ordem global, intercalando tópicos por pré-requisito), commit.
- Livro baixado depois: volte a F1–F3 só nos assuntos dele e registre em `roadmap/MUDANCAS.md`; unidade já estudada não se reabre, ganha "aprofundar depois" no PROGRESSO.

## Insumos antigos (não refazer o que presta)
- `auditoria-roadmaps/sumarios/` (448 sumários), `temas/*/fontes/inventarios/` (leituras verificadas), `auditoria-roadmaps/unidades/montadas/` (U00–U18): servem de insumo. As U antigas foram montadas para "aluno do zero" com 1 livro: **não se copiam**, se refazem pelo F3.
- O `estudo-antecipado` (U00–U18) está encerrado desde 2026-11-09; o que o aluno fechou lá entra no PROGRESSO novo como "já visto" (revisão, não reestudo).
