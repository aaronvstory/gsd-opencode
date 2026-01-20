# Claude Code Global Configuration (Windows-First)

> Scope: This is a **global** Claude Code configuration for this machine (`~/.claude/CLAUDE.md`).
> **Context Strategy:** This file stays small. For complex Tools (Serena/MCP) or Workflows (GSD/PRs), explicitly load reference files below.

---

## 📚 Reference Files (Load On-Demand)

| File | When to Load | Size |
|------|-------------|------|
| `@~/.claude/ref/TOOLS.md` | Using Serena/MCP, AI Reviews, debugging tool errors, checking MCP parameters | ~8.5k |
| `@~/.claude/ref/WORKFLOWS.md` | Using GSD commands, managing PRs/Git, Windows issues, hooks setup, dev principles | ~13k |

**Total saved:** Loading all 3 files = ~24.7k vs. monolithic = ~40k (38% reduction).

---

## ⚡ Critical Rules (Always Active)

### Path Junction

**`C:\claude` and `F:\claude` are the SAME directory** (Windows junction/symlink). Always use consistent paths - prefer `C:\claude\` as canonical.

### Use AskUserQuestion (NON-NEGOTIABLE)

**NEVER ask questions in plain text. If you'd type `?` to the user → STOP and use `AskUserQuestion`.**

```typescript
AskUserQuestion({
  questions: [{
    question: "What should we do next?",
    header: "Next step",
    multiSelect: false,
    options: [
      { label: "Fix bugs (Recommended)", description: "Address identified issues" },
      { label: "Run tests", description: "Verify current state" }
    ]
  }]
})
```

### TodoWrite Enforcement (3+ Steps)

For any task with 3+ steps:
1. **Immediately** use `TodoWrite` to create a todo list
2. **Update** via `TodoWrite` as work progresses (mark in_progress → completed)
3. **Verify** remaining scope before declaring completion

### When Uncertain: Use Exa

**Before claiming facts about current standards, APIs, best practices, or security:** Use Exa MCP to verify. Never guess when uncertain—be evidence-based.

---

## MCPSearch & Tool Loading

**Rule:** MCP tools are **deferred** (not auto-loaded). You must search/load schemas before use.

### 3-Step Pattern (Mandatory)

```typescript
// 1. Load tool schema
MCPSearch({ query: "select:mcp__plugin_serena_serena__list_dir" })

// 2. Read schema for required parameters (returned in result)

// 3. Call with correct parameters
mcp__plugin_serena_serena__list_dir({ relative_path: ".", recursive: false })
```

### Serena MCP: Safe Large File Edits

**Activation Pattern:**

```typescript
// 1. Load schema first
MCPSearch({ query: "serena" })

// 2. Activate project (repo root = folder with .git/)
mcp__plugin_serena_serena__activate_project({ project: "ACTUAL_REPO_ROOT" })

// 3. Now use Serena tools with correct parameters
mcp__plugin_serena_serena__list_dir({ relative_path: ".", recursive: false })
```

**Hard Enforcement (STOP and use Serena if ANY apply):**

- Markdown file > 500 lines
- JSON file > 1000 lines
- Any file needing multiple edits
- Native Edit would hang

*For detailed parameter tables and common errors, load `@~/.claude/ref/TOOLS.md`*

---

## Session Management

### On Session Start

1. Ensure `.claude/` directory exists in repo (create if missing)
2. Check DeepWiki if public GitHub repo
3. Read last 50 lines of `.claude/session-log.md` for context

**Nesting Rule:** When repo root IS `.claude` (e.g., `~/.claude`), write files directly to repo root—do NOT create `.claude/.claude/`.

### Mandatory Logging

After EVERY non-trivial exchange (edits, commands, decisions), append to `.claude/session-log.md`:

```markdown
## YYYY-MM-DD HH:MM - [Brief Task Name]

- **What changed:** [files/areas modified]
- **Why:** [decision/constraint/requirement]
- **Verified:** [commands/tests run, or "not run yet"]
```

**Note:** If session-log grows > 500 lines, use Serena MCP for edits.

---

## Git Safety (Non-Negotiable)

**Why these rules exist:**

| Rule | Why | Consequence of Breaking |
|------|-----|------------------------|
| Never `--no-verify` | Hooks catch real issues (secrets, bad code) | Secrets leak, CI fails, bad commits slip in |
| Fix failing tests/hooks before completion | Incomplete work + failing checks = broken main | Cascading failures, team blocked |
| Provide clickable PR link after push | Reviewer can't find your work otherwise | PR gets lost, review delayed |

**Completion is prohibited** while tests/hooks are failing.

---

## Windows Environment Constraints

**For detailed troubleshooting, load `@~/.claude/ref/WORKFLOWS.md`**

Quick summary:

1. **Bash Background:** NEVER use `&` (exits immediately). Use `run_in_background: true` or `Start-Process`.
2. **Process Killing:** NEVER kill by name (kills Claude Code). ALWAYS find PID and kill PID.
3. **Pathing:** Use forward slashes `C:/path/`.
4. **Cleanliness:** Delete `nul` files that appear.

---

## Completion Checklist

Before declaring work complete:

- [ ] Searched for existing scripts/patterns?
- [ ] Created/Updated `TodoWrite` list if 3+ steps?
- [ ] All relevant tests & linters passing?
- [ ] Behavior verified in running environment?
- [ ] Session-log updated?
- [ ] No `nul` files in project root?
- [ ] Changes tracked (commit message clear, CHANGELOG updated if applicable)?

---

## Architecture Context

**Workspace:** `C:\claude` (Windows Dev Environment)

- **`launchers/`**: Claude Code scripts (Git Bash based).
- **`tools/gsd-opencode/`**: GSD Framework (symlinked to `~/.config/opencode/`).
- **`MCPs/`**: Server configurations.
- **`.claude/`**: Session state, logs, hooks, settings.

**Permission Hierarchy:**
Global (`~/.claude/settings.json`) > Project (`.claude/settings.json`) > Local (`.claude/settings.local.json`).

---

## Next Steps

For detailed information on:

- **Tools, MCP syntax, AI reviews** → Load `@~/.claude/ref/TOOLS.md`
- **GSD, PRs, Git, Windows issues, hooks** → Load `@~/.claude/ref/WORKFLOWS.md`
