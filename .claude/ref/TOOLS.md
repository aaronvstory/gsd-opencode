# Tools Reference

> Load this file when: Working with MCP tools, AI tools, or troubleshooting tool syntax
> Usage: `@~/.claude/ref/TOOLS.md`
> Size: ~8.5k | Complements: `CLAUDE.md` (~3.2k) + `WORKFLOWS.md` (~13k)

---

## Common MCP Tools - MCPSearch Queries

| Tool | MCPSearch Query |
|------|----------------|
| **Serena** | |
| List directory | `select:mcp__plugin_serena_serena__list_dir` |
| Find file | `select:mcp__plugin_serena_serena__find_file` |
| Read file | `select:mcp__plugin_serena_serena__read_file` |
| Replace content | `select:mcp__plugin_serena_serena__replace_content` |
| Get symbols | `select:mcp__plugin_serena_serena__get_symbols_overview` |
| Find symbol | `select:mcp__plugin_serena_serena__find_symbol` |
| Search pattern | `select:mcp__plugin_serena_serena__search_for_pattern` |
| **GitHub** | |
| PR read | `select:mcp__plugin_github_github__pull_request_read` |
| PR write | `select:mcp__plugin_github_github__create_pull_request` |
| Issue read | `select:mcp__plugin_github_github__issue_read` |
| **Exa** | |
| Web search | `select:mcp__exa__web_search_exa` |
| Code context | `select:mcp__exa__get_code_context_exa` |
| **DeepWiki** | |
| Ask question | `select:mcp__deepwiki__ask_question` |
| Read structure | `select:mcp__deepwiki__read_wiki_structure` |
| **Context7** | |
| Query docs | `select:mcp__context7__query-docs` |

---

## Serena Tools - Required Parameters

| Tool | Required Parameters | Common Mistakes |
|------|-------------------|-----------------|
| `list_dir` | `relative_path`, `recursive` | Forgetting `recursive` parameter |
| `find_file` | `file_mask`, `relative_path` | Using `name_pattern` instead of `file_mask` |
| `read_file` | `relative_path` | Using `offset`/`limit` instead of `start_line`/`end_line` |
| `replace_content` | `relative_path`, `needle`, `repl`, `mode` | All required, no omissions |
| `get_symbols_overview` | `relative_path` | Usually correct |
| `find_symbol` | `name_path_pattern`, `relative_path` | Usually correct |
| `search_for_pattern` | `substring_pattern` | Forgetting `relative_path` to narrow scope |

### Common Parameter Errors (AVOID)

```typescript
// WRONG - Missing required 'recursive' parameter
mcp__plugin_serena_serena__list_dir({ relative_path: "." })

// CORRECT - Include all required parameters
mcp__plugin_serena_serena__list_dir({ relative_path: ".", recursive: false })

// WRONG - Using 'name_pattern' instead of 'file_mask'
mcp__plugin_serena_serena__find_file({ name_pattern: "*.py" })

// CORRECT - Use 'file_mask' and include 'relative_path'
mcp__plugin_serena_serena__find_file({ file_mask: "*.py", relative_path: "." })

// WRONG - Using offset/limit (wrong tool parameters)
mcp__plugin_serena_serena__read_file({ relative_path: "file.py", offset: 0, limit: 50 })

// CORRECT - Use start_line/end_line (0-based indices)
mcp__plugin_serena_serena__read_file({ relative_path: "file.py", start_line: 0, end_line: 49 })
```

---

## Serena Code Intelligence

| Need | Serena Tool |
|------|-------------|
| File symbols | `mcp__plugin_serena_serena__get_symbols_overview` |
| Find symbol | `mcp__plugin_serena_serena__find_symbol` |
| Find references | `mcp__plugin_serena_serena__find_referencing_symbols` |
| Symbol body/types | `mcp__plugin_serena_serena__find_symbol` with `include_body: true` |
| Search patterns | `mcp__plugin_serena_serena__search_for_pattern` |

**Serena Dashboard:** [http://127.0.0.1:24282/dashboard/index.html](http://127.0.0.1:24282/dashboard/index.html)

---

## MCPSearch Common Errors and Fixes

**Error: "Field required: recursive"**

```typescript
// WRONG - missing required parameter
mcp__plugin_serena_serena__list_dir({ relative_path: "." })

// CORRECT - include all required parameters
mcp__plugin_serena_serena__list_dir({ relative_path: ".", recursive: false })
```

**Error: "Tool not found"**

```typescript
// WRONG - forgot to load tool first
mcp__plugin_serena_serena__list_dir({ relative_path: "." })

// CORRECT - load tool first
MCPSearch({ query: "select:mcp__plugin_serena_serena__list_dir" })
mcp__plugin_serena_serena__list_dir({ relative_path: ".", recursive: false })
```

### Why MCPSearch Over MCP-CLI

| Aspect | MCP-CLI (deprecated) | MCPSearch (current) |
|--------|---------------------|---------------------|
| Windows | Broken (path mangling) | Works natively |
| Maintenance | Requires patch after updates | No patching needed |
| Error handling | Silent failures | Clear schema errors |
| Token usage | Zero upfront | Deferred/on-demand |

---

## AI Tool Roles

| AI Tool | Role | Best For |
|---------|------|----------|
| **Gemini CLI** | Best practices validation | Web standards, accessibility, current knowledge |
| **Codex CLI** | Deep logic analysis | Algorithms, architecture, type safety, edge cases |
| **Copilot CLI** | Multi-aspect analysis | Algorithms, architecture, type safety, edge cases, polish, bugs, enhancements |

### Codex CLI Auto-Delegation (GPT-5.1-Codex-Max)

The `codex-executor` subagent invokes OpenAI's Codex CLI with `gpt-5.1-codex-max` for superior deep reasoning. **Auto-delegate** when:

| Trigger | Example |
|---------|----------|
| Complex multi-file implementations | "Implement auth system across 5+ files" |
| Stuck after 2+ attempts | Repeated failures on same problem |
| Deep analysis/debugging needed | Root cause investigation, perf optimization |
| User wants "second opinion" | "Double-check this", "verify my approach" |
| Reasoning-heavy tasks | Algorithm design, architectural decisions |
| Large refactors | Pattern migrations, framework upgrades |

### Gemini, Copilot & Codex Proactive Usage

| When | Use | Command |
|------|-----|----------|
| Completing significant feature | `/dualcheck` | Gemini + Codex review |
| Complex debugging | `/triplecheck` | Gemini + Codex + Copilot review |
| Unsure about approach | Gemini | Best practices check |
| Logic/algorithm issues | Codex | Deep reasoning |

---

## DeepWiki for Public GitHub Repos

**For ANY public GitHub repo, query DeepWiki BEFORE reading code directly.**
DeepWiki = pre-indexed understanding, ZERO context cost.

### Usage Pattern

```python
# 1. Check repo structure
mcp__deepwiki__read_wiki_structure(repoName="owner/repo")

# 2. Ask architectural questions
mcp__deepwiki__ask_question(
    repoName="owner/repo",
    question="How does the authentication system work?"
)
```

### Decision Order

1. DeepWiki (if public repo) → ZERO context cost
2. Direct file reading → only when necessary

---

## Exa MCP - External Research

Use Exa MCP for external research (superior to generic web search):

- `mcp__exa__web_search_exa` - Web research, current standards, best practices
- `mcp__exa__get_code_context_exa` - API docs, SDK examples, library patterns

### When to Use Exa

**Current standards (OWASP, WCAG), API docs, framework patterns, security research, code examples.**

**RULE:** If there is ANY uncertainty about external facts, standards, APIs, best practices, or security guidance, use Exa MCP before making claims.

### Usage Example

```python
# Web research
mcp__exa__web_search_exa(query="OWASP password requirements 2024")

# Code context (SDK docs, library examples)
mcp__exa__get_code_context_exa(
    query="Express.js middleware authentication pattern",
    repository="expressjs/express"
)
```

---

## Context7 - Knowledge Base Queries

Query internal/external knowledge bases for context-specific information.

### Usage Pattern

```python
mcp__context7__query_docs(
    query="How does payment processing work in our system?",
    include_citations=True
)
```

### When to Use

- Seeking internal documentation
- Understanding system architecture specifics
- Referencing past decisions or patterns
- Getting context-specific best practices

---

## Frontend Design Skill (Proactive)

**ALWAYS invoke the `frontend-design` skill** when building any frontend interface, component, or page.

### When to Use

Automatically activate for:

- Building web components (buttons, forms, cards, modals)
- Creating pages (landing, dashboard, portfolio, etc.)
- Any HTML/CSS/JS, React, Vue, or frontend work
- UI design tasks
- "Make it look good" requests

### How to Invoke

```bash
Skill(skill: "frontend-design")
```

### Key Principles

- **Bold aesthetic direction** - No cookie-cutter designs
- **Distinctive typography** - NEVER use Inter, Roboto, Arial
- **Cohesive color palettes** - Dominant + sharp accents
- **Motion & animation** - High-impact moments
- **Unexpected layouts** - Asymmetry, overlap, grid-breaking

### Anti-Patterns to Avoid

- Generic purple gradients on white
- Overused font families (Inter, Space Grotesk, Roboto)
- Predictable layouts and component patterns
- Cookie-cutter design lacking context-specific character

**Rule:** When user asks for ANY frontend work, invoke `frontend-design` FIRST before writing code.
