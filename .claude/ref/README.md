# Reference Files

This directory contains detailed reference documentation for Claude Code workflows and tools. These files are designed to be **loaded on-demand** to keep the main `CLAUDE.md` small and focused.

---

## Files

### `TOOLS.md` (~8.5k)

**Load when:** Working with MCP tools, AI reviews, or troubleshooting tool syntax.

**Contains:**

- MCP tool MCPSearch queries (Serena, GitHub, Exa, DeepWiki, Context7)
- Serena parameter tables and common errors
- Serena code intelligence patterns
- AI tool roles (Gemini, Codex, Copilot)
- DeepWiki usage patterns
- Exa MCP external research
- Context7 knowledge base queries
- Frontend Design skill invocation

**How to load in Claude Code:**

```
@~/.claude/ref/TOOLS.md
```

---

### `WORKFLOWS.md` (~13k)

**Load when:** Using GSD, managing PRs/Git, troubleshooting Windows issues, setting up hooks, or understanding development principles.

**Contains:**

- GSD Framework commands and file structure
- GitHub PR review workflow and bots
- Hooks configuration and active hooks list
- Core development principles (Root Cause, No Substitutions, Verification Loop, etc.)
- Windows environment constraints (bash jobs, CWD bug, nul file, process killing)
- File organization and CSS standards

**How to load in Claude Code:**

```
@~/.claude/ref/WORKFLOWS.md
```

---

## Token Budget Impact

| File | Size | When Loaded |
|------|------|-------------|
| `CLAUDE.md` | ~3.2k | Always (main config) |
| `TOOLS.md` | ~8.5k | On-demand (tool usage) |
| `WORKFLOWS.md` | ~13k | On-demand (GSD/PR/env work) |
| **Total when all loaded** | ~24.7k | - |
| **vs. Original monolithic** | ~40k | Always |
| **Token savings** | **~38% reduction** | - |

---

## Loading Strategy

**Best Practice:**

1. Start with `CLAUDE.md` - always available, covers fundamentals
2. Mid-session, load `TOOLS.md` when you need MCP syntax or tool parameters
3. Mid-session, load `WORKFLOWS.md` when working with GSD, PRs, or Windows troubleshooting
4. Keep loaded references in context only as long as needed

**Example Session:**

```
[User starts Claude Code session]
  -> CLAUDE.md auto-loaded
  -> Read about TodoWrite, AskUserQuestion, Serena hard requirements

[User says "use Serena to find all Python files"]
  -> Claude loads @~/.claude/ref/TOOLS.md
  -> Finds MCPSearch queries and parameters
  -> Executes with correct syntax
  -> Unloads TOOLS.md when done

[User says "create a new GSD milestone"]
  -> Claude loads @~/.claude/ref/WORKFLOWS.md
  -> Reviews GSD commands and file structure
  -> Executes `/gsd:new-milestone`
  -> Keeps loaded while working through milestone phases
```

---

## Why Split into Three Files?

**Problem with monolithic CLAUDE.md (~40k):**

- Massive token usage, always loaded
- Hard to find specific sections
- Mixing"always needed" rules with "sometimes needed" reference tables
- Difficulty updating without affecting other sections
- Noise during sessions where you're not using all features

**Solution: Three-file architecture:**

1. **CLAUDE.md** (~3.2k) - Core rules, must always be understood
   - Path junction, AskUserQuestion, TodoWrite, Exa, MCPSearch 3-step, Serena hard enforcement, Session management, Git safety, Windows constraints, Completion checklist

2. **TOOLS.md** (~8.5k) - MCP syntax and tool reference
   - Load when working with tools, AI reviews, or debugging tool errors
   - Extensive parameter tables and usage examples
   - Reusable across all projects

3. **WORKFLOWS.md** (~13k) - Process and environment reference
   - Load when using GSD, managing PRs, Windows troubleshooting, or hooks
   - Contains "why" explanations for core principles
   - Environment-specific (Windows, launchers, versions)

**Benefits:**

✅ **38% token savings** when loading all 3 files vs. monolithic (~24.7k vs. ~40k)  
✅ **Lower baseline context usage** - only CLAUDE.md (~3.2k) always loaded  
✅ **Faster lookups** - smaller files = quicker search/navigation  
✅ **Easier maintenance** - changes to Tools don't affect Workflows or vice versa  
✅ **Reusable** - TOOLS.md and WORKFLOWS.md can be referenced across projects  
✅ **Clearer intent** - file names and organization show what each covers

---

## Quick Reference: What Goes Where?

| Topic | File | Load When |
|-------|------|----------|
| Path junction, critical rules | CLAUDE.md | Always (main) |
| AskUserQuestion, TodoWrite | CLAUDE.md | Always (main) |
| MCPSearch 3-step pattern | CLAUDE.md | Always (main) |
| Serena activation & hard requirements | CLAUDE.md | Always (main) |
| Session management & logging | CLAUDE.md | Always (main) |
| Git safety rules | CLAUDE.md | Always (main) |
| Windows quick constraints | CLAUDE.md | Always (main) |
| Completion checklist | CLAUDE.md | Always (main) |
| --- | --- | --- |
| MCP tool MCPSearch queries | TOOLS.md | Using tools |
| Serena parameter tables & examples | TOOLS.md | Working with Serena |
| AI tool roles (Gemini, Codex, Copilot) | TOOLS.md | Reviews/analysis |
| DeepWiki usage patterns | TOOLS.md | Public repo work |
| Exa external research | TOOLS.md | Need current data |
| Context7 knowledge base | TOOLS.md | Need internal context |
| Frontend Design skill | TOOLS.md | Building UI |
| --- | --- | --- |
| GSD commands & file structure | WORKFLOWS.md | Using GSD |
| GitHub PR review & bots | WORKFLOWS.md | Managing PRs |
| Hooks configuration & examples | WORKFLOWS.md | Setting up hooks |
| Core dev principles (Root Cause, etc.) | WORKFLOWS.md | Understanding philosophy |
| Windows bash jobs & CWD bug | WORKFLOWS.md | Windows issues |
| Process killing rules | WORKFLOWS.md | Windows cleanup |
| PowerShell-first ops | WORKFLOWS.md | System operations |
| Launchers & versions | WORKFLOWS.md | Environment setup |
| File organization & CSS standards | WORKFLOWS.md | Project structure |

---

## Future Maintenance

If these files grow beyond recommended sizes:

- **TOOLS.md > 12k:** Consider splitting into `TOOLS_MCP.md` and `TOOLS_AI.md`
- **WORKFLOWS.md > 18k:** Consider splitting into `WORKFLOWS_GSD.md` and `WORKFLOWS_WINDOWS.md`
- **CLAUDE.md > 6k:** Consider moving less-critical rules to WORKFLOWS.md

For now, current split is optimal.
