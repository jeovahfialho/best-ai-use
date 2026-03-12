# Guia Completo para Construir Skills para o Claude

> Fonte: "The Complete Guide to Building Skills for Claude" - Anthropic

## O que e uma Skill

Uma **skill** e um conjunto de instrucoes -- empacotado como uma pasta simples -- que ensina o Claude a lidar com tarefas ou fluxos de trabalho especificos. Em vez de re-explicar preferencias a cada conversa, skills permitem ensinar o Claude uma vez e se beneficiar sempre.

**Funciona em:** Claude.ai, Claude Code e API (mesmo skill, todas as superficies).

---

## Estrutura de Arquivos

```
nome-da-skill/
|-- SKILL.md                  # Obrigatorio
|-- scripts/                  # Opcional - codigo executavel
|   |-- process_data.py
|   |-- validate.sh
|-- references/               # Opcional - documentacao
|   |-- api-guide.md
|   |-- examples/
|-- assets/                   # Opcional - templates
    |-- report-template.md
```

### Regras Criticas

- Arquivo deve ser exatamente `SKILL.md` (sensivel a maiusculas)
- Pasta em kebab-case: `notion-project-setup` (CORRETO)
- Sem espacos, underscores ou maiusculas no nome
- **NAO inclua README.md** dentro da pasta da skill

---

## Divulgacao Progressiva (3 Niveis)

| Nivel | O que | Quando carrega |
|-------|-------|----------------|
| 1o - Frontmatter YAML | Metadados da skill | Sempre (prompt de sistema) |
| 2o - Corpo do SKILL.md | Instrucoes completas | Quando skill e relevante |
| 3o - Arquivos vinculados | Docs detalhados, scripts | Sob demanda |

Isso minimiza uso de tokens mantendo expertise especializada.

---

## Frontmatter YAML

### Formato Minimo Obrigatorio

```yaml
---
name: nome-da-skill
description: O que faz. Use quando o usuario pede [frases especificas].
---
```

### Campos

| Campo | Obrigatorio | Regras |
|-------|-------------|--------|
| `name` | Sim | kebab-case, sem espacos, = nome da pasta |
| `description` | Sim | O que faz + quando usar, < 1024 chars |
| `license` | Nao | MIT, Apache-2.0, etc. |
| `compatibility` | Nao | Requisitos de ambiente (1-500 chars) |
| `metadata` | Nao | author, version, mcp-server |

### Restricoes de Seguranca

- **Proibido:** Tags XML (< >) no frontmatter
- **Proibido:** "claude" ou "anthropic" no nome da skill

---

## O Campo Description

Estrutura: `[O que faz] + [Quando usar] + [Capacidades-chave]`

### Boas Descricoes

```yaml
# Especifica e acionavel
description: Analisa arquivos de design do Figma e gera documentacao
  de handoff. Use quando o usuario faz upload de .fig, pede "design specs",
  "documentacao de componentes", ou "design-to-code handoff".

# Inclui frases de disparo
description: Gerencia fluxos de trabalho de projeto Linear incluindo
  planejamento de sprint e criacao de tarefas. Use quando o usuario
  menciona "sprint", "tarefas Linear", "planejamento de projeto".
```

### Descricoes Ruins

```yaml
# Muito vaga
description: Helps with projects.

# Faltam gatilhos
description: Creates sophisticated multi-page documentation systems.
```

---

## 3 Categorias de Skills

### Categoria 1: Criacao de Documentos e Ativos

Cria saidas consistentes e de alta qualidade (docs, apps, designs, codigo).

**Tecnicas:** Guias de estilo incorporados, templates, checklists de qualidade. Sem ferramenta externa -- usa capacidades nativas do Claude.

### Categoria 2: Automacao de Fluxo de Trabalho

Processos com multiplas etapas com metodologia consistente.

**Tecnicas:** Fluxo passo a passo com portoes de validacao, templates, ciclos de refinamento.

### Categoria 3: Aprimoramento de MCP

Orientacao de fluxo de trabalho sobre acesso a ferramentas MCP.

**Tecnicas:** Coordena multiplas chamadas MCP, incorpora expertise de dominio, tratamento de erros para problemas comuns.

---

## Skills + MCP

**MCP = A cozinha profissional** (acesso a ferramentas, dados)
**Skills = As receitas** (instrucoes passo a passo)

| MCP (Conectividade) | Skills (Conhecimento) |
|---|---|
| Conecta ao servico | Ensina a usar de forma eficaz |
| Acesso a dados e ferramentas | Captura fluxos de trabalho e boas praticas |
| O que pode fazer | Como deve fazer |

---

## Escrevendo Instrucoes Eficazes

### Seja Especifico e Acionavel

```markdown
# BOM
Execute `python scripts/validate.py --input {filename}` para verificar formato.
Se falhar, problemas comuns:
- Campos obrigatorios faltando (adicione ao CSV)
- Formatos de data invalidos (use YYYY-MM-DD)

# RUIM
Valide os dados antes de prosseguir.
```

### Inclua Tratamento de Erros

```markdown
## Problemas Comuns

### Falha na Conexao MCP
Se "Connection refused":
1. Verifique servidor MCP: Settings > Extensions
2. Confirme chave API valida
3. Reconecte: Settings > Extensions > [Servico] > Reconnect
```

### Referencie Recursos Claramente

```markdown
Antes de escrever consultas, consulte `references/api-patterns.md` para:
- Rate limiting
- Padroes de paginacao
- Codigos de erro
```

---

## 5 Padroes de Implementacao

### Padrao 1: Orquestracao Sequencial

Processos com multiplas etapas em ordem especifica.

```markdown
### Etapa 1: Criar Conta
Chamar MCP: `create_customer` (name, email, company)

### Etapa 2: Configurar Pagamento
Chamar MCP: `setup_payment_method`
Aguardar: verificacao

### Etapa 3: Criar Assinatura
Chamar MCP: `create_subscription` (plan_id, customer_id da Etapa 1)
```

**Tecnicas:** Ordenacao explicita, dependencias entre etapas, validacao em cada estagio, instrucoes de rollback.

### Padrao 2: Coordenacao Multi-MCP

Fluxos que abrangem multiplos servicos.

```markdown
Fase 1: Exportacao de Design (Figma MCP)
Fase 2: Armazenamento de Ativos (Drive MCP)
Fase 3: Criacao de Tarefas (Linear MCP)
Fase 4: Notificacao (Slack MCP)
```

**Tecnicas:** Separacao de fases, passagem de dados entre MCPs, validacao antes de avancar.

### Padrao 3: Refinamento Iterativo

Qualidade melhora com iteracao.

```markdown
Rascunho Inicial -> Verificacao de Qualidade -> Ciclo de Refinamento -> Finalizacao
```

**Tecnicas:** Criterios de qualidade explicitos, scripts de validacao, saber quando parar.

### Padrao 4: Selecao Consciente de Contexto

Mesmo resultado, ferramentas diferentes dependendo do contexto.

```markdown
- Arquivos grandes (>10MB): cloud storage MCP
- Docs colaborativos: Notion/Docs MCP
- Codigo: GitHub MCP
- Temporarios: armazenamento local
```

### Padrao 5: Inteligencia de Dominio

Adiciona conhecimento especializado alem do acesso a ferramentas.

```markdown
Antes de processar pagamento:
1. Verificar listas de sancoes
2. Verificar jurisdicao
3. Avaliar risco
4. Documentar decisao de conformidade
```

---

## Testes

### 3 Areas de Teste

#### 1. Testes de Disparo

```
Deve disparar:
- "Configure workspace ProjectHub"
- "Crie projeto no ProjectHub"

NAO deve disparar:
- "Qual o clima em SP?"
- "Me ajude com Python"
```

#### 2. Testes Funcionais

```
Teste: Criar projeto com 5 tarefas
Dado: Nome "Q4 Planning", 5 descricoes
Quando: Skill executa
Entao: Projeto criado, 5 tarefas vinculadas, 0 erros
```

#### 3. Comparacao de Desempenho

```
Sem skill: 15 mensagens, 3 falhas API, 12k tokens
Com skill: 2 perguntas, 0 falhas, 6k tokens
```

### Dica: Use a skill skill-creator

```
"Use a skill skill-creator para me ajudar a construir uma skill para [caso de uso]"
```

15-30 minutos para primeira skill funcional.

---

## Resolucao de Problemas

### Skill Nao Dispara

**Checklist:**
- Description muito generica?
- Inclui frases que usuarios realmente diriam?
- Menciona tipos de arquivo relevantes?

**Debug:** Pergunte ao Claude: "Quando voce usaria a skill [nome]?"

### Skill Dispara Demais

**Solucoes:**
1. Adicione gatilhos negativos: "NAO use para exploracao simples"
2. Seja mais especifico no description
3. Clarifique o escopo

### Instrucoes Nao Seguidas

**Causas comuns:**
1. Instrucoes muito verbosas -- use listas
2. Instrucoes criticas enterradas -- coloque no topo
3. Linguagem ambigua -- seja especifico
4. Para validacoes criticas: use scripts em vez de linguagem natural

### Problemas de Contexto Grande

**Solucoes:**
1. Mantenha SKILL.md abaixo de 5.000 palavras
2. Mova docs para references/
3. Reduza skills habilitadas (< 20-50 simultaneas)

---

## Distribuicao

### Instalacao pelo Usuario

1. Baixar pasta da skill
2. Compactar (zip)
3. Upload: Claude.ai > Settings > Capabilities > Skills

### Skills Organizacionais

Administradores podem implantar em todo workspace com atualizacoes automaticas.

### Via API

```
- Endpoint /v1/skills para gerenciamento
- Parametro container.skills na Messages API
- Funciona com Claude Agent SDK
```

### Padrao Aberto: Agent Skills

Skills sao um padrao aberto e portavel entre ferramentas -- mesma skill funciona independente da plataforma.

---

## Checklist Rapido

### Antes de Comecar
- [ ] 2-3 casos de uso concretos identificados
- [ ] Ferramentas identificadas (nativas ou MCP)

### Durante Desenvolvimento
- [ ] Pasta em kebab-case
- [ ] SKILL.md existe (ortografia exata)
- [ ] Frontmatter com delimitadores ---
- [ ] name: kebab-case
- [ ] description: O QUE + QUANDO
- [ ] Sem tags XML
- [ ] Instrucoes claras e acionaveis
- [ ] Tratamento de erros incluido
- [ ] Exemplos fornecidos

### Antes do Upload
- [ ] Testou disparo em tarefas obvias
- [ ] Testou que NAO dispara em topicos nao relacionados
- [ ] Testes funcionais passam

### Apos Upload
- [ ] Testar em conversas reais
- [ ] Monitorar sobre/sub-disparo
- [ ] Iterar na descricao

---

## Fontes

- [The Complete Guide to Building Skills for Claude (PDF)](https://resources.anthropic.com/hubfs/The-Complete-Guide-to-Building-Skill-for-Claude.pdf)
- [Skills Documentation - Anthropic](https://docs.anthropic.com/en/docs/agents-and-tools/skills)
- [GitHub: anthropics/skills](https://github.com/anthropics/skills)
- [Introducing Agent Skills - Blog](https://www.anthropic.com/research/agent-skills)
