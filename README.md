# modo-tutor-claude-code

Uma skill para estudar a fundo com o Claude Code: **`tutora-de-estudos`**. Uma sessão só faz tudo: lê os sumários de **todos** os livros da sua lista, monta um roadmap consolidado (tópico → módulo → unidade, cada unidade com os capítulos de vários livros sobre o mesmo assunto) e roda o ciclo **ler → planejar → ensinar → testar** em nível de quem quer dominar o assunto, pelo **modo tutor**: o agente ensina, o aluno escreve o código, o agente revisa, o aluno roda.

## Por que uma sessão só

A versão anterior (arquivada em [arquivo/](arquivo/)) era um pipeline de 9 etapas e 8 agentes: lia a bibliografia inteira, inventariava, verificava em lote e só depois ensinava. Medido em 08/11/2026: dos 10 temas, 2 chegaram à fase de ensino; o ensino real aconteceu por um atalho; e quem ensinava recebia o conteúdo de terceira mão, sem poder corrigir o plano. O loop atual segue o que a Anthropic recomenda para agentes (*Building agents: when to fold subagents into your main agent loop*): um loop único, com o conhecimento carregado sob demanda e um segundo agente só quando o contexto de fato estoura.

## O que tem aqui

```
.claude/skills/tutora-de-estudos/
  SKILL.md                  o loop (abrir, roadmap, ciclo da unidade, regras, contexto, parada)
  references/construir-roadmap.md   F0–F4: material, sumários, mapa de assuntos, roadmap, ordem
  template/                 modelo do repo privado de estado
    MODO-TUTOR.md           método do tutor (fonte única)
    CLAUDE.md  INDICE.md  README.md  .gitignore
    roadmap/                PLANO · ASSUNTOS · ORDEM · PROGRESSO · CADERNO · MUDANCAS · mapa/ · unidades/
.claude/agents/
  estudos-leitor-de-capitulo.md   o único subagente: lê um capítulo grande demais para o contexto
arquivo/                    o pipeline antigo (super-planejadora-de-estudos + 8 agentes), só para consulta
```

## Instalação

```bash
git clone https://github.com/lucaslobaodev/modo-tutor-claude-code.git
cd modo-tutor-claude-code
mkdir -p ~/.claude/agents ~/.claude/skills
cp -n .claude/agents/estudos-leitor-de-capitulo.md ~/.claude/agents/
cp -rn .claude/skills/tutora-de-estudos ~/.claude/skills/
```

Para **atualizar** depois de um `git pull`: `rm -r ~/.claude/skills/tutora-de-estudos && cp -r .claude/skills/tutora-de-estudos ~/.claude/skills/`. A skill não guarda estado do usuário; o estado mora no repo de acompanhamento.

Quem usava o pipeline antigo: apague de `~/.claude/` a skill `super-planejadora-de-estudos` e os 8 agentes `estudos-*` dela. Os temas antigos ficam no repo de acompanhamento como insumo (sumários, inventários); o ensino sai do `roadmap/`.

### Pré-requisitos
- `gh` autenticado (para criar e usar o repo privado de acompanhamento).
- `pdftotext` (pacote poppler-utils) para ler PDFs.

## Como usar

1. Abra o Claude Code dentro do clone do seu `acompanhamento-de-estudos` e diga *"monta o roadmap"* ou *"continua o estudo"*. Se o repo não existir, a sessão pede confirmação e o cria **privado** a partir do `template/`.
2. **Material:** você baixa os livros da lista para `temas/<frente>/material/` (fora do git).
3. **Roadmap:** a tutora extrai o sumário de cada livro, atribui cada capítulo a um assunto e junta no mesmo assunto os capítulos de todos os livros que o cobrem, do mais acessível ao mais fundo. Sai `roadmap/<topico>/ROADMAP.md`; você aprova tópico a tópico.
4. **Cada unidade:** a tutora lê todos os capítulos dela, escreve o plano (3–6 blocos com aula, teste e critério de domínio), dá a aula sintetizando os livros e testa com os exercícios deles — prever, implementar, quebrar, diagnosticar, explicar de volta.
5. **Estado:** `roadmap/PROGRESSO.md` e `CADERNO.md` a cada bloco, com commit e push. A compactação automática fica ligada no repo de acompanhamento; o `/clear` é seu.

## Limites conhecidos
- Fonte que não abre (anti-bot, paywall, PDF escaneado sem texto) vira `NAO_VERIFICADO` dito ao aluno, nunca resumo inventado.
- O roadmap consolidado (2026-11-09) ainda não rodou um tópico de ponta a ponta; o primeiro tópico é o teste.
