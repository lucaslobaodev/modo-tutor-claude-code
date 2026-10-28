# Fatiamento, cobertura e redespacho

Referência da gerente-estudadora para as etapas 2-3 (e para dimensionar os lotes das etapas 4 e 8).

## 1. O teto: 50% da janela do subagente

- **O teto vale para o modelo do agente que vai ler**, declarado no frontmatter dele (`model:`). Antes de fatiar, a gerente confere o tamanho da janela desse modelo na documentação do provedor e anota no `ESTADO.md` do tema: `janela do mini-classificador: <N> tokens (fonte: <link>, conferido em <data>)`. Não use número de memória.
- **Teto da fatia = 50% dessa janela**, contando tudo o que o subagente vai carregar: o texto da fatia **mais** o prompt de despacho, o `formatos.md` e a saída que ele vai escrever. Na prática, reserve ~35% para o texto da fatia e o resto para o trabalho.
- **Medir, não estimar de olho.** Extraia o texto e conte caracteres:
  - PDF: `pdftotext -f <ini> -l <fim> -layout arquivo.pdf - | wc -c` (poppler-utils).
  - Página web: salve o texto da seção (ou use o tamanho que o WebFetch devolveu) e conte.
  - Transcrição: `wc -c material/transcricoes/<fonte>.txt`.
- Converter caracteres em tokens é **aproximação**: use uma razão conservadora (por exemplo, 3 caracteres por token para texto técnico misto) e registre a razão usada no `fontes/REGISTRO.md`. Se o provedor oferecer contagem exata de tokens, prefira.

## 2. A unidade lógica

Corte sempre em fronteira de sentido, nunca no meio de uma seção:

| Fonte | Unidade | Se a unidade passa do teto | Se é muito pequena |
|---|---|---|---|
| Livro (PDF) | capítulo | divide por seção de 1º nível do capítulo | junta capítulos vizinhos até perto do teto |
| Documentação | seção do índice | divide por subpágina | junta subpáginas irmãs |
| Artigo / post | o artigo | divide por título H2 | junta vários artigos curtos do mesmo assunto |
| Fórum / Q&A | a thread | divide por blocos de respostas | junta threads do mesmo assunto |
| Vídeo | o vídeo | divide por capítulo do vídeo ou blocos de ~20 min | junta vídeos curtos da mesma série |
| Repositório | README + docs/ + exemplos | divide por pasta | — |

Cada fatia recebe uma **faixa exata** no formato da unidade: `pp. 118-137`, `seções 3.1-3.4 (lista de URLs)`, `00:20:00-00:41:30`. Livro: registre o deslocamento entre página impressa e página do PDF (`PDF = impressa + 26`) e passe as duas ao mini-classificador.

## 3. Transcrição de vídeos (pré-requisito opcional, não testado por este repo)

Feita pela gerente **antes** de fatiar, gravada em `material/transcricoes/<fonte>.txt` (fora do git, ver `.gitignore` do template):

1. **Legenda do próprio YouTube**, pela biblioteca Python `youtube-transcript-api` (consulte o `--help` da versão instalada para os parâmetros de idioma e formato).
2. **Se não houver legenda**: baixar o áudio com `yt-dlp` e transcrever localmente com `faster-whisper` (biblioteca Python; um script curto basta).
3. Alternativa citada pelo autor deste repo: a ferramenta `ytranscript`, que encadeia as duas camadas. Confira se ela existe e o que faz antes de usar.

Registre em `fontes/REGISTRO.md` qual camada gerou cada transcrição: legenda manual, legenda automática ou whisper. Transcrição automática é fonte de **menor confiança**: o mini-classificador marca `duvida: true` em nome próprio, número e comando lidos dela.

Se nada funcionar, o vídeo sai do pipeline e entra nas **Lacunas** com o motivo. Nunca resuma vídeo pelo título ou pela descrição.

## 4. Despacho dos mini-classificadores

- Em paralelo, várias chamadas num único turno. Lote de tamanho que a máquina e a conta aguentem; o resto vai na leva seguinte.
- Cada despacho leva só: a fatia (ID, faixa pedida, caminho ou URLs, deslocamento de página), o tema, o caminho de saída e o ponteiro para `formatos.md` §3. **Nunca** o conteúdo de outras fatias.
- O subagente escreve o YAML no caminho dado e devolve só um resumo de 5 linhas (faixa lida, nº de micro, nº de `duvida`, faixas não lidas). A gerente não lê o inventário inteiro: lê o bloco `cobertura`.

## 5. Conferência de cobertura (obrigatória)

Para cada fonte do `REGISTRO.md`:

1. **Soma das faixas `lida`** de todas as fatias da fonte = **extensão total**. Faixa faltando, sobreposta por engano ou listada em `nao_lida` sem motivo aceitável = buraco.
2. **Âncoras**: em ao menos uma fatia por fonte (e em toda fatia que voltou rápido demais ou com poucos micro para o tamanho), a gerente confere as âncoras contra o texto real (`pdftotext ... | grep -F "<trecho>"`). Âncora que não existe no texto = fatia não lida: refaz inteira.
3. **Densidade**: fatia com micro muito abaixo da média da mesma fonte, por tamanho, é suspeita. Abra 1-2 páginas do meio da faixa e veja se o assunto delas aparece no inventário.
4. Registre o resultado no `ESTADO.md`, tabela de fatias: `ok` · `redespachar: <faixa> (<motivo>)`.

## 6. Redespacho

- Redespache **só a faixa que faltou**, como fatia nova com sufixo (`S03-f07b`), nunca a fonte inteira.
- Fatia que falhou duas vezes não vai para uma terceira tentativa às cegas: a gerente lê o motivo (PDF escaneado sem texto? página dinâmica? vídeo sem legenda?) e decide: OCR, outra fonte ou **lacuna declarada** no `ESTADO.md` e na bibliografia.
- O pipeline só sai da etapa 3 quando toda fonte tem cobertura `ok` ou lacuna declarada.

## 7. Dimensionar os outros lotes

O mesmo teto vale para verificador (lote de URLs e afirmações), pedagoga (se o conjunto de inventários não couber, ver SKILL.md, etapa 5) e mini-revisores (uma parte por revisor). Sempre: medir o tamanho do que o agente vai ler, somar a saída prevista, ficar abaixo de 50%.
