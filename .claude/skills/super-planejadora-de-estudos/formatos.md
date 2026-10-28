# Formatos de troca entre os agentes

Contrato único: cada agente escreve **exatamente** o formato abaixo, no caminho que a gerente passou no despacho. A saída de uma etapa é a entrada da próxima; se um campo muda aqui, muda em todos os agentes que o citam.

Caminhos são relativos à pasta do tema no repo de acompanhamento: `temas/<tema>/`.

## Identificadores

| Coisa | Formato | Exemplo | Quem cria |
|---|---|---|---|
| Fonte | `S` + 2 dígitos | `S03` | leitora-bibliografa |
| Fatia | `<fonte>-f` + 2 dígitos | `S03-f07` | gerente (fatiamento) |
| Microtópico bruto | `<fatia>-t` + 2 dígitos | `S03-f07-t12` | mini-classificador |
| Tópico canônico (deduplicado) | `T` + 3 dígitos | `T041` | pedagoga |
| Passo do plano | `F<n>.M<n>.P<n>` | `F2.M3.P4` | pedagoga |
| Experimento | `F<n>.E<n>` | `F2.E3` | pedagoga |
| Fase do super projeto | `SP<n>` | `SP2` | projetista |
| Achado de revisão | `R-<parte>-<n>` | `R-plano-F2-07` | mini-revisor |

Um ID nunca é reaproveitado. Item removido fica com o ID "aposentado" na lista de mudanças, não some calado.

## 1. `bibliografia.md` (leitora-bibliografa)

Uma tabela por categoria, nesta ordem: livros, documentação oficial, artigos e blogs, fóruns e Q&A, cursos, vídeos, repositórios. Colunas:

| ID | Título | Autor / mantenedor | Ano / versão | Idioma | Tipo de acesso | Link | Por que entra | Aberto? |
|---|---|---|---|---|---|---|---|---|

- **Tipo de acesso**: `livre` · `PDF a obter pelo usuário` · `pago` · `transcrição` (vídeo).
- **Aberto?**: `sim` (a leitora abriu a URL e o conteúdo corresponde) · `não` (citado por busca, não aberto). Nunca `sim` sem ter aberto.
- Livros trazem ISBN quando a página aberta o mostra; sem página aberta, o campo fica `não visto`.
- Fecha com a seção **Lacunas da busca** (o que procurou e não achou) e **Lista de download** (só os PDFs que o usuário precisa obter, com o nome de arquivo sugerido: `S03-<slug>.pdf`).

## 2. `fontes/REGISTRO.md` (gerente, ao fatiar)

Uma linha por fonte que entra no pipeline, com a extensão total que a cobertura vai provar:

| Fonte | Arquivo ou URL | Unidade | Extensão total | Tamanho medido | Fatias |
|---|---|---|---|---|---|
| S03 | `material/S03-livro.pdf` | página impressa | pp. 1-432 (PDF = impressa + 26) | 1.1 M caracteres | S03-f01..f09 |
| S07 | https://exemplo.org/docs/ | seção | 14 seções (lista abaixo) | 210 k caracteres | S07-f01..f03 |
| S12 | `material/transcricoes/S12.txt` | minuto | 00:00-48:30 | 52 k caracteres | S12-f01 |

## 3. `fontes/inventarios/<fatia>.yaml` (mini-classificador)

```yaml
fatia: S03-f07
fonte: S03
cobertura:
  pedida: "pp. 118-137"          # o que a gerente mandou
  lida: "pp. 118-137"            # o que de fato foi lido do início ao fim
  nao_lida: []                   # faixas puladas, com motivo: ["pp. 130-131: figura sem texto"]
  ancoras:                       # prova de leitura: texto literal, curto, conferível
    inicio: "p. 118, 1º título: '8.1 ...'"
    fim: "p. 137, última frase: '...'"
macrotopicos:
  - nome: "Health checks"
    micro:
      - id: S03-f07-t01
        titulo: "HEALTHCHECK no Dockerfile"
        ref: "p. 121"                     # só página e/ou link; nada além disso
        tipo: conceito                    # conceito | procedimento | armadilha | fato-de-versao | opiniao-do-autor
        nivel: fundamento                 # fundamento | intermediario | avancado
        prereqs: [S03-f04-t03]            # IDs brutos, de qualquer fatia, ou texto livre entre aspas se não souber o ID
        exercicio: "Try it now, p. 122"   # ou null
        erro_comum: "achar que unhealthy reinicia o container"   # ou null
        versao: null                      # ex.: "Engine >= 25.0, segundo o autor (p. 13)"
        divergencia: null                 # ex.: "S07 diz o contrário em <link>"
        duvida: false                     # true = o classificador desconfia; vai para o verificador
```

Regras: um microtópico por ideia; `ref` sempre presente; `fato-de-versao` exige `versao`; nenhum campo inventado: se a fonte não diz, o valor é `null`.

## 4. `verificacao/<lote>.yaml` (verificador)

```yaml
lote: V02
itens:
  - alvo: S03-f07-t01            # ID do microtópico ou "url:<url>"
    afirmacao: "unhealthy não reinicia nada em engine único"
    status: CONFIRMADO          # CONFIRMADO | CORRIGIDO | NAO_VERIFICADO
    correcao: null              # obrigatório se CORRIGIDO
    url_final: null             # se a URL redirecionou ou foi trocada
    prova: "https://... (aberto em AAAA-MM-DD), trecho: '...'"   # obrigatório se CONFIRMADO ou CORRIGIDO
    motivo: null                # obrigatório se NAO_VERIFICADO: 403, anti-bot, paywall, sem fonte primária...
```

## 5. `pedagogia/topicos.yaml` e `pedagogia/plano-base.md` (pedagoga)

`topicos.yaml`: um item por tópico canônico, com `id`, `titulo`, `fundidos` (IDs brutos que viraram este), `frequencia` (nº de fontes distintas), `classe` (`central` se ≥ 3 fontes ou se for pré-requisito de ≥ 3 tópicos; `nicho` se 1 fonte; `apoio` no meio), `prereqs` (IDs `T`), `status_verificacao` (o pior status entre os fundidos).

`plano-base.md`: fases → módulos → passos. Cada passo:

```markdown
**F2.M3.P4 — <título curto>**  ·  tópicos: T041, T042
- Ideia: <uma ideia, 1-2 linhas>
- Tarefa: <uma tarefa prática que o aluno faz>
- Pronto quando: <o que aparece e prova que terminou>
- Checagem: <pergunta verificável; nunca "entendeu?">
- Pista: <onde procurar, sem a resposta>
- Fontes: <S03 p. 121> · <S07 link>   (só referência curta)
- Armadilha: <se houver>
```

Experimento: `F2.E3` com **hipótese** (o aluno escreve antes), **como provocar**, **o que observar**, **onde registrar**. O plano abre com a seção "Como ler", a legenda dos status de verificação e a lista de **Lacunas** (tópico central sem fonte boa).

## 6. `projeto/super-projeto.md` (projetista)

Objetivo em uma frase; arquitetura mínima; fases `SP1..SPn`, cada uma com: entregável observável, tópicos `T` que exercita, critério de pronto, o que fica de fora. Fecha com a **matriz de cobertura** (todo tópico `central` aparece em ≥ 1 fase `SP`; tópico não coberto é listado com motivo).

## 7. `consolidado/plano-vN.md` (tech-lead-magister)

O plano-base com cada módulo amarrado a uma fase `SP` (`Módulo F2.M3 → SP2`), a ordem final de estudo intercalando estudo e construção, e a tabela `SP × F.M` no topo.

## 8. `revisao/<rodada>/<parte>.yaml` (mini-revisor) e `revisao/<rodada>/parecer.md` (revisora)

```yaml
parte: plano-F2
rodada: r1
lido: "consolidado/plano-v1.md, linhas 210-480"   # prova de cobertura da revisão
achados:
  - id: R-plano-F2-01
    alvo: F2.M3.P4
    severidade: bloqueante       # bloqueante | importante | menor
    criterio: "pergunta de checagem entrega a resposta"
    evidencia: "trecho literal"
    sugestao: "..."
```

`parecer.md`: achados deduplicados e ordenados por severidade, cada um com os IDs `R-` de origem, e um veredito: `APROVADO` · `APROVADO COM AJUSTES` · `REPROVADO`.

## 9. `PLANO-FINAL.md` e `consolidado/mudancas.md` (tech-lead-magister)

`PLANO-FINAL.md` é o artefato que a tutora segue. `mudancas.md` responde cada achado do parecer: `aceito` (onde mudou) ou `rejeitado` (motivo). Achado sem resposta = versão final não está pronta.
