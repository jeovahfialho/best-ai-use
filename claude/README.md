# Guia Completo: Melhores Praticas para Usar o Claude na Programacao

## Indice

1. [Claude Code (CLI)](#1-claude-code-cli)
2. [API do Claude](#2-api-do-claude)
3. [Claude para Codificacao](#3-claude-para-codificacao)

---

## 1. Claude Code (CLI)

### 1.1 O que e o Claude Code

O Claude Code e a ferramenta oficial de linha de comando da Anthropic que permite usar o Claude diretamente no terminal. Em fevereiro de 2026, cerca de 4% dos commits publicos no GitHub (~135.000 por dia) sao criados pelo Claude Code.

### 1.2 Arquivo CLAUDE.md - Contexto Persistente do Projeto

O `CLAUDE.md` e um arquivo especial que o Claude le automaticamente no inicio de cada sessao. Ele fornece contexto persistente sobre o projeto.

**Onde colocar:**
- Raiz do repositorio (compartilhado com a equipe)
- Diretorios pai (para monorepos)
- `~/.claude/CLAUDE.md` (aplicacao universal em todos os projetos)

**Estrutura recomendada:**

```markdown
# Visao Geral do Projeto
Aplicacao e-commerce Next.js com integracao Stripe e PostgreSQL.

# Comandos Importantes
- Build: `npm run build`
- Testes: `npm test`
- Testes unitarios: `npm run test:unit`
- Lint: `npm run lint`
- Deploy: `npm run deploy:staging`

# Arquitetura
- `app/api/` - Route handlers da API
- `app/models/` - Modelos do banco de dados (Prisma)
- `app/core/` - Configuracao e utilitarios
- `lib/` - Bibliotecas compartilhadas

# Convencoes de Codigo
- TypeScript strict mode sempre
- Usar named exports (nunca default exports)
- Preferir composicao a heranca
- Nomes de variaveis em ingles, comentarios podem ser em portugues

# Avisos Importantes
- NUNCA alterar arquivos em `app/core/config/` sem aprovacao
- O servico de pagamento tem rate limiting de 100 req/min
- Testes de integracao precisam do Docker rodando
```

**O que NAO incluir:**
- Chaves de API, credenciais ou strings de conexao
- Regras de formatacao que um linter pode resolver (use ESLint/Prettier)
- Informacoes sobre vulnerabilidades de seguranca

**Dica:** Use `/init` para gerar um CLAUDE.md inicial baseado na estrutura do projeto, e refine com o tempo.

### 1.3 Hooks - Automacao Deterministica

Hooks sao scripts que rodam automaticamente em pontos especificos do fluxo do Claude. Diferente das instrucoes no CLAUDE.md (que sao consultivas), hooks sao deterministicos -- sempre executam.

**Eventos disponiveis:**
- `PreToolUse` - Antes de uma ferramenta executar
- `PostToolUse` - Depois de uma ferramenta completar com sucesso

**Configuracao em `.claude/settings.json`:**

```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Bash",
        "command": "python3 .claude/scripts/block-dangerous.py",
        "timeout": 5
      }
    ],
    "PostToolUse": [
      {
        "matcher": "Write|Edit",
        "command": "npx prettier --write $CLAUDE_FILE_PATH",
        "timeout": 30
      }
    ]
  }
}
```

**Casos de uso praticos:**
- **PreToolUse:** Bloquear comandos destrutivos (`rm -rf`, `git reset --hard`)
- **PostToolUse:** Rodar formatador (Prettier, Black) automaticamente apos cada edicao
- **PostToolUse:** Executar testes nos arquivos modificados

### 1.4 MCP Servers - Integracao com Ferramentas Externas

MCP (Model Context Protocol) permite conectar ferramentas externas ao Claude Code.

```bash
# Adicionar um servidor MCP
claude mcp add notion-server -- npx @notionhq/mcp-server
claude mcp add postgres -- npx @modelcontextprotocol/server-postgres $DATABASE_URL
claude mcp add github -- npx @modelcontextprotocol/server-github
```

**Usos comuns:**
- Implementar features a partir de issues do Notion/Linear
- Consultar bancos de dados diretamente
- Buscar dados de monitoramento
- Integrar designs do Figma

### 1.5 Slash Commands - Atalhos Reutilizaveis

Slash commands sao arquivos Markdown que funcionam como atalhos para prompts frequentes.

**Localizacao:**
- Pessoais: `~/.claude/commands/` (disponiveis em qualquer projeto)
- Projeto: `.claude/commands/` (compartilhados via repositorio)
- Estrutura de pastas: `.claude/commands/git/commit.md` vira `/git:commit`

**Exemplo: `.claude/commands/commit.md`**

```markdown
---
disable-model-invocation: true
---

Analise as mudancas staged e crie um commit message seguindo Conventional Commits.

!git diff --staged

Formato: <tipo>(<escopo>): <descricao>

Tipos: feat, fix, docs, style, refactor, test, chore
```

**Exemplo: `.claude/commands/review.md`**

```markdown
---
model: haiku
---

Revise o codigo abaixo e identifique:
1. Bugs potenciais
2. Problemas de performance
3. Violacoes de boas praticas

!git diff
```

**Dicas:**
- Use `!comando` para executar um comando e injetar a saida no prompt
- Use `disable-model-invocation: true` para comandos com efeitos colaterais
- Use `model: haiku` para comandos que precisam ser rapidos e baratos

### 1.6 Gerenciamento de Contexto

A janela de contexto do Claude Code tem limite de 200.000 tokens. Conforme se aproxima do teto, a performance degrada.

**Comandos essenciais:**

| Comando | Funcao |
|---------|--------|
| `/clear` | Limpa todo o historico (inicio fresco) |
| `/compact` | Resume a conversa (70k tokens -> ~4k tokens) |
| `/compact foco nas mudancas da API` | Compacta com foco especifico |
| `/context` | Mostra uso detalhado de tokens |

**Estrategias:**
- Use `/clear` entre tarefas distintas
- Use `/compact` quando precisa manter contexto mas esta ficando sem espaco
- Quando restar ~50.000 tokens livres, e hora de agir
- Coloque regras persistentes no CLAUDE.md (sobrevivem a `/clear` e `/compact`)

### 1.7 Subagentes e Delegacao de Tarefas

Subagentes permitem delegar trabalho para agentes paralelos, preservando o contexto da conversa principal.

**Beneficios:**
- Preservam contexto (exploracao fica fora da conversa principal)
- Ate 7 agentes simultaneos
- Permitem restricao de ferramentas e system prompts focados

**Padroes de uso:**
- **Paralelo:** 3+ tarefas independentes, sem estado compartilhado
- **Sequencial:** Tarefas com dependencias, arquivos compartilhados

**Dica:** Delegue operacoes que produzem muita saida (rodar testes, buscar documentacao, processar logs) para subagentes.

### 1.8 Fluxos de Trabalho Avancados

**Modo Planejamento:** Antes de codificar, peca ao Claude para planejar.

```
Antes de implementar, crie um plano detalhado para:
- Adicionar autenticacao OAuth2 com Google
- Liste arquivos a criar/modificar
- Identifique dependencias
- Proponha a ordem de implementacao
```

**Headless Mode (CI/CD):**

```bash
claude --headless --prompt "Revise o PR e sugira melhorias" --output json
```

---

## 2. API do Claude

### 2.1 Engenharia de Prompts - Fundamentos

Seja claro e explicito. Os modelos Claude 4.x respondem melhor a instrucoes claras e especificas.

```python
import anthropic

client = anthropic.Anthropic()

# Bom - especifico
message = client.messages.create(
    model="claude-sonnet-4-5-20250514",
    max_tokens=1024,
    messages=[
        {"role": "user", "content": """Revise o codigo Python abaixo e:
1. Identifique bugs e corrija-os
2. Melhore a performance (especialmente loops)
3. Adicione type hints
4. Siga PEP 8

<code>
def process(data):
    result = []
    for i in range(len(data)):
        if data[i]['status'] == 'active':
            result.append(data[i]['name'].upper())
    return result
</code>

Retorne o codigo corrigido com comentarios explicando cada mudanca."""}
    ]
)
```

### 2.2 System Prompts e Role Prompting

```python
message = client.messages.create(
    model="claude-sonnet-4-5-20250514",
    max_tokens=2048,
    system="""Voce e um engenheiro de software senior especializado em Python e
arquitetura de microsservicos. Voce:
- Sempre sugere solucoes com tratamento de erros robusto
- Prioriza legibilidade e manutencao sobre otimizacao prematura
- Segue os principios SOLID
- Fornece exemplos praticos com testes
- Responde em portugues brasileiro""",
    messages=[
        {"role": "user", "content": "Como estruturar um servico de notificacoes?"}
    ]
)
```

### 2.3 Tags XML para Estruturar Prompts

O Claude foi fine-tuned para prestar atencao especial a estrutura criada por tags XML.

```python
prompt = """
<instructions>
Analise o log de erros e identifique a causa raiz.
Classifique a severidade como: critica, alta, media, baixa.
</instructions>

<context>
Aplicacao: API REST em FastAPI
Ambiente: Producao (AWS ECS)
Horario: Pico de trafego (18h-22h)
</context>

<error_log>
2026-03-10 19:45:23 ERROR sqlalchemy.pool: Connection pool exhausted
2026-03-10 19:45:24 ERROR app.api: Request timeout after 30s - /api/orders
2026-03-10 19:45:25 CRITICAL app.health: Health check failed
</error_log>

<output_format>
- Causa raiz (1-2 frases)
- Severidade
- Acoes imediatas (lista)
- Prevencao futura (lista)
</output_format>
"""
```

**Tags uteis:** `<instructions>`, `<context>`, `<example>`, `<input>`, `<output_format>`, `<constraints>`, `<documents>`

### 2.4 Exemplos (Few-Shot Prompting)

Exemplos sao a forma mais confiavel de direcionar formato, tom e estrutura. Use 3-5 exemplos.

```python
prompt = """Converta descricoes de bugs em tickets estruturados.

<examples>
<example>
<input>O botao de login nao funciona no Safari</input>
<output>
**Titulo:** [Bug] Botao de login inoperante no Safari
**Severidade:** Alta
**Passos:** 1. Abrir app no Safari 2. Clicar em "Login"
**Esperado:** Modal de login abre
**Atual:** Nenhuma resposta ao clique
**Plataforma:** Safari (todas versoes)
</output>
</example>
</examples>

<input>Os emails de confirmacao chegam com o nome errado do cliente</input>
"""
```

### 2.5 Tool Use (Uso de Ferramentas)

```python
tools = [
    {
        "name": "buscar_usuario",
        "description": "Busca informacoes de um usuario pelo ID ou email",
        "input_schema": {
            "type": "object",
            "properties": {
                "user_id": {"type": "string", "description": "ID unico do usuario"},
                "email": {"type": "string", "description": "Email do usuario"}
            },
            "required": []
        }
    }
]

response = client.messages.create(
    model="claude-sonnet-4-5-20250514",
    max_tokens=1024,
    tools=tools,
    messages=[
        {"role": "user", "content": "Busque o usuario joao@email.com"}
    ]
)
```

**Boas praticas:**
- Nomes descritivos para ferramentas e parametros
- Inclua exemplos junto com o schema
- Use `strict: true` para garantir validacao do schema

### 2.6 Extended Thinking (Pensamento Estendido)

```python
response = client.messages.create(
    model="claude-opus-4-6-20250515",
    max_tokens=4096,
    thinking={"type": "adaptive"},
    messages=[
        {"role": "user", "content": "Projete a arquitetura de um sistema de filas distribuido"}
    ]
)

for block in response.content:
    if block.type == "thinking":
        print("Raciocinio:", block.thinking)
    elif block.type == "text":
        print("Resposta:", block.text)
```

### 2.7 Padrao de Auto-Correcao (Chaining)

Gerar rascunho -> Revisar contra criterios -> Refinar:

```python
# Passo 1: Gerar codigo
draft = client.messages.create(model="claude-sonnet-4-5-20250514", ...)

# Passo 2: Revisar
review = client.messages.create(
    model="claude-sonnet-4-5-20250514",
    messages=[{"role": "user", "content": f"Revise este codigo: {draft.content[0].text}"}]
)

# Passo 3: Refinar
final = client.messages.create(
    model="claude-sonnet-4-5-20250514",
    messages=[{"role": "user", "content": f"Corrija baseado na revisao: ..."}]
)
```

---

## 3. Claude para Codificacao - Praticas Efetivas

### 3.1 Escrevendo Prompts Efetivos

**Ruim:**
> "Crie uma funcao de autenticacao"

**Bom:**
> "Crie uma funcao de autenticacao em `app/auth/service.py` usando JWT com refresh tokens. Siga o padrao existente em `app/users/service.py`. Use PyJWT. Tokens de acesso expiram em 15min, refresh em 7 dias. Inclua testes."

**Checklist para prompts de codigo:**
- [ ] Linguagem e framework especificados
- [ ] Arquivo ou local de destino indicado
- [ ] Padroes existentes referenciados
- [ ] Restricoes e requisitos listados
- [ ] Formato de saida esperado descrito

### 3.2 Desenvolvimento Iterativo

```
1. PLANEJAR  -> "Liste os arquivos que precisam mudar"
2. IMPLEMENTAR -> "Siga com o plano"
3. VALIDAR   -> "Rode os testes"
4. REFINAR   -> "O teste falhou. Corrija..."
5. DOCUMENTAR -> "Adicione docstrings e atualize o README"
```

### 3.3 Gerenciamento de Contexto na Pratica

| Estrategia | Quando Usar |
|-----------|-------------|
| CLAUDE.md | Regras permanentes do projeto |
| `/clear` | Entre tarefas distintas |
| `/compact` | Contexto util mas conversa longa |
| Subagentes | Tarefas que geram muita saida |
| Slash commands | Prompts repetitivos |
| Hooks | Acoes que devem SEMPRE acontecer |

**Anti-padroes:**
- Colar arquivos inteiros no prompt (deixe o Claude ler com ferramentas)
- Manter conversas longas sem compactar
- Repetir instrucoes que deveriam estar no CLAUDE.md

### 3.4 Estrategias por Tipo de Tarefa

**Para bugs:**
```
O endpoint POST /api/orders retorna 500 quando "discount" e null.
Comportamento esperado: tratar null como 0.
Stack trace: [colar]
Corrija e adicione teste.
```

**Para features novas:**
```
Adicionar exportacao CSV na listagem de usuarios.
- Endpoint GET /api/users/export?format=csv
- Colunas: nome, email, data_cadastro
- Limite de 10.000 registros, streaming
- Seguir padrao de app/api/products/export.py
```

**Para refatoracao:**
```
Refatore app/services/payment.py:
- Extraia validacao para classe separada
- Substitua if/elif por Strategy Pattern
- Mantenha interface publica identica
- Garanta que testes existentes passem
```

### 3.5 Cinco Camadas de Efetividade

1. **Configuracao:** CLAUDE.md, settings.json, permissoes
2. **Permissoes:** Controle do que o Claude pode fazer automaticamente
3. **Hooks:** Automacao deterministica (formatacao, validacao, seguranca)
4. **MCP:** Ferramentas externas (bancos, APIs, servicos)
5. **Delegacao:** Subagentes para exploracao e trabalho especializado

### 3.6 Dicas Finais

1. Comece com `/init` para gerar CLAUDE.md inicial
2. Use `/clear` liberalmente -- conversas curtas superam conversas longas
3. Planeje antes de codar
4. Referencie arquivos existentes como exemplos
5. Delegue para subagentes operacoes verbosas
6. Automatize com hooks tudo que deve sempre acontecer
7. Crie slash commands para fluxos repetitivos
8. Monitore tokens com `/context`
9. Itere, nao reinicie
10. Trate o CLAUDE.md como codigo -- faca commits, revise, evolua

---

## Fontes

- [Best Practices for Claude Code - Documentacao Oficial](https://code.claude.com/docs/en/best-practices)
- [Using CLAUDE.MD files - Blog Anthropic](https://claude.com/blog/using-claude-md-files)
- [Claude Code Hooks Reference](https://code.claude.com/docs/en/hooks)
- [Slash Commands - Documentacao Oficial](https://code.claude.com/docs/en/slash-commands)
- [Create Custom Subagents](https://code.claude.com/docs/en/sub-agents)
- [Prompting Best Practices - API Docs](https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/claude-prompting-best-practices)
- [Structured Outputs - API Docs](https://platform.claude.com/docs/en/build-with-claude/structured-outputs)
- [Tool Use Implementation](https://platform.claude.com/docs/en/agents-and-tools/tool-use/implement-tool-use)
- [Extended Thinking Tips](https://platform.claude.com/docs/en/build-with-claude/prompt-engineering/extended-thinking-tips)
- [Effective Context Engineering - Anthropic Engineering](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents)
