# Guia Completo: Melhores Praticas para Usar o Google Gemini na Programacao

## Indice

1. [Gemini CLI](#1-gemini-cli)
2. [Gemini API](#2-gemini-api)
3. [Gemini em IDEs](#3-gemini-em-ides)
4. [Workflows Praticos](#4-workflows-praticos)

---

## 1. Gemini CLI

### 1.1 Instalacao e Configuracao

O Gemini CLI e um agente de IA open-source (Apache 2.0) para o terminal.

```bash
# Instalar globalmente
npm install -g @google/gemini-cli

# Ou executar sem instalar
npx https://github.com/google-gemini/gemini-cli
```

**Autenticacao:**
- **Google Account (gratuito):** Gemini 2.5 Pro, ~60 req/min, 1.000 req/dia
- **API Key:** Para uso programatico ou limites maiores (Google AI Studio)

### 1.2 GEMINI.md - Contexto Persistente

**Hierarquia de carregamento:**
1. **Global**: `~/.gemini/GEMINI.md`
2. **Projeto/Ancestrais**: Do diretorio atual ate a raiz
3. **Subdiretorios**: Instrucoes especificas por componente

```markdown
<!-- .gemini/GEMINI.md -->
# Contexto do Projeto

Projeto Next.js 15 com TypeScript e Tailwind CSS.

## Regras de Codigo
- TypeScript strict mode
- Componentes funcionais com hooks
- Arquivos em kebab-case
- Testes com Vitest
- Early returns para reduzir aninhamento

## Arquitetura
- /src/app - Rotas do App Router
- /src/components - Componentes reutilizaveis
- /src/lib - Funcoes utilitarias
- /src/services - Chamadas a APIs externas
```

**Modularizacao:**
```markdown
<!-- GEMINI.md principal -->
@conventions.md
@architecture.md
@testing-guidelines.md
```

### 1.3 Comandos de Memoria

```bash
/memory show      # Ver memoria hierarquica
/memory reload    # Recarregar GEMINI.md
/memory add "..."  # Adicionar persistente
```

### 1.4 Extensoes e MCP

```json
{
  "mcpServers": {
    "github": {
      "command": "docker",
      "args": ["run", "-i", "--rm", "-e", "GITHUB_PERSONAL_ACCESS_TOKEN", "ghcr.io/github/github-mcp-server"],
      "env": {
        "GITHUB_PERSONAL_ACCESS_TOKEN": "ghp_seutoken"
      }
    }
  }
}
```

### 1.5 Melhores Praticas para o Gemini CLI

1. Execute dentro do diretorio do projeto
2. Seja especifico nos prompts
3. Comece com planejamento (PRD), divida em etapas pequenas
4. Mantenha GEMINI.md atualizado mas conciso
5. Use extensoes para integrar servicos externos
6. Commits frequentes entre modificacoes do agente
7. Revise sempre o output (especialmente seguranca)
8. Use `/memory add` para persistir aprendizados
9. Prefira tarefas incrementais

---

## 2. Gemini API

### 2.1 Configuracao

```python
# pip install google-genai (o antigo google-generativeai esta DEPRECADO)
import os
from google import genai

client = genai.Client(api_key=os.getenv("GEMINI_API_KEY"))
```

```typescript
// npm install @google/genai (o antigo @google/generative-ai esta DEPRECADO)
import { GoogleGenAI } from "@google/genai";
const ai = new GoogleGenAI({ apiKey: process.env.GEMINI_API_KEY });
```

### 2.2 Framework PTCF (Persona, Task, Context, Format)

```python
prompt = """
Persona: Engenheiro de software senior em Python e microsservicos.

Tarefa: Revise o codigo e identifique problemas de performance,
seguranca e manutenibilidade.

Contexto: Endpoint Flask que processa pagamentos, alto trafego (~10k req/min).

Formato: Para cada problema:
1. Localizacao no codigo
2. Descricao
3. Severidade (alta/media/baixa)
4. Codigo corrigido
"""
```

### 2.3 Gemini 3.x - Prompts Diretos

O Gemini 3 segue instrucoes curtas melhor que versoes anteriores:

```python
# Gemini 3.x (direto ao ponto)
prompt = """
Crie funcao TypeScript que ordena lista de objetos por
multiplas chaves. Suporte ascendente e descendente.
Retorne tipagem generica.
"""
```

### 2.4 Saida Estruturada

```python
from pydantic import BaseModel

class CodeReview(BaseModel):
    arquivo: str
    problemas: list[str]
    severidade: str
    sugestao_correcao: str

response = client.models.generate_content(
    model="gemini-2.0-flash",
    contents="Analise este codigo para problemas de seguranca: ...",
    config={
        "response_mime_type": "application/json",
        "response_schema": list[CodeReview],
    },
)
```

### 2.5 Capacidades Multimodais

```python
from google.genai import types

# Analisar screenshot de UI para gerar codigo
response = client.models.generate_content(
    model="gemini-2.0-flash",
    contents=[
        types.Part.from_bytes(data=image_data, mime_type="image/png"),
        "Analise este screenshot e gere o codigo React com Tailwind CSS.",
    ],
)
```

### 2.6 Temperatura no Gemini 3.x

**Regra importante:** Mantenha temperatura no padrao **1.0**. Reduzir pode causar loops ou degradacao.

---

## 3. Gemini em IDEs

### 3.1 VS Code - Gemini Code Assist

- Completions em tempo real
- `/fix` - Corrigir erros
- `/generate` - Gerar codigo
- `/doc` - Documentacao
- `/simplify` - Simplificar codigo
- **Agent Mode** para tarefas multi-etapas complexas

### 3.2 Android Studio

1. **Project Assistant:** Gera apps inteiras a partir de prompts e mockups
2. **Force busca na documentacao:** Adicione "busque na documentacao oficial"
3. **Agent Mode:** Plano de execucao multi-arquivo
4. **Custom Coding Rules:** "Sempre adicionar testes unitarios"
5. **`.aiexclude`:** Controle de privacidade
6. **Melhor modelo:** Use API Key do AI Studio para modelos mais recentes

### 3.3 Outros IDEs

- JetBrains (IntelliJ, PyCharm, WebStorm)
- Cloud Shell Editor / Cloud Workstations
- Qualquer editor com suporte MCP via Gemini CLI

---

## 4. Workflows Praticos

### 4.1 Workflow de Desenvolvimento Completo

```
1. PLANEJAMENTO   -> PRD no GEMINI.md, dividir em tarefas
2. SCAFFOLD       -> gemini "Crie estrutura conforme PRD"
3. IMPLEMENTACAO  -> Uma feature por vez, commit apos cada etapa
4. TESTES         -> gemini "Gere testes para src/services/payment.ts"
5. CODE REVIEW    -> gemini "Revise ultimos commits para seguranca"
```

### 4.2 Comandos de Terminal

```bash
gemini "Encontre todos os .ts modificados nos ultimos 7 dias com 'TODO'"
```

### 4.3 Debugging Multimodal

```python
response = client.models.generate_content(
    model="gemini-2.0-flash",
    contents=[
        types.Part.from_bytes(data=screenshot_erro, mime_type="image/png"),
        "Screenshot do erro. Stack trace: TypeError: Cannot read 'map'..."
    ],
)
```

### 4.4 Desempenho dos Modelos (Referencia 2025)

| Modelo | SWE-bench | Melhor para |
|---|---|---|
| Gemini 3 Flash | 78% | Tarefas frequentes, alta velocidade |
| Gemini 3 Pro | Top-tier | Tarefas complexas, raciocinio profundo |
| Gemini 2.5 Pro | Muito bom | Alternativa estavel |

**Dica:** Gemini 3 Flash supera o Pro no SWE-bench para coding agentico.

---

## Fontes

- [Gemini CLI - GitHub](https://github.com/google-gemini/gemini-cli)
- [Gemini CLI Best Practices - DEV Community](https://dev.to/proflead/gemini-cli-best-practices-10-pro-tips-272b)
- [Gemini CLI Tips & Tricks - Addy Osmani](https://addyo.substack.com/p/gemini-cli-tips-and-tricks)
- [Mastering Gemini CLI - Google Cloud Blog](https://cloud.google.com/blog/topics/developers-practitioners/mastering-gemini-cli)
- [Prompt Design Strategies - Google AI](https://ai.google.dev/gemini-api/docs/prompting-strategies)
- [Gemini in Android Studio - Google Developers](https://developers.google.com/gemini-code-assist/docs/android-studio-overview)
- [GEMINI.md Files Documentation](https://geminicli.com/docs/cli/gemini-md/)
- [Gemini Code Assist Overview](https://developers.google.com/gemini-code-assist/docs/overview)
