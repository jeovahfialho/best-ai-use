# Promptfoo - Avaliacao e Teste de Prompts/Modelos LLM

## O que e

O Promptfoo e uma **plataforma open-source para avaliacao, teste e red teaming de aplicacoes LLM**. Permite abordagem sistematica e orientada a testes, em vez de tentativa e erro.

- 300.000+ usuarios open-source
- 127 empresas Fortune 500
- 70+ provedores de LLM suportados
- 50+ tipos de vulnerabilidades testados

---

## Como Funciona

### Metodologia de 5 Etapas

1. **Definir casos de teste** - cenarios principais e modos de falha
2. **Configurar avaliacao** - prompts, testes e provedores de API
3. **Executar avaliacao** - registrar saidas do modelo
4. **Analisar resultados** - pontuacao automatizada ou revisao na interface web
5. **Iterar** - refinar com base nos resultados

### Privacidade
Opera localmente com comunicacoes diretas ao LLM -- nenhuma transferencia de dados para servidores externos.

---

## Instalacao e Uso

```bash
# Metodo 1: npx
npx promptfoo@latest init --example getting-started

# Metodo 2: npm global
npm install -g promptfoo
promptfoo init --example getting-started

# Metodo 3: Homebrew
brew install promptfoo
```

### Executar e Visualizar

```bash
npx promptfoo@latest eval        # Executar avaliacao
npx promptfoo@latest view        # Ver resultados no navegador
promptfoo eval --watch            # Modo watch (recarregamento ao vivo)
```

---

## Configuracao (promptfooconfig.yaml)

```yaml
# Prompts a testar
prompts:
  - 'Converta o texto em ingles para {{language}}: {{input}}'
  - file://prompt2.txt

# Provedores (modelos)
providers:
  - openai:gpt-5-mini
  - anthropic:messages:claude-opus-4-6
  - google:gemini-3-pro-preview
  - ollama:chat:llama3.3

# Casos de teste
tests:
  - vars:
      language: French
      input: Hello world
    assert:
      - type: contains
        value: 'Bonjour le monde'
  - vars:
      language: Spanish
      input: Where is the library?
    assert:
      - type: icontains
        value: 'Donde esta la biblioteca'

# Configuracao padrao para todos os testes
defaultTest:
  assert:
    - type: llm-rubric
      value: nao descreve a si mesmo como uma IA
```

### Provedores com Configuracao

```yaml
providers:
  - id: openai:gpt-5
    config:
      temperature: 0.7
      max_tokens: 150
```

---

## Sistema de Assertions (Validacoes)

### Deterministicas

| Tipo | Funcao |
|------|--------|
| `equals` | Correspondencia exata |
| `contains` / `icontains` | Presenca de substring |
| `starts-with` | Correspondencia de prefixo |
| `contains-any` / `contains-all` | Logica de multiplas substrings |
| `regex` | Expressao regular |
| `is-json` | Validacao JSON (com schema) |
| `is-html` / `is-sql` / `is-xml` | Validacao de formato |
| `latency` | Tempo de resposta (ms) |
| `cost` | Custo de uso da API |

### Avaliadas por Modelo (Model-Graded)

| Tipo | Funcao |
|------|--------|
| `llm-rubric` | Criterios customizados (mais flexivel) |
| `factuality` | Aderencia a fatos fornecidos |
| `context-faithfulness` | Saida usa o contexto fornecido |
| `context-recall` | Contexto contem informacao necessaria |
| `context-relevance` | Contexto relevante para a consulta |
| `answer-relevance` | Resposta aborda a pergunta |
| `similar` | Similaridade por cosseno |
| `select-best` | Ranquear multiplas saidas |

### Negacao
Todo tipo pode ser negado com `not-` (ex: `not-equals`, `not-regex`).

---

## Exemplo Completo

```yaml
prompts:
  - 'Assistente util. Responda: {{question}}'
  - 'Especialista. Responda com precisao: {{question}}'

providers:
  - openai:gpt-5-mini
  - anthropic:messages:claude-sonnet-4-6

defaultTest:
  assert:
    - type: llm-rubric
      value: resposta educada e informativa
      weight: 2
    - type: cost
      threshold: 0.01
    - type: latency
      threshold: 5000

tests:
  - vars:
      question: Qual e a capital do Brasil?
    assert:
      - type: contains
        value: Brasilia
      - type: factuality
        value: A capital do Brasil e Brasilia
  - vars:
      question: Explique fotossintese
    assert:
      - type: llm-rubric
        value: explica de forma clara e acessivel
        threshold: 0.8
```

---

## Avaliacao de RAG

**Dimensoes:**
- **Factualidade:** Saidas alinhadas com a verdade?
- **Relevancia da resposta:** Aborda a pergunta?
- **Aderencia ao contexto:** Se baseia no contexto fornecido?
- **Recall do contexto:** Contexto contem info necessaria?
- **Relevancia do contexto:** Quanto do contexto e necessario?

```yaml
tests:
  - vars:
      query: Qual e o limite maximo de compra?
      context: file://docs/reembolso.md
    assert:
      - type: contains
        value: 'R$500'
      - type: factuality
        value: aprovacao do gerente necessaria
      - type: answer-relevance
        threshold: 0.9
      - type: context-faithfulness
        threshold: 0.8
```

---

## Red Teaming (Testes de Seguranca)

### Vulnerabilidades Testadas

**Camada do modelo:** Injecoes de prompt, jailbreaks, discurso de odio, alucinacoes, violacoes de copyright, conselhos prejudiciais, vazamento de PII.

**Camada da aplicacao:** Injecoes indiretas, vazamento de PII do contexto (RAG), vulnerabilidades de ferramentas, sequestro de conteudo, exfiltracao de dados.

### Uso

```bash
npx promptfoo@latest redteam setup    # Configuracao
npx promptfoo@latest redteam run      # Executar
npx promptfoo@latest redteam report   # Dashboard
```

---

## Provedores Suportados (70+)

### Comerciais
OpenAI, Anthropic, Google, AWS Bedrock, Mistral, Groq, DeepSeek, Perplexity, Cohere, Alibaba

### Cloud/Enterprise
Azure OpenAI, Google Vertex AI, AWS SageMaker, Databricks, Snowflake Cortex

### Gateways
OpenRouter, LiteLLM (400+ LLMs), Together AI, Vercel AI Gateway

### Locais
Ollama, llama.cpp, LocalAI, vLLM, Docker Model Runner

### Custom
JavaScript, Python, Go, Ruby, HTTP API, WebSocket, Shell Scripts, Browser

---

## Integracao com MCP

```yaml
providers:
  - id: openai:gpt-5
    config:
      mcp:
        enabled: true
        servers:
          - command: npx
            args: ['-y', '@modelcontextprotocol/server-memory']
            name: memory
```

---

## Melhores Praticas

1. **Combine assertions:** Deterministicas para eficiencia + model-graded para qualidade
2. **Pese estrategicamente:** Pesos maiores para criterios criticos
3. **Use templates:** Reduza duplicacao com `$ref`
4. **Nomeie metricas:** Propriedade `metric` para clareza na interface
5. **Defina thresholds:** Pontuacoes minimas aceitaveis
6. **Use `defaultTest`:** Assertions que se aplicam a todos os testes
7. **Separe em arquivos:** `file://` para prompts, testes e provedores
8. **Variaveis de ambiente:** Nunca chaves de API no YAML versionado
9. **Avalie RAG separadamente:** Recuperacao e geracao isoladas
10. **Integre no CI/CD:** Avaliacoes automaticas para cada mudanca de prompt

---

## Comandos Principais

| Comando | Funcao |
|---------|--------|
| `init` | Inicializar projeto |
| `eval` | Executar avaliacao |
| `view` | Interface web |
| `share` | URL compartilhavel |
| `validate` | Verificar config |
| `cache clear` | Limpar cache |
| `generate dataset` | Gerar testes sinteticos |
| `redteam setup/run/report` | Pipeline de seguranca |

---

## Fontes

- [Promptfoo - Site Oficial](https://www.promptfoo.dev/)
- [Documentacao](https://www.promptfoo.dev/docs/intro/)
- [GitHub](https://github.com/promptfoo/promptfoo)
- [Guia de Inicio](https://www.promptfoo.dev/docs/getting-started/)
- [Red Teaming](https://www.promptfoo.dev/docs/red-team/)
- [Avaliacao de RAG](https://www.promptfoo.dev/docs/guides/evaluate-rag/)
