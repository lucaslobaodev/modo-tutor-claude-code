# modo-tutor-claude-code

Duas peças para estudar a fundo com o Claude Code:

1. **Modo tutor** ([MODO-TUTOR.md](.claude/skills/super-planejadora-de-estudos/template/MODO-TUTOR.md)): o agente ensina, o aluno escreve o código, o agente revisa, o aluno roda. **Fonte única:** esse arquivo dentro do template da skill; é ele que vai para o repo de acompanhamento na criação. Não há cópia na raiz, para não divergir.
2. **Super planejadora de estudos** (skill + agentes em [.claude/](.claude/)): monta, para qualquer tema técnico, um plano de estudos **verificado** e um **super projeto** prático, que depois o modo tutor segue passo a passo.

## Por que a planejadora existe

Pedir "me faça um plano de estudos de X" a um modelo dá um plano escrito de memória: links que não abrem, fatos de versão errados, capítulos "lidos" pelo resumo. A planejadora troca isso por um processo em que cada afirmação tem dono e prova:

- a bibliografia diz o que foi aberto e o que só foi citado;
- cada fonte é lida **inteira**, em fatias que cabem com folga no contexto de quem lê, e a leitura é provada (faixa lida + trechos literais conferíveis);
- links e fatos de versão são abertos e marcados `CONFIRMADO`, `CORRIGIDO` ou `NAO_VERIFICADO`;
- o plano e o projeto passam por revisão por partes antes da versão final.

## O que tem aqui

```
.claude/skills/super-planejadora-de-estudos/
  SKILL.md                     a orquestração (etapas, pausas, retomada)
  formatos.md                  o contrato de saída de cada agente
  fatiamento.md                teto de 50%, cobertura, redespacho, transcrição
  template/                    modelo do repo privado de estado
    MODO-TUTOR.md              método do tutor (fonte única)
    CLAUDE.md  INDICE.md  README.md  .gitignore  temas/_modelo/
.claude/agents/
  estudos-leitora-bibliografa.md   estudos-mini-classificador.md   estudos-verificador.md
  estudos-pedagoga.md              estudos-projetista.md           estudos-tech-lead-magister.md
  estudos-mini-revisor.md          estudos-revisora.md
```

## Instalação

Para usar em qualquer pasta (recomendado, porque as sessões rodam dentro do repo de acompanhamento):

```bash
git clone https://github.com/lucaslobaodev/modo-tutor-claude-code.git
cd modo-tutor-claude-code
mkdir -p ~/.claude/agents ~/.claude/skills
cp -n .claude/agents/estudos-*.md ~/.claude/agents/
cp -rn .claude/skills/super-planejadora-de-estudos ~/.claude/skills/
```

Os agentes têm o prefixo `estudos-` para não colidir com agentes que você já tenha (um `revisora` ou `verificador` global, por exemplo). O `-n` não sobrescreve nada: se um arquivo já existia, ele **não** é atualizado.

Para **atualizar** depois de um `git pull` deste repo:

```bash
# skill: substitua inteira (ela não guarda estado do usuário; o estado mora no repo de acompanhamento)
rm -r ~/.claude/skills/super-planejadora-de-estudos
cp -r .claude/skills/super-planejadora-de-estudos ~/.claude/skills/
# agentes: veja o que muda e copie conscientemente
for f in .claude/agents/estudos-*.md; do diff -u ~/.claude/agents/$(basename $f) $f; done
cp .claude/agents/estudos-*.md ~/.claude/agents/   # depois de conferir o diff (sobrescreve ajustes locais, como model:)
```

A skill instalada carrega o próprio template (`template/`), então não precisa deste clone depois da instalação.

Só para um projeto: copie as mesmas pastas para o `.claude/` do projeto. Abrir o Claude Code dentro deste repo também já carrega a skill e os agentes.

O `model:` de cada agente (`sonnet` para leitura em volume, `opus` para julgamento) é uma escolha inicial; ajuste ao seu plano de uso. A `estudos-projetista` é o agente de propósito geral (lê, escreve, roda comandos e pesquisa na web). Nenhum agente da skill tem a ferramenta Agent: por desenho, só a sessão principal (gerente-estudadora) despacha.

### Pré-requisitos

- `gh` autenticado (para criar e usar o repo privado de acompanhamento).
- `pdftotext` (pacote poppler-utils) para medir e ler PDFs.
- Opcional, para vídeos (**não testado por este repo**): `youtube-transcript-api` (legenda do YouTube); se não houver legenda, `yt-dlp` + `faster-whisper` local. A ferramenta `ytranscript` foi citada como alternativa que faz as duas camadas; confira antes de usar. Detalhes em [fatiamento.md](.claude/skills/super-planejadora-de-estudos/fatiamento.md) §3.

## Como usar, de ponta a ponta

1. Abra o Claude Code e diga: *"você é a gerente-estudadora; quero estudar X"*. A sessão carrega a skill.
2. **Repo de acompanhamento.** Se você ainda não tem o `acompanhamento-de-estudos`, a sessão pede confirmação e o cria **privado** na sua conta a partir do `template/` da skill (que inclui o `MODO-TUTOR.md`). Daí em diante, abra as sessões dentro do clone dele.
3. **Escopo.** A gerente pergunta objetivo, nível, idiomas e recorte de versão.
4. **Bibliografia** (`estudos-leitora-bibliografa`): livros em qualquer língua, docs oficiais, artigos, fóruns, cursos, vídeos, repositórios.
5. **Pausa humana 1:** você obtém os PDFs da lista de download, salva em `temas/<tema>/material/` e diz o que fica e o que sai.
6. **Fatiamento** (a própria gerente): cada fonte vira fatias lógicas (capítulo, seção, vídeo) de no máximo 50% da janela de contexto do modelo que vai ler.
7. **Pausa humana 2:** você aprova a estimativa de despachos do pipeline inteiro (o custo).
8. **Inventário** (`estudos-mini-classificador`, em paralelo): cada fatia lida inteira, com prova de cobertura; o que faltou é redespachado.
9. **Verificação** (`estudos-verificador`): links abertos e fatos de versão conferidos na fonte primária.
10. **Pedagogia** (`estudos-pedagoga`): deduplica, mede o que é central e o que é nicho, monta o grafo de pré-requisitos e o plano em fases, módulos e passos atômicos.
11. **Super projeto** (`estudos-projetista`). **Pausa humana 3:** você aprova ou pede outra ideia.
12. **Consolidação** (`estudos-tech-lead-magister`): cada fase do projeto amarrada aos módulos do plano.
13. **Revisão** (**pausa 2b**, curta: ok para o nº de mini-revisores; `estudos-mini-revisor` por partes, consolidados pela `estudos-revisora`). A `estudos-tech-lead-magister` responde cada achado e gera `plano-v2`; com parecer aprovado, promove a `PLANO-FINAL.md`. Reprovado: segunda rodada; reprovado de novo, decide você.
14. **Pausa humana 4:** você aceita a entrega.
15. **Estudo:** nova sessão, *"você é a tutora do tema X"*. Ela segue o `PLANO-FINAL.md` no modo tutor e registra seu progresso.

## Retomar depois de `/clear`

Todo o estado vive em arquivos versionados no repo de acompanhamento (`INDICE.md`, `temas/<tema>/ESTADO.md`, `temas/<tema>/PROGRESSO.md`). Antes de parar, a sessão reescreve o estado e faz push. Para continuar, abra uma sessão no clone e diga só o papel e o tema; o `CLAUDE.md` de lá ensina a sessão a achar onde parou e o próximo passo.

## Limites conhecidos

- O pipeline gasta muitos despachos num tema grande; a pausa 2 existe para você decidir com o número na mão.
- Fonte que não abre (anti-bot, paywall, PDF escaneado sem texto) vira lacuna declarada, não resumo inventado.
- Os comandos de transcrição de vídeo são indicações, não receitas testadas aqui.
- O pipeline ainda não foi executado de ponta a ponta nesta forma; ele formaliza um processo feito à mão.
