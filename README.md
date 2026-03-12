# Best AI Use - Guia de Boas Praticas para Desenvolvimento com IA

Repositorio central de artigos, boas praticas e guias para desenvolvimento assistido por IA.

> **Proposito:** Servir como base de conhecimento referenciavel via `CLAUDE.md`, `AGENTS.md` ou `GEMINI.md` em outros projetos, garantindo que agentes de IA sigam as melhores praticas documentadas aqui.

## Estrutura

```
best-ai-use/
  claude/          # Boas praticas com Claude (Code, API, prompting, Skills)
  codex/           # Boas praticas com OpenAI Codex (CLI, API)
  gemini/          # Boas praticas com Google Gemini (CLI, API, IDEs)
  tools/           # Ferramentas complementares (Context Mesh, Context7, Promptfoo, Ouroboros)
```

## Ferramentas de IA

| Ferramenta | Guia | Foco |
|-----------|------|------|
| [Claude](claude/README.md) | Claude Code, API, prompting | Anthropic |
| [Claude Skills](claude/building-skills.md) | Construir Skills para o Claude | Anthropic |
| [Codex](codex/README.md) | Codex CLI, API, workflows | OpenAI |
| [Gemini](gemini/README.md) | Gemini CLI, API, IDEs | Google |

## Ferramentas Complementares

| Ferramenta | Guia | Foco |
|-----------|------|------|
| [Context Mesh](tools/context-mesh.md) | Framework de contexto AI-First | Processo |
| [Context7](tools/context7.md) | Documentacao atualizada para LLMs via MCP | Contexto |
| [Promptfoo](tools/promptfoo.md) | Avaliacao e teste de prompts/modelos | Qualidade |
| [Ouroboros](tools/ouroboros.md) | Desenvolvimento por especificacao (plugin Claude Code) | Processo |

## Como Usar em Outros Projetos

Adicione no `CLAUDE.md` do seu projeto:

```markdown
# Referencia de Boas Praticas
Consulte o repositorio best-ai-use para padroes e convencoes:
- Claude: https://github.com/jeovahfialho/best-ai-use/blob/main/claude/README.md
- Context Mesh: https://github.com/jeovahfialho/best-ai-use/blob/main/tools/context-mesh.md
- Context7: https://github.com/jeovahfialho/best-ai-use/blob/main/tools/context7.md
```

## Contribuicao

Este repositorio e continuamente atualizado com novos artigos, frameworks e boas praticas. Cada guia inclui fontes e referencias para aprofundamento.
