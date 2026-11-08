# modo-tutor-claude-code

Uma skill para estudar a fundo com o Claude Code: **`tutora-de-estudos`**. Uma sessão só faz tudo: combina o objetivo, escolhe até 3 fontes, lê o capítulo na hora de ensinar, mantém um roteiro que ela mesma corrige e ensina pelo **modo tutor**: o agente ensina, o aluno escreve o código, o agente revisa, o aluno roda.

## Por que uma sessão só

A versão anterior (arquivada em [arquivo/](arquivo/)) era um pipeline de 9 etapas e 8 agentes: lia a bibliografia inteira, inventariava, verificava em lote e só depois ensinava. Medido em 08/11/2026: dos 10 temas, 2 chegaram à fase de ensino; o ensino real aconteceu por um atalho; e quem ensinava recebia o conteúdo de terceira mão, sem poder corrigir o plano. O loop atual segue o que a Anthropic recomenda para agentes (*Building agents: when to fold subagents into your main agent loop*): um loop único, com o conhecimento carregado sob demanda e um segundo agente só quando o contexto de fato estoura.

## O que tem aqui

```
.claude/skills/tutora-de-estudos/
  SKILL.md                  o loop (abrir, abertura do tema, loop do módulo, regras, contexto, parada)
  references/migracao.md    como um tema do pipeline antigo entra no loop novo
  template/                 modelo do repo privado de estado
    MODO-TUTOR.md           método do tutor (fonte única)
    CLAUDE.md  INDICE.md  README.md  .gitignore
    temas/_modelo/          ROTEIRO.md · PROGRESSO.md · CADERNO.md
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

Quem usava o pipeline antigo: apague de `~/.claude/` a skill `super-planejadora-de-estudos` e os 8 agentes `estudos-*` dela. Os temas já criados continuam no repo de acompanhamento, e o `references/migracao.md` diz como cada um entra no loop novo.

### Pré-requisitos
- `gh` autenticado (para criar e usar o repo privado de acompanhamento).
- `pdftotext` (pacote poppler-utils) para ler PDFs.

## Como usar

1. Abra o Claude Code dentro do clone do seu `acompanhamento-de-estudos` e diga *"quero estudar X"*. Se o repo ainda não existir, a sessão pede confirmação e o cria **privado** a partir do `template/`.
2. **Abertura:** a tutora pergunta, uma coisa por vez, para quê, o que você já sabe, horas por semana, máquina e idioma. Ela propõe fontes para três papéis (espinha, doc oficial e um complemento opcional) e você escolhe. Os PDFs ficam em `temas/<tema>/material/`, fora do git.
3. **Roteiro de uma página:** de 6 a 15 módulos no nível de capítulo. Você aprova.
4. **Cada módulo:** a tutora lê o capítulo, escreve de 3 a 8 passos, ensina um por vez, confere na doc oficial o que pode ter mudado e ajusta o resto do módulo se você já sabe ou quer pular.
5. **Estado:** `ROTEIRO.md`, `PROGRESSO.md` e `CADERNO.md` por tema, com commit e push a cada módulo. A compactação automática fica ligada no repo de acompanhamento; o `/clear` é seu.

## Limites conhecidos
- Fonte que não abre (anti-bot, paywall, PDF escaneado sem texto) vira `NAO_VERIFICADO` dito ao aluno, nunca resumo inventado.
- O loop novo ainda não rodou um tema de ponta a ponta; o primeiro tema que passar por ele é o teste.
