# Tool and Agent Mappings: Claude Code → OpenCode

This document maps all Claude Code tools, agents, and paths to their OpenCode equivalents.

## Tool Mappings

| Claude Code | OpenCode | Notes |
|-------------|-----------|-------|
| `AskUserQuestion` | `question` | Same functionality, different name |
| `Bash` | `bash` | Direct 1:1 mapping |
| `Edit` | `edit` | Direct 1:1 mapping |
| `Glob` | `glob` | Direct 1:1 mapping |
| `Grep` | `grep` | Direct 1:1 mapping |
| `Read` | `read` | Direct 1:1 mapping |
| `WebFetch` | `webfetch` | Direct 1:1 mapping |
| `Write` | `write` | Direct 1:1 mapping |
| `SlashCommand` | Command syntax | OpenCode uses `/command` directly |
| `Task` | Not documented | May use agent invocation or similar |

## Agent Mappings

| Claude Code | OpenCode | Notes |
|-------------|-----------|-------|
| `Explore` | `Explore` | Direct 1:1 mapping (same name) |
| `general-purpose` | `general` | OpenCode's general subagent |

## Path Mappings

| Claude Code | OpenCode | Notes |
|-------------|-----------|-------|
| `~/.claude/` | `~/.config/opencode/` | Global config directory |
| `./.claude/` | `.opencode/` | Local config directory |
| `commands/` | `command/` | Command folder (singular not plural) |

## MCP Tools

MCP tools should be preserved as-is. Format: `mcp__{server_name}__{tool_name}`

Examples:
- `mcp__context7__*` → No change
- `mcp__filesystem__read_file` → No change

## Windows File Operations (CRITICAL)

**On Windows, native `Write` and `Edit` tools FAIL with multi-line content** due to bash/CMD quoting issues.

**ALWAYS use MCP tools for file operations on Windows:**

| Native Tool | MCP Alternative (PRIMARY) | MCP Fallback |
|-------------|---------------------------|--------------|
| `Write` | `serena_create_text_file` | `desktop-commander_write_file` |
| `Edit` | `serena_replace_content` | `desktop-commander_edit_block` |
| `Read` | `serena_read_file` | `desktop-commander_read_file` |

### Serena MCP (Preferred)

Requires project activation first:
```python
serena_activate_project(project="project_folder_name")
serena_create_text_file(relative_path="docs/file.md", content="# Multi-line\n\nContent works!")
serena_replace_content(relative_path="file.md", mode="literal", needle="find", repl="replace")
```

### Desktop Commander MCP (Fallback)

Use when Serena unavailable (no `.serena/project.yml`). Requires **absolute paths**:
```python
desktop-commander_write_file(path="C:\\full\\path\\file.md", content="# Content")
desktop-commander_edit_block(file_path="C:\\path\\file.md", old_string="find", new_string="replace")
```

### Never Do This on Windows

- `bash echo >` or `cat <<EOF` for file creation
- Native `Write`/`Edit` for multi-line content
- Heredocs in bash commands for file content

**This applies to ALL GSD workflows and subagents.**

## Translation Rules

1. **Tool Names**: Replace Claude Code tool names with OpenCode equivalents in all references
2. **Agent Names**: Translate `general-purpose` to `general`, keep `Explore` as-is
3. **Paths**: Replace all instances of `~/.claude/` and `./.claude/` with OpenCode paths
4. **Command Invocations**: Use `/command` syntax (no `SlashCommand` tool)
5. **Subagent Spawning**: Use appropriate OpenCode agent invocation syntax
6. **MCP Tools**: Preserve all MCP tool names unchanged

## Command File Headers

OpenCode commands use this frontmatter format:
```yaml
---
name: gsd:command-name
description: Command description
---
```

The command file name (minus `.md`) becomes the command name.

## Allowed Tools in Command Files

When listing `allowed-tools` in command files, use OpenCode tool names:
```yaml
---
allowed-tools:
  - Read
  - Write
  - Edit
  - Bash
  - Glob
  - Grep
  - question
  - webfetch
---
```

## File References in Commands

Use `@` syntax for file references in OpenCode commands:
```
@.planning/PROJECT.md
@~/.config/opencode/get-shit-done/templates/project.md
```

## Agent Invocation

For spawning subagents (like in map-codebase), use OpenCode's agent syntax:
- Primary agent: Build or Plan
- Subagents: General, Explore

The exact syntax for programmatic agent invocation should follow OpenCode conventions.
