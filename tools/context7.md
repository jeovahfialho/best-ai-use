# Context7 - Documentacao Atualizada para LLMs via MCP

## O que e

O Context7 e um **servidor MCP que fornece documentacao atualizada e especifica por versao diretamente para LLMs**. Desenvolvido pela Upstash.

**Problemas que resolve:**
- Exemplos de codigo baseados em dados de treinamento desatualizados
- APIs alucinadas que nem existem
- Respostas genericas para versoes antigas de pacotes

**Como funciona:** Busca documentacao em tempo real dos repositorios originais das bibliotecas e insere no contexto do prompt.

---

## Como Usar

### Processo em 3 Etapas

1. Escreva seu prompt normalmente
2. Adicione `use context7` ao final
3. Receba codigo com APIs atuais

### Exemplo de Prompt

```
Crie um middleware Next.js que verifique JWT nos cookies e redirecione
usuarios nao autenticados para /login. use context7
```

Com biblioteca especifica:
```
use context7 with /supabase/supabase for authentication docs
```

Com versao:
```
Como configuro middleware no Next.js 14? use context7
```

### Regra Automatica (Sem Digitar "use context7")

Adicione nas configuracoes do editor:
```
Always use Context7 MCP when I need library/API documentation, code generation,
setup or configuration steps without me having to explicitly ask.
```

---

## Instalacao por Ferramenta

### Instalacao Automatica
```bash
npx ctx7 setup
# Com flags: --cursor, --claude, --opencode
```

### Claude Code

**Servidor Remoto (Recomendado):**
```bash
# Com API key
claude mcp add --scope user --header "CONTEXT7_API_KEY: YOUR_KEY" --transport http context7 https://mcp.context7.com/mcp

# Com OAuth
claude mcp add --scope user --transport http context7 https://mcp.context7.com/mcp/oauth

# Sem chave
claude mcp add --scope user --transport http context7 https://mcp.context7.com/mcp
```

**Servidor Local:**
```bash
claude mcp add --scope user context7 -- npx -y @upstash/context7-mcp --api-key YOUR_KEY
```

### Cursor

```json
// ~/.cursor/mcp.json
{
  "mcpServers": {
    "context7": {
      "url": "https://mcp.context7.com/mcp",
      "headers": { "CONTEXT7_API_KEY": "YOUR_KEY" }
    }
  }
}
```

### VS Code

```json
{
  "mcp": {
    "servers": {
      "context7": {
        "type": "http",
        "url": "https://mcp.context7.com/mcp",
        "headers": { "CONTEXT7_API_KEY": "YOUR_KEY" }
      }
    }
  }
}
```

### OpenAI Codex

```toml
[mcp_servers.context7]
url = "https://mcp.context7.com/mcp"
http_headers = { "CONTEXT7_API_KEY" = "YOUR_KEY" }
```

### Gemini CLI

```json
// ~/.gemini/settings.json
{
  "mcpServers": {
    "context7": {
      "httpUrl": "https://mcp.context7.com/mcp",
      "headers": {
        "CONTEXT7_API_KEY": "YOUR_KEY",
        "Accept": "application/json, text/event-stream"
      }
    }
  }
}
```

### Claude Desktop

Remoto: Settings > Connectors > Add Custom Connector. URL: `https://mcp.context7.com/mcp`

### ChatGPT

1. Settings > Apps > Advanced settings (Developer Mode)
2. Create App: Nome "Context7", URL `https://mcp.context7.com/mcp/oauth`
3. Complete OAuth

### Windsurf

```json
{
  "mcpServers": {
    "context7": {
      "serverUrl": "https://mcp.context7.com/mcp",
      "headers": { "CONTEXT7_API_KEY": "YOUR_KEY" }
    }
  }
}
```

### Copilot

```json
{
  "mcpServers": {
    "context7": {
      "type": "http",
      "url": "https://mcp.context7.com/mcp",
      "headers": { "CONTEXT7_API_KEY": "YOUR_KEY" },
      "tools": ["query-docs", "resolve-library-id"]
    }
  }
}
```

---

## API REST

### Autenticacao
```
Authorization: Bearer CONTEXT7_API_KEY
```
Chaves em https://context7.com/dashboard (prefixo `ctx7sk`).

### Buscar Biblioteca
```bash
curl "https://context7.com/api/v2/libs/search?libraryName=react&query=hooks" \
  -H "Authorization: Bearer CONTEXT7_API_KEY"
```

### Obter Documentacao
```bash
curl "https://context7.com/api/v2/context?libraryId=/facebook/react&query=useEffect" \
  -H "Authorization: Bearer CONTEXT7_API_KEY"
```

### Fixar Versao
```bash
curl "https://context7.com/api/v2/context?libraryId=/vercel/next.js/v15.1.8&query=app%20router" \
  -H "Authorization: Bearer CONTEXT7_API_KEY"
```

---

## Skills

Templates de prompt reutilizaveis:

```bash
ctx7 skills search [palavra-chave]
ctx7 skills install /anthropics/skills [nome]
ctx7 skills list
ctx7 skills suggest
```

**Skills populares:** `vercel-react-best-practices`, `supabase-postgres-best-practices`, `seo-audit`, `web-design-guidelines`

---

## Planos

| | Free | Pro ($10/user/mes) | Enterprise |
|---|---|---|---|
| Chamadas API/mes | 1.000 | 5.000/user | Personalizavel |
| Repos privados | Nao | Sim | Sim |
| SSO | - | - | SAML/OIDC |
| Self-hosted | - | - | Disponivel |

---

## Fontes

- [Context7 - Site Oficial](https://context7.com)
- [GitHub - context7](https://github.com/upstash/context7)
- [Documentacao](https://context7.com/docs/overview.md)
