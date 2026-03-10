# Guia Completo: OpenAI Codex e Codex CLI para Programacao

## Indice

1. [Codex CLI - Instalacao, Uso e Modos de Sandbox](#1-codex-cli)
2. [API da OpenAI para Codigo](#2-api-da-openai-para-codigo)
3. [Workflows Praticos](#3-workflows-praticos)

---

## 1. Codex CLI

### 1.1 O que e o Codex CLI

O Codex CLI e o agente de codificacao da OpenAI que roda localmente no terminal. Open source, escrito em Rust, pode ler, editar e executar codigo na sua maquina.

### 1.2 Instalacao e Setup

```bash
# Instalar via npm
npm install -g @openai/codex

# Ou via Homebrew (macOS)
brew install openai-codex

# Autenticar
export OPENAI_API_KEY="sk-..."
# Ou interativamente
codex auth login
```

Incluido nos planos ChatGPT Plus, Pro, Business, Edu e Enterprise sem custo adicional.

### 1.3 Modos de Aprovacao

| Modo | Comportamento | Quando Usar |
|------|--------------|-------------|
| `suggest` | Pede aprovacao para **todas** as acoes | Producao, codigo critico |
| `auto-edit` | Aprova edicoes, confirma comandos | Desenvolvimento diario |
| `full-auto` | Executa tudo sem confirmacao | Ambientes isolados, CI/CD |

```bash
codex --approval-mode suggest
codex --approval-mode auto-edit
codex --full-auto
```

**Melhor pratica:** Use `suggest` ou `auto-edit` para producao. Reserve `full-auto` para ambientes isolados.

### 1.4 Modos de Sandbox

- **`workspace-write`** - Ler e editar dentro do workspace (padrao do full-auto)
- **`danger-full-access`** - Remove todas as restricoes
- **`read-only`** - Apenas leitura, ideal para auditorias

### 1.5 Configuracao com config.toml

```toml
# ~/.codex/config.toml
model = "gpt-5.4"
approval_policy = "auto-edit"
sandbox_mode = "workspace-write"

[profiles.rapido]
model = "gpt-5.3-codex-spark"
approval_policy = "on-request"

[profiles.seguro]
model = "gpt-5.4"
approval_policy = "suggest"
sandbox_mode = "read-only"
```

```bash
codex --profile rapido
codex --profile seguro
```

### 1.6 AGENTS.md - Instrucoes Personalizadas

O `AGENTS.md` e um README para agentes de IA. O Codex le antes de comecar qualquer trabalho.

**Hierarquia:**
1. `~/.codex/AGENTS.md` (global)
2. `<raiz-do-repo>/AGENTS.md` (repositorio)
3. `<subdiretorio>/AGENTS.md` (diretorio especifico)
4. `AGENTS.override.md` tem prioridade em cada nivel

**Exemplo:**

```markdown
# AGENTS.md

## Sobre o Projeto
Backend Node.js com Express e PostgreSQL.

## Comandos Importantes
- `pnpm test` - rodar todos os testes
- `pnpm lint` - verificar linting
- `pnpm build` - compilar o projeto

## Padroes do Projeto
- Repository pattern para acesso a dados
- Testes unitarios para novas funcoes
- JSDoc para funcoes publicas
```

---

## 2. API da OpenAI para Codigo

### 2.1 Modelos Recomendados

| Modelo | Uso Ideal |
|--------|----------|
| **GPT-5.4** | Tarefas gerais de codificacao |
| **GPT-5.2-Codex** | Engenharia de software complexa |
| **GPT-5.3-Codex-Spark** | Tarefas rapidas, sessoes interativas |
| **GPT-4.1** | Tarefas com diff de codigo |

### 2.2 Prompt Engineering para Codigo

```python
from openai import OpenAI
client = OpenAI()

response = client.chat.completions.create(
    model="gpt-5.4",
    temperature=0,  # Para codigo, temperatura 0 e recomendado
    messages=[
        {
            "role": "system",
            "content": """Voce e um engenheiro de software senior.
            Stack: Python 3.12, FastAPI, SQLAlchemy 2.0, PostgreSQL.
            Convencoes: type hints obrigatorios, docstrings Google style,
            testes com pytest."""
        },
        {
            "role": "user",
            "content": """Crie um endpoint FastAPI para CRUD de usuarios.

            Modelo existente:
            class User(Base):
                id: int (PK)
                email: str (unique)
                name: str
                created_at: datetime

            Requisitos:
            - Validacao com Pydantic v2
            - Paginacao na listagem
            - Tratamento de erros
            - Retornar 404 quando nao encontrado"""
        }
    ]
)
```

### 2.3 Tecnicas Avancadas

**Diff Generation:**
```
Ao editar arquivos existentes, use o formato de diff unificado:
--- a/arquivo.py
+++ b/arquivo.py
@@ -10,7 +10,7 @@
 def funcao_existente():
-    return valor_antigo
+    return valor_novo
```

**Raciocinio Adaptativo:**
- `reasoning_effort: "medium"` - Sessoes interativas
- `reasoning_effort: "high"` ou `"xhigh"` - Tarefas mais dificeis

---

## 3. Workflows Praticos

### 3.1 Workflow Interativo (Dia a Dia)

```bash
cd meu-projeto
codex
> Implemente autenticacao JWT com refresh tokens no modulo auth/
```

**Dicas:**
- Comece com tarefas bem definidas e escopadas
- Revise cada mudanca antes de aceitar
- Use `/compact` quando a conversa ficar longa

### 3.2 Workflow Nao-Interativo (CI/CD)

```bash
# Tarefa sem interacao
codex exec "Atualize o CHANGELOG.md com as mudancas desde a ultima tag"

# Pipe para stdout
codex exec --format jsonl "Liste todos os TODOs no codigo" > todos.jsonl

# Automacao em CI
codex exec "Rode os testes e corrija qualquer falha" --full-auto
```

### 3.3 Multi-Agent (Tarefas Paralelas)

```bash
# Habilitar multi-agent
/experimental

# Criar agentes em worktrees isolados
> Lance um agente para refatorar autenticacao
> Lance outro para adicionar testes ao modulo de pagamento
```

**Worktrees para isolamento:** Escolha "Worktree" em vez de "Local" para evitar conflitos.

### 3.4 Codex Cloud (Tarefas Assincronas)

```bash
codex cloud exec "Refatore o sistema de cache para Redis Cluster"
codex cloud list
```

Cada tarefa roda em seu proprio sandbox cloud, pre-carregado com seu repositorio.

### 3.5 Slash Commands Uteis

| Comando | Funcao |
|---------|--------|
| `/review` | Revisao de codigo |
| `/fork` | Criar fork de uma thread |
| `/compact` | Compactar contexto |
| `/agent` | Alternar entre agentes |
| `/experimental` | Habilitar features experimentais |

### 3.6 Integracao com MCP

```toml
[mcp_servers.meu-banco]
command = "mcp-server-postgres"
args = ["postgresql://localhost:5432/meubanco"]
enabled = true
```

### 3.7 Dicas de Produtividade

1. **Escreva bons AGENTS.md** - Melhor documentacao = melhores resultados
2. **Tarefas bem escopadas** - "Refatore o modulo de auth" > "refatore todo o backend"
3. **Multiplos agentes em paralelo** para tarefas independentes
4. **Web search integrado** - Util para documentacao, mas trate resultados como nao-confiaveis
5. **Aproveite profiles** para diferentes contextos
6. **Use `codex exec` para scripts** em automacao

---

## Resumo Rapido

| Necessidade | Solucao |
|------------|---------|
| Desenvolvimento diario | `codex` com modo `auto-edit` |
| Revisao de codigo | `codex -s read-only` + `/review` |
| Automacao CI/CD | `codex exec --full-auto` |
| Tarefas pesadas | Codex Cloud |
| Trabalho paralelo | Multi-agent com worktrees |
| Personalizar comportamento | `AGENTS.md` + `config.toml` |
| Ferramentas externas | MCP servers |

---

## Fontes

- [Codex CLI - OpenAI Developers](https://developers.openai.com/codex/cli/)
- [Codex CLI Features](https://developers.openai.com/codex/cli/features/)
- [Introducing Codex - OpenAI](https://openai.com/index/introducing-codex/)
- [Codex Prompting Guide](https://developers.openai.com/cookbook/examples/gpt-5/codex_prompting_guide/)
- [Custom Instructions with AGENTS.md](https://developers.openai.com/codex/guides/agents-md/)
- [Codex Workflows](https://developers.openai.com/codex/workflows/)
- [Multi-agents](https://developers.openai.com/codex/multi-agent/)
- [Codex Cloud](https://developers.openai.com/codex/cloud/)
- [Prompt Engineering - OpenAI API](https://developers.openai.com/api/docs/guides/prompt-engineering/)
