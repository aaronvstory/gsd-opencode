# Workflows Reference

> Load this file when: Using GSD, managing PRs/Git, Windows troubleshooting, hooks setup, or understanding core dev principles
> Usage: `@~/.claude/ref/WORKFLOWS.md`
> Size: ~13k | Complements: `CLAUDE.md` (~3.2k) + `TOOLS.md` (~8.5k)

---

## GSD (Get Shit Done) Framework

### Repository & Git Structure

**Your Fork:** [https://github.com/aaronvstory/gsd-opencode](https://github.com/aaronvstory/gsd-opencode)

**Local Location:** `C:\claude\tools\gsd-opencode\`

**Git Remotes:**

| Remote | URL | Purpose |
|--------|-----|----------|
| `origin` | `aaronvstory/gsd-opencode` | **YOUR repo** - push changes here |
| `rokicool` | `rokicool/gsd-opencode` | Reference - check occasionally for fixes |
| `upstream` | `glittercowboy/get-shit-done` | Original Claude Code version (NOT OpenCode) |

**Symlinks (Live Updates):**

```
~/.config/opencode/command/gsd/     -> C:\claude\tools\gsd-opencode\gsd-opencode\command\gsd
~/.config/opencode/get-shit-done/   -> C:\claude\tools\gsd-opencode\gsd-opencode\get-shit-done\
```

### Git Workflow

```bash
# Make changes locally, then push to YOUR repo
cd C:\claude\tools\gsd-opencode
git add .
git commit -m "description"
git push origin main

# Check for updates (DO NOT auto-merge!)
git fetch rokicool
git fetch upstream
git log rokicool/main --oneline -5   # See what rokicool changed
git log upstream/main --oneline -5   # See what original changed

# Cherry-pick specific commits if wanted
git cherry-pick <commit-hash>
```

**IMPORTANT:** Never auto-sync with rokicool or upstream. Our version has significant adaptations.

### Available Commands (26 total)

| Command | Purpose |
|---------|----------|
| `/gsd:help` | Show all GSD commands and usage |
| `/gsd:new-project` | Initialize a new GSD project |
| `/gsd:create-roadmap` | Create project roadmap with milestones |
| `/gsd:new-milestone` | Add a new milestone |
| `/gsd:discuss-milestone` | Discuss and refine a milestone |
| `/gsd:complete-milestone` | Mark milestone as complete |
| `/gsd:add-phase` | Add a phase to a milestone |
| `/gsd:insert-phase` | Insert phase at specific position |
| `/gsd:remove-phase` | Remove a phase |
| `/gsd:discuss-phase` | Discuss and refine a phase |
| `/gsd:plan-phase` | Create detailed plan for a phase |
| `/gsd:research-phase` | Research requirements for a phase |
| `/gsd:execute-phase` | Execute a phase with wave-based parallelism |
| `/gsd:execute-plan` | Execute the current plan |
| `/gsd:verify-work` | Verify completed work |
| `/gsd:progress` | Show current progress |
| `/gsd:status` | Monitor background agent status |
| `/gsd:pause-work` | Pause current work |
| `/gsd:resume-work` | Resume paused work |
| `/gsd:map-codebase` | Map the codebase structure |
| `/gsd:consider-issues` | Consider potential issues |
| `/gsd:list-phase-assumptions` | List assumptions for a phase |
| `/gsd:plan-fix` | Plan a fix for an issue |
| `/gsd:add-todo` | Capture ideas as todos mid-session |
| `/gsd:check-todos` | Review and work on captured todos |
| `/gsd:debug` | Systematic debugging with persistent state |

### GSD File Structure

```
.gsd/
├── roadmap.md           # Project roadmap with milestones
├── milestones/          # Individual milestone details
│   ├── milestone-1.md
│   └── ...
├── phases/              # Phase details and plans
├── todos.md             # Captured todos
├── debug-state.md       # Debug session state
└── progress.md          # Progress tracking
```

---

## GitHub PR Review Workflow

### Review Bots to Watch For

| Check | Type | Priority | What It Does |
|-------|------|----------|---------------|
| **CI / tests** | Automated | High | Core test suite - usually should pass |
| **pytest** | Automated | High | Python tests - usually should pass |
| **GitGuardian** | Security | High | Secrets detection - always check |
| **GitHub Copilot** | AI Agent | Medium | Comment `@copilot` to trigger (creates NEW PR) |
| **Claude Code Review** | AI Review | Medium | Claude code-review plugin |
| **Greptile Review** | AI Review | Medium | Codebase-aware review |
| **CodeRabbit** | AI Review | Medium | Line-by-line feedback |
| **Sourcery** | AI Review | Low | Code quality suggestions |
| **Codoki PR Review** | AI Review | Low | Additional review |

### Claude Code GitHub Actions

**`claude.yml` - On-Demand Assistance:**

- Trigger: Mention `@claude` in issues or PR comments
- Use: Ask Claude to explain code, suggest fixes, update PR descriptions

**`claude-code-review.yml` - Automated Review:**

- Trigger: PR opened, synchronized, or marked ready
- Output: Review comments with suggestions

### GitHub Copilot Agent (`@copilot`)

**IMPORTANT:** Copilot creates a **sub-PR targeting YOUR branch**, not main.

**Flow:**

```
Your PR (#N): your-feature-branch -> main
         ^
Copilot PR (#M): copilot/sub-pr-N -> your-feature-branch
```

1. Comment `@copilot` on your PR
2. Copilot creates `copilot/sub-pr-XX` branch targeting YOUR branch
3. Copilot opens a draft PR with improvements
4. Merge Copilot's sub-PR into your branch
5. Your PR now includes Copilot's changes
6. Merge your PR to main

**Auto-trigger after creating PRs:**

```python
mcp__plugin_github_github__add_issue_comment(
    owner="aaronvstory",
    repo="repo-name",
    issue_number=PR_NUMBER,
    body="@copilot please review and improve this PR"
)
```

### Workflow: Checking PR Status

```python
# Check PR status and reviews
mcp__plugin_github_github__pull_request_read(
    method="get_status",
    owner="aaronvstory",
    repo="repo-name",
    pullNumber=46
)

# Get review comments
mcp__plugin_github_github__pull_request_read(
    method="get_review_comments",
    owner="aaronvstory",
    repo="repo-name",
    pullNumber=46
)
```

### Merge Guidance

**High priority (fix before merge):**

- CI/test failures on code you changed
- GitGuardian security alerts
- Critical bugs identified by AI reviewers

**Medium priority (review but may proceed):**

- AI reviewer suggestions (improvements, not blockers)
- Style/refactoring recommendations

**Merge command:**

```python
mcp__plugin_github_github__merge_pull_request(
    owner="aaronvstory",
    repo="repo-name",
    pullNumber=46,
    merge_method="squash"  # or "merge" or "rebase"
)
```

---

## Hooks Configuration

**Hooks MUST be configured in `~/.claude/settings.json`**, NOT in a separate `hooks.json` file.

### Common Mistake

Creating `~/.claude/hooks/hooks.json` does NOTHING. Claude Code ignores this file.

### Correct Configuration Location

```json
// ~/.claude/settings.json
{
  "hooks": {
    "SessionStart": [...],
    "UserPromptSubmit": [...],
    "PostToolUse": [...],
    "Stop": [...]
  }
}
```

### Currently Active Hooks

| Event | Hook | Purpose |
|-------|------|----------|
| `SessionStart` | `session_start_bootstrap.py` | Ensures `.claude/` files exist |
| `SessionStart` | `session_start_enforce_rules.py` | Injects critical rules |
| `SessionStart` | `gsd-check-update.js` | Checks GSD updates |
| `UserPromptSubmit` | `periodic_reminder.py` | Reminder every 10 messages |
| `PostToolUse` | `post_tool_use_format_and_log.py` | Logs Write/Edit ops |
| `Stop` | `stop_verify.py` | Verifies work before stop |
| `Stop` | `ralph_wiggum_stop.py` | Stops Ralph loops |

### Adding New Hooks

1. Create script in `~/.claude/hooks/`
2. Add to `~/.claude/settings.json` under correct event
3. Update `~/.claude/hooks/README.md`
4. Restart Claude Code

---

## Core Development Principles

### 1. Root Cause Analysis

**Why this matters:** Symptoms and root causes are different. Fix the cause, not the symptom.

Before fixing:

- Understand *why* the issue happens (5 Whys mindset)
- Use `rg`/`fd` to search for similar patterns across codebase
- Fix all affected locations, not just the first one
- Avoid try/catch guards that merely mask errors

### 2. No Substitutions (No Deviation)

**Why this matters:** Switching technologies when one fails is technical debt in disguise.

When encountering issues:

- Do NOT switch to alternative technologies just because current one is failing
- Fix the issue using the currently selected stack unless user explicitly approves change

**Examples:**

- Switching to SQLite instead of fixing PostgreSQL
- Switching to a different vector DB instead of fixing Pinecone
- Diagnose and fix the real cause of failure in existing system

### 3. Verification Loop

**Why this matters:** Changes without verification create cascading failures.

For each non-trivial change:

1. Make a small, targeted change
2. Run relevant tests/verification immediately
3. Analyze failures and fix them
4. Repeat until everything passes

Never:

- Dismiss failing tests/linters as "pre-existing"
- Declare completion with any failing checks
- Assume changes are correct without verification

### 4. Scope Completeness

**Why this matters:** Partial fixes look done but break later.

Before and during batch operations:

- Use `fd`/`rg` with explicit patterns to discover *all* relevant items
- Capture counts: "Found N items"
- Track: "Processed N/N" before declaring completion
- Check multiple locations (root, subdirectories, dot-directories)

### 5. Design Consistency (UI)

**Why this matters:** Inconsistent UI confuses users and wastes future dev time.

When editing or adding UI:

- Study 3-5 similar pages/components in existing codebase
- Reuse existing patterns (components, layout, typography, colors)
- Match provided mockups; avoid inventing new design systems

### 6. Requirements Completeness (Full Stack)

**Why this matters:** Incomplete features block deployment or cause bugs.

For each feature or field:

```
UI Fields -> API Endpoint -> Validation -> Business Logic -> Database Schema
```

Before declaring complete:

- Every UI field has corresponding API parameter, validation rule, and business logic
- Database changes include proper migrations and are tested
- End-to-end flows tested with realistic data

---

## Windows Environment

### Bash Background Jobs (CRITICAL)

**NEVER use bash `&` for background jobs on Windows** - they exit immediately in Git Bash.

**Why:** Windows Git Bash doesn't properly support background job management with `&`.

| Use Case | Correct Approach | Wrong Approach |
|----------|------------------|----------------|
| **Bash tool background** | `Bash({ command: "cmd", run_in_background: true })` | `bash_command &` |
| **Persistent servers** | `Start-Process powershell -ArgumentList '-NoExit','-Command','cmd'` | `bash_command &` |
| **Fire-and-forget (browser)** | Bash `&` is OK (doesn't need to persist) | N/A |

### CWD File Pollution Bug (FIXED - v2.1.5+)

**Status:** Fixed in local installation (2026-01-13)
**Fix Location:** `~/.claude/scripts/apply-cwd-fix.js`

**What was the bug:** Claude Code created malformed temp files in project directories (`cusersd0nbxxappdatalocaltempclaude-XXXX-cwd`) on EVERY bash command.

**Root Cause:** Windows paths weren't converted to Unix paths before use in bash commands.

**After Claude Code Updates:**

```bash
# Check if fix is still applied
node C:/Users/d0nbxx/.claude/scripts/check-cwd-fix.js

# Re-apply the fix if needed
node C:/Users/d0nbxx/.claude/scripts/apply-cwd-fix.js

# Clean up any pollution
powershell -File C:/Users/d0nbxx/.claude/scripts/cleanup-cwd-pollution.ps1 -Recursive
```

### "nul" File Bug

Claude Code sometimes creates a `nul` file in project root. Clean periodically:

```bash
rm -f nul 2>&1 && echo "nul removed" || echo "nul doesn't exist"
```

### Process Killing Rules (NON-NEGOTIABLE)

**NEVER kill by process name - kills Claude Code:**

```bash
taskkill //F //IM node.exe   # ❌ Kills Claude Code!
Stop-Process -Name node      # ❌ Kills Claude Code!
```

**ALWAYS kill by specific PID:**

```powershell
powershell -Command "(Get-NetTCPConnection -LocalPort 4100).OwningProcess"
powershell -Command "Stop-Process -Id <PID> -Force"
```

### PowerShell-First for System Ops

Use PowerShell (not bash) for: process management, port checking, complex paths.

**Git Bash Escalation Rule:** If you hit 2 command failures due to quoting/path weirdness in Git Bash, switch to PowerShell.

**PowerShell Backtick Rule:** Commands with backticks (`` `n``, `` `t``) or `$_` - use `powershell -Command` directly, NOT bash.

**Git Bash Path Quoting:** Use forward slashes: `"C:/path/to/dir/"` not `"C:\path\to\dir\"`

### Launchers (from `launchers/`)

```bash
# Production (full features)
launchers/primary/claude-code-production-launcher-fixed.bat

# Clean (troubleshooting)
launchers/right-click/claude-code-clean-launcher.bat

# Right-click context menu launchers
launchers/right-click/claude-code-rust-launcher.bat      # Rust version
launchers/right-click/claude-code-wsl-launcher.bat       # WSL version
```

### Claude Code Versions

```bash
# Node-based (PRIMARY)
win-claude-code --version

# Rust native (BACKUP)
C:\Users\d0nbx\.local\bin\claude.exe --version

# Update Node-based
npm install -g @anthropic-ai/claude-code@latest --ignore-scripts
```

---

## File Organization & Change Tracking

**Standard locations:**

- Documentation → `./docs/`
- Utility scripts → `./scripts/`
- Artifacts (summaries, generated READMEs) → `./docs/artifacts/`

**CHANGELOG:** Always update `./CHANGELOG` before committing code changes (if project maintains one).

---

## CSS Standards

Avoid `!important` except for carefully designed utility classes.

Preferred approach:

- Use scoped selectors for specificity (e.g., `body.login-page .form-control`)
- Work with the CSS cascade instead of fighting it
- Refactor conflicting rules instead of layering more `!important`
