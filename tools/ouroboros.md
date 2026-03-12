# Ouroboros - Sistema de Desenvolvimento AI-First por Especificacao

> "Stop prompting. Start specifying." (Pare de dar prompts. Comece a especificar.)

## O que e

O Ouroboros e um **sistema de desenvolvimento com foco em especificacao** que funciona como **plugin para o Claude Code**. Transforma ideias vagas em especificacoes validadas ANTES que a IA escreva codigo.

**Problema que resolve:** A maioria dos projetos de coding com IA falha na **entrada** (o que voce pede), nao na **saida** (o que a IA gera). O gargalo e a **clareza humana**, nao a capacidade da IA.

**Filosofia:** Inspirada em Socrates -- toda grande pergunta leva a uma pergunta mais profunda e ontologica. Nao "como faco isso?" mas "o que E isso, realmente?".

**GitHub:** https://github.com/Q00/ouroboros | **Licenca:** MIT | **Linguagem:** Python

---

## O Loop Evolutivo

```
Interview --> Seed --> Execute --> Evaluate
    ^                                |
    +------ Loop Evolutivo ----------+
```

Cada ciclo NAO repete -- **evolui**. A saida da avaliacao alimenta a proxima geracao.

### As 5 Fases

| Fase | O que acontece |
|------|----------------|
| **Interview** | Questionamento socratico expoe suposicoes ocultas |
| **Seed** | Respostas cristalizam em especificacao imutavel |
| **Execute** | Double Diamond: Descobrir -> Definir -> Projetar -> Entregar |
| **Evaluate** | Portao de 3 estagios: Mecanico ($0) -> Semantico -> Consenso Multi-Modelo |
| **Evolve** | "O que ainda nao sabemos?" -> Reflexao -> proxima geracao |

### Pipeline Interno (6 Fases)

- **Fase 0 - Big Bang:** Transforma ideias em Seed (especificacao)
- **Fase 1 - PAL Router:** Seleciona modelo mais custo-efetivo
- **Fase 2 - Double Diamond:** Decompoe e executa tarefas
- **Fase 3 - Resiliencia:** Detecta estagnacao, aplica pensamento lateral
- **Fase 4 - Avaliacao:** Verificacao em 3 estagios
- **Fase 5 - Loop Secundario:** Processa TODOs adiados

---

## Instalacao e Uso

### Modo Plugin (recomendado, sem Python)

```bash
# No terminal:
claude plugin marketplace add Q00/ouroboros
claude plugin install ouroboros@ouroboros
```

```bash
# Dentro de uma sessao Claude Code:
ooo setup        # Registra servidor MCP (uma vez)
ooo interview "Quero construir uma CLI de gerenciamento de tarefas"
ooo seed         # Gera a especificacao
ooo run          # Executa via Double Diamond
ooo evaluate     # Verificacao em 3 estagios
```

### Modo Completo (Python 3.12+)

```bash
git clone https://github.com/Q00/ouroboros
cd ouroboros
uv sync
export ANTHROPIC_API_KEY="sua-chave"
ouroboros config init
ouroboros init "Construir uma API REST para gerenciamento de tarefas"
ouroboros run seed.yaml
```

### Modo Claude Code Max Plan (sem API key)

```bash
ouroboros init --orchestrator "Sua ideia aqui"
ouroboros run seed.yaml   # orchestrator mode e o padrao
```

### PyPI

```bash
pip install ouroboros-ai
```

---

## Conceitos Fundamentais

### Score de Ambiguidade -- O Portao entre Ideia e Codigo

A entrevista nao termina quando voce se sente pronto -- termina quando a **matematica** diz:

```
Ambiguidade = 1 - Soma(clarezai x pesoi)
```

**Dimensoes (projeto novo):**
- Clareza do Objetivo: peso 40%
- Clareza de Restricoes: peso 30%
- Criterios de Sucesso: peso 30%

**Limiar: Ambiguidade <= 0.2** -- so entao um Seed pode ser gerado. 80% de clareza ponderada.

### O Seed -- A "Constituicao" Imutavel

Especificacao YAML imutavel (Pydantic frozen):

```yaml
goal: "Construir um rastreador de financas pessoais com SQLite"
task_type: code
constraints:
  - "Aplicacao desktop apenas"
  - "Orcamento baseado em categorias"
acceptance_criteria:
  - "Rastrear receitas e despesas"
  - "Categorizar transacoes automaticamente"
  - "Gerar relatorios mensais"
ontology_schema:
  name: "FinanceTracker"
  fields:
    - name: "transactions"
      field_type: "array"
      description: "Todas as transacoes financeiras"
metadata:
  ambiguity_score: 0.15
  seed_id: "finance_001"
```

### PAL Router -- Otimizacao de Custos

| Nivel | Custo | Complexidade |
|-------|-------|-------------|
| **Frugal** | 1x | < 0.4 |
| **Standard** | 10x | < 0.7 |
| **Frontier** | 30x | >= 0.7 |

Comeca barato, escala so em caso de falha (2 falhas -> sobe). Apos 5 sucessos -> desce.

### Double Diamond -- Execucao em 4 Fases

Cada criterio de aceitacao passa por:
1. **Discover** (divergir) -- explorar o espaco do problema
2. **Define** (convergir) -- convergir no problema central
3. **Design** (divergir) -- explorar abordagens de solucao
4. **Deliver** (convergir) -- convergir na implementacao

Se um AC nao e atomico, e dividido em 2-5 sub-ACs. Profundidade maxima: 5 niveis.

### Avaliacao em 3 Estagios

1. **Mecanico ($0):** Lint, build, testes, analise estatica, cobertura (limiar: 70%)
2. **Semantico ($$):** Conformidade com ACs, alinhamento, drift, incerteza
3. **Consenso ($$$):** Votacao multi-modelo (GPT-4o, Claude Sonnet, Gemini 2.5 Pro) -- maioria 2/3

**6 condicoes que disparam consenso:**
1. Modificacao do Seed
2. Evolucao da ontologia
3. Reinterpretacao do objetivo
4. Drift do Seed > 0.3
5. Incerteza do Estagio 2 > 0.3
6. Adocao de pensamento lateral

### Convergencia Ontologica

O loop para quando geracoes consecutivas produzem schemas identicos:

```
Similaridade = 0.5 * sobreposicao_nomes + 0.3 * correspondencia_tipos + 0.2 * correspondencia_exata
```

**Limiar: >= 0.95** = convergiu. Limite rigido: 30 geracoes max.

### Controle de Drift (Desvio)

Mede quanto a execucao desviou do Seed:
- Objetivo: peso 50%
- Restricoes: peso 30%
- Ontologia: peso 20%
- Limiar: Drift <= 0.3

---

## Resiliencia -- Quando Esta Travado

### 4 Padroes de Estagnacao

| Padrao | Deteccao |
|--------|----------|
| **SPINNING** | Mesmo hash de saida 3x |
| **OSCILLATION** | Padrao A->B->A->B |
| **NO_DRIFT** | Score de drift nao muda |
| **DIMINISHING_RETURNS** | Taxa de melhoria < 0.01 |

### 5 Personas de Pensamento Lateral

| Persona | Estrategia | Melhor para |
|---------|-----------|-------------|
| **Hacker** | Caminhos nao-convencionais | SPINNING |
| **Researcher** | Buscar mais informacao | NO_DRIFT |
| **Simplifier** | Reduzir complexidade | DIMINISHING_RETURNS |
| **Architect** | Reestruturar fundamentalmente | OSCILLATION |
| **Contrarian** | Desafiar tudo | Todos |

---

## Os Nove Agentes ("The Nine Minds")

| Agente | Papel | Pergunta Central |
|--------|-------|-------------------|
| **Socratic Interviewer** | So pergunta. Nunca constroi. | "O que voce esta assumindo?" |
| **Ontologist** | Encontra essencia, nao sintomas | "O que E isso, realmente?" |
| **Seed Architect** | Cristaliza specs do dialogo | "Completo e sem ambiguidade?" |
| **Evaluator** | Verificacao em 3 estagios | "Construimos a coisa certa?" |
| **QA Judge** | Julgamento de qualidade | Avalia conformidade |
| **Contrarian** | Desafia cada suposicao | "E se o oposto fosse verdade?" |
| **Hacker** | Caminhos nao-convencionais | "Quais restricoes sao reais?" |
| **Simplifier** | Remove complexidade | "O mais simples que funciona?" |
| **Researcher** | Para de codificar, investiga | "Que evidencia temos?" |
| **Architect** | Identifica causas estruturais | "Se recomecassemos?" |

---

## Comandos

### Dentro do Claude Code (ooo)

| Comando | O que faz |
|---------|-----------|
| `ooo setup` | Registra servidor MCP (uma vez) |
| `ooo interview` | Questionamento socratico |
| `ooo seed` | Cristaliza em spec imutavel |
| `ooo run` | Executa via Double Diamond |
| `ooo evaluate` / `ooo eval` | Verificacao em 3 estagios |
| `ooo evolve` | Loop evolutivo ate convergir |
| `ooo unstuck` / `ooo stuck` | Pensamento lateral |
| `ooo status` / `ooo drift` | Drift + rastreamento |
| `ooo ralph` | Loop persistente ate verificado |
| `ooo tutorial` | Aprendizado interativo |
| `ooo qa` | Julgamento de qualidade |
| `ooo cancel` | Cancela operacao |

### CLI (terminal)

| Comando | Descricao |
|---------|-----------|
| `ouroboros init "ideia"` | Inicia entrevista |
| `ouroboros run seed.yaml` | Executa workflow |
| `ouroboros run seed.yaml --dry-run` | Valida sem executar |
| `ouroboros run seed.yaml --debug` | Saida verbosa |
| `ouroboros run seed.yaml --resume orch_abc` | Retoma sessao |
| `ouroboros tui monitor` | Dashboard TUI |
| `ouroboros status health` | Saude do sistema |
| `ouroboros config init` | Inicializa configuracao |
| `ouroboros mcp serve` | Inicia servidor MCP |

---

## Configuracao

```yaml
# ~/.ouroboros/config.yaml
providers:
  default: anthropic/claude-3-5-sonnet
  frugal: anthropic/claude-3-haiku
  standard: anthropic/claude-3-5-sonnet
  frontier: anthropic/claude-3-opus

execution:
  max_parallel_tasks: 5
  default_mode: standard
  auto_save: true
```

**Override de verificacao mecanica** (`.ouroboros/mechanical.toml`):
```toml
lint = "cargo clippy -- -D warnings"
build = "cargo build --release"
test = "cargo nextest run"
timeout = 600
coverage_threshold = 0.5
```

### Linguagens com Verificacao Automatica

| Linguagem | Detectado por | Lint | Build | Test |
|-----------|--------------|------|-------|------|
| Python (uv) | `uv.lock` | ruff | py_compile | pytest |
| Rust | `Cargo.toml` | clippy | cargo build | cargo test |
| Go | `go.mod` | go vet | go build | go test |
| Node | lockfiles | lint | build | test |
| Zig | `build.zig` | -- | zig build | zig build test |

---

## Integracao MCP (Bidirecional)

### Como Servidor (outros clientes usam Ouroboros)

```bash
ouroboros mcp serve
```

Ferramentas expostas: `ouroboros_execute_seed`, `ouroboros_session_status`, `ouroboros_query_events`

### Como Cliente (Ouroboros usa ferramentas externas)

```bash
ouroboros run --mcp-config mcp.yaml seed.yaml
```

---

## Principios de Design

1. **Frugal First** -- Comeca barato, escala so quando necessario
2. **Direcao Imutavel** -- O Seed nao muda; so o caminho se adapta
3. **Verificacao Progressiva** -- Checks baratos primeiro
4. **Lateral sobre Vertical** -- Travado? Mude a perspectiva
5. **Event-Sourced** -- Cada mudanca e um evento; nada se perde

---

## Exemplos Praticos

**Projeto novo:**
```
ooo interview "Construir uma API REST para blog"
ooo seed
ooo run
ooo evaluate
```

**Quando travado:**
```
ooo unstuck
```

**Loop ate convergir:**
```
ooo ralph
```

**Pesquisa (sem codigo):**
```yaml
goal: "Pesquisar tecnologias de fila de mensagens"
task_type: research
```

---

## Fontes

- [GitHub - Q00/ouroboros](https://github.com/Q00/ouroboros)
- [PyPI - ouroboros-ai](https://pypi.org/project/ouroboros-ai/)
