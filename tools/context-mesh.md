# Context Mesh - Framework de Contexto AI-First

> "Contexto e Primario, Codigo e Manifestacao"

## O que e

O Context Mesh e um **framework de processo para desenvolvimento AI-First** que trata o contexto como a criacao primaria, com codigo como sua manifestacao. Habilita "vibe code" -- descreva o que voce quer, a IA constroi -- mantendo voce no controle.

**Problema que resolve:** Codigo gerado por IA funciona, mas o contexto desaparece. Tres meses depois, seu proprio codigo parece estranho. Desenvolvedores perdem horas re-explicando arquitetura a cada sessao. Nao existe forma padrao de preservar contexto entre sessoes.

**NAO substitui Scrum ou Agil** -- e complementar. Funciona com qualquer assistente de IA (Cursor, Copilot, Claude, ChatGPT, Gemini).

**Criador:** Jeftar Mascarenhas | [GitHub](https://github.com/jeftarmascarenhas/context-mesh)

---

## Fluxo de 3 Passos

### Passo 1: INTENCAO (Intent)
**Defina o que quer construir e por que ANTES de escrever codigo.**

- Cria intents de features/bugs
- Toma decisoes tecnicas (ADRs)
- Cria contexto vivo inicial
- Identifica padroes

**Papel Humano:** Liderar captura de intencao, validar, aprovar contexto inicial.
**Papel IA:** Auxiliar na estruturacao, sugerir organizacao, gerar contexto a partir de prompts.

**Saidas:** Declaracao de Intencao, Decisoes Tecnicas (ADRs), Contexto Vivo Inicial, Padroes.

### Passo 2: CONSTRUIR (Build)
**IA constroi, humano supervisiona.**

A IA carrega automaticamente: Intent da feature, decisoes tecnicas, padroes estabelecidos, project intent geral.

**Definition of Done:**
- Codigo implementado e funcionando
- Testes passando
- Code review completado
- Contexto atualizado
- Documentacao atualizada
- Deploy realizado

### Passo 3: APRENDER (Learn)
**IA analisa, humano valida.**

- Atualizar decisions com outcomes
- Documentar padroes aprendidos
- Registrar o que funcionou e o que nao funcionou
- Loop de feedback: aprendizados refinam a intencao para o proximo ciclo

### Padrao Plan-Approve-Execute

Aplicado em todo trabalho assistido por IA:
1. **Planejar:** IA explica o que fara
2. **Aprovar:** Voce revisa e aprova
3. **Executar:** IA executa com aprovacao

---

## Estrutura de Diretorios

### Completa:
```
context/
  intent/
    project-intent.md
    feature-*.md
    bug-*.md
  decisions/
    001-*.md
    002-*.md
  knowledge/
    patterns/
    anti-patterns/
  agents/
  evolution/
    changelog.md
```

### Minima (para comecar):
```
context/
  intent/
    feature-user-auth.md
  decisions/
    002-auth.md
```

---

## Como Aplicar na Pratica

### Cenarios de Inicio

- **Novo Projeto:** Comece do zero. Use o prompt "new-project" da Biblioteca de Prompts.
- **Projeto Existente:** IA analisa codigo, extrai funcionalidades, cria base. Use o prompt "existing-project".
- **Projeto Freelance:** Estrutura intencao a partir de specs do cliente. Use o prompt "freelance-project".

### Workflow

1. Referencie o arquivo da feature: `@context/intent/feature-homepage.md`
2. A IA carrega o contexto completo automaticamente
3. A IA gera codigo consistente com contexto completo

### Regra de Ouro para Arquivos

> "Isso e um novo escopo ou evolucao do escopo existente?"

- **Novo escopo** -> Criar novo arquivo
- **Mesmo escopo** -> Atualizar arquivo existente (Git preserva historico)
- **Nunca delete** arquivos -- marque como deprecated/resolved

### Convencoes de Nomenclatura

**Intent:** `project-intent.md`, `feature-*.md`, `bug-*.md`, `refactor-*.md`
**Decisions:** `001-*.md`, `002-*.md`
**Status Intent:** Draft, In Progress, Completed, Deprecated, Resolved
**Status Decisions:** Proposed, Accepted, Superseded, Deprecated
**Status Knowledge:** Active, Deprecated, Superseded

---

## 5 Principios Fundamentais

1. **Contexto como Criacao Primaria** -- Contexto e o artefato principal. Codigo e sua manifestacao.
2. **Arquitetura Orientada por Intencao** -- Comece com o que e por que antes do como.
3. **Conhecimento como Entidade Viva** -- Contexto evolui continuamente.
4. **Arquitetura Contextual de Decisoes** -- Decisoes capturadas com justificativa, alternativas, resultados.
5. **Consciencia Colaborativa Humano-IA** -- Papeis claros em cada passo.

---

## Integracao com Ferramentas de IA

### Cursor
Configurar Rules for AI para carregar contexto com prefixo `@context/`. Usar Composer para agentes multi-arquivo.

### Claude / ChatGPT
Upload ou cole arquivos de contexto. Estruture prompts referenciando os arquivos.

### AGENTS.md
- AGENTS.md = Roteador operacional (comandos, workflow, referencias)
- Context Mesh = Contexto estrategico (intent, decisions, knowledge)
- Juntos: AGENTS.md roteia agentes para os arquivos Context Mesh corretos

---

## Tecnicas Avancadas

### Agentes Compostos
Orquestre sub-agentes para funcionalidades complexas (database, API, UI, testes).

### Heranca de Contexto
`feature-auth-oauth.md` herda de `feature-auth.md`. Decisions com prefixos (`001a-` estende `001-`).

### Execucao Condicional
Agentes que se adaptam baseado na configuracao do projeto.

### Escalamento por Tamanho

| Tamanho | Contexto |
|---------|----------|
| Pequeno (1-2 devs) | Intent + decisoes chave + alguns padroes |
| Medio (3-10 devs) | Intencoes de feature + padroes detalhados + agentes |
| Grande (10+ devs) | Contexto por dominio + bibliotecas compartilhadas |

---

## Casos de Sucesso

### Migracao Front-End
- 2 devs migraram monolito React em 10 micro front-ends em **15 dias**
- O que normalmente leva meses

### Website Context Mesh
- 73 arquivos TypeScript/React, 17 paginas, 31 componentes
- Tempo tradicional com IA: ~53 horas
- Tempo com Context Mesh: **50 minutos** (63,6x mais rapido)

---

## Fontes

- [Context Mesh - Site Oficial](https://www.context-mesh.org/pt)
- [GitHub - context-mesh](https://github.com/jeftarmascarenhas/context-mesh)
