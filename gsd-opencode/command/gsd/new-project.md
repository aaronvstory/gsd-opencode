---
name: gsd:new-project
description: Initialize a new project with deep context gathering and PROJECT.md
---

<objective>

Initialize a new project through comprehensive context gathering.

This is the most leveraged moment in any project. Deep questioning here means better plans, better execution, better outcomes.

Creates `.planning/` with PROJECT.md and config.json.

</objective>

<execution_context>

@~/.config/opencode/get-shit-done/references/principles.md
@~/.config/opencode/get-shit-done/references/questioning.md
@~/.config/opencode/get-shit-done/templates/project.md
@~/.config/opencode/get-shit-done/templates/config.json

</execution_context>

<process>

<step name="setup">

**MANDATORY FIRST STEP — Execute these checks before ANY user interaction:**

1. **Abort if project exists:**
   Use `desktop-commander_get_file_info(path=".planning/PROJECT.md")` to check if project already exists:
   - If file info returned (file exists) → ERROR: Project already initialized. Use /gsd:progress. Stop execution.
   - If error "not found" → Continue (project not initialized yet)

2. **Initialize git repo in THIS directory** (required even if inside a parent repo):
   Use `desktop-commander_get_file_info(path=".git")` to check for existing repo:
   - If exists (file or directory) → "Git repo exists in current directory"
   - If not found → Run `git init` using Bash tool, then "Initialized new git repo"

3. **Detect existing code (brownfield detection):**
   
   **Find code files:**
   Use `desktop-commander_start_search(path=".", pattern="*.ts", searchType="files")` and similar searches for .js, .py, .go, .rs, .swift, .java extensions.
   Then use `desktop-commander_get_more_search_results(sessionId=...)` to get results.
   Filter out any paths containing "node_modules" or ".git".
   Store first 20 results as CODE_FILES.
   
   **Check for package manifests (HAS_PACKAGE):**
   Use `desktop-commander_get_file_info` for each of these files:
   - `package.json`
   - `requirements.txt`
   - `Cargo.toml`
   - `go.mod`
   - `Package.swift`
   Set HAS_PACKAGE = "yes" if ANY of these exist.
   
   **Check for existing codebase map (HAS_CODEBASE_MAP):**
   Use `desktop-commander_get_file_info(path=".planning/codebase")`:
   - If exists → Set HAS_CODEBASE_MAP = "yes"
   - If not found → Set HAS_CODEBASE_MAP = "no"

   **You MUST run all checks above using Desktop Commander tools before proceeding.**

</step>

<step name="brownfield_offer">

**If existing code detected and .planning/codebase/ doesn't exist:**

Check the results from setup step:
- If `CODE_FILES` is non-empty OR `HAS_PACKAGE` is "yes"
- AND `HAS_CODEBASE_MAP` is NOT "yes"

Use question:
- header: "Existing Code"
- question: "I detected existing code in this directory. Would you like to map the codebase first?"
- options:
  - "Map codebase first" — Run /gsd:map-codebase to understand existing architecture (Recommended)
  - "Skip mapping" — Proceed with project initialization

**If "Map codebase first":**
```
Run `/gsd:map-codebase` first, then return to `/gsd:new-project`
```
Exit command.

**If "Skip mapping":** Continue to question step.

**If no existing code detected OR codebase already mapped:** Continue to question step.

</step>

<step name="question">

**1. Open (FREEFORM — do NOT use question):**

Ask inline: "What do you want to build?"

Wait for their freeform response. This gives you the context needed to ask intelligent follow-up questions.

**2. Follow the thread (NOW use question):**

Based on their response, use question with options that probe what they mentioned:
- header: "[Topic they mentioned]"
- question: "You mentioned [X] — what would that look like?"
- options: 2-3 interpretations + "Something else"

**3. Sharpen the core:**

Use question:
- header: "Core"
- question: "If you could only nail one thing, what would it be?"
- options: Key aspects they've mentioned + "All equally important" + "Something else"

**4. Find boundaries:**

Use question:
- header: "Scope"
- question: "What's explicitly NOT in v1?"
- options: Things that might be tempting + "Nothing specific" + "Let me list them"

**5. Ground in reality:**

Use question:
- header: "Constraints"
- question: "Any hard constraints?"
- options: Relevant constraint types + "None" + "Yes, let me explain"

**6. Decision gate:**

Use question:
- header: "Ready?"
- question: "Ready to create PROJECT.md, or explore more?"
- options (ALL THREE REQUIRED):
  - "Create PROJECT.md" — Finalize and continue
  - "Ask more questions" — I'll dig deeper
  - "Let me add context" — You have more to share

If "Ask more questions" → check coverage gaps from `questioning.md` → return to step 2.
If "Let me add context" → receive input via their response → return to step 2.
Loop until "Create PROJECT.md" selected.

</step>

<step name="project">

Synthesize all context into `.planning/PROJECT.md` using the template from `templates/project.md`.

**For greenfield projects:**

Initialize requirements as hypotheses:

```markdown
## Requirements

### Validated

(None yet — ship to validate)

### Active

- [ ] [Requirement 1]
- [ ] [Requirement 2]
- [ ] [Requirement 3]

### Out of Scope

- [Exclusion 1] — [why]
- [Exclusion 2] — [why]
```

All Active requirements are hypotheses until shipped and validated.

**For brownfield projects (codebase map exists):**

Infer Validated requirements from existing code:

1. Read `.planning/codebase/ARCHITECTURE.md` and `STACK.md`
2. Identify what the codebase already does
3. These become the initial Validated set

```markdown
## Requirements

### Validated

- ✓ [Existing capability 1] — existing
- ✓ [Existing capability 2] — existing
- ✓ [Existing capability 3] — existing

### Active

- [ ] [New requirement 1]
- [ ] [New requirement 2]

### Out of Scope

- [Exclusion 1] — [why]
```

**Key Decisions:**

Initialize with any decisions made during questioning:

```markdown
## Key Decisions

| Decision | Rationale | Outcome |
|----------|-----------|---------|
| [Choice from questioning] | [Why] | — Pending |
```

**Last updated footer:**

```markdown
---
*Last updated: [date] after initialization*
```

Do not compress. Capture everything gathered.

</step>

<step name="mode">

Ask workflow mode preference:

Use question:

- header: "Mode"
- question: "How do you want to work?"
- options:
  - "Interactive" — Confirm at each step
  - "YOLO" — Auto-approve, just execute

</step>

<step name="depth">

Ask planning depth preference:

Use question:

- header: "Depth"
- question: "How thorough should planning be?"
- options:
  - "Quick" — Ship fast, minimal phases/plans (3-5 phases, 1-3 plans each)
  - "Standard" — Balanced scope and speed (5-8 phases, 3-5 plans each)
  - "Comprehensive" — Thorough coverage, more phases/plans (8-12 phases, 5-10 plans each)

**Depth controls compression tolerance, not artificial inflation.** All depths use 2-3 tasks per plan. Comprehensive means "don't compress complex work"—it doesn't mean "pad simple work to hit a number."

Create `.planning/config.json` with chosen mode and depth using `templates/config.json` structure.

</step>

<step name="commit">

```bash
git add .planning/PROJECT.md .planning/config.json
git commit -m "docs: initialize [project-name] - [One-liner from PROJECT.md]"
```

Note: Replace `[project-name]` and `[One-liner from PROJECT.md]` with actual values extracted during the questioning phase.

</step>

<step name="done">

Present completion with next steps (see ~/.config/opencode/get-shit-done/references/continuation-format.md):

```
Project initialized:

- Project: .planning/PROJECT.md
- Config: .planning/config.json (mode: [chosen mode])
[If .planning/codebase/ exists:] - Codebase: .planning/codebase/ (7 documents)

---

## ▶ Next Up

**[Project Name]** — create roadmap

`/gsd:create-roadmap`

*`/clear` first → fresh context window*

---

```

</step>

</process>

<output>

- `.planning/PROJECT.md`
- `.planning/config.json`

</output>

<success_criteria>

- [ ] Deep questioning completed (not rushed)
- [ ] PROJECT.md captures full context with evolutionary structure
- [ ] Requirements initialized as hypotheses (greenfield) or with inferred Validated (brownfield)
- [ ] Key Decisions table initialized
- [ ] config.json has workflow mode
- [ ] All committed to git

</success_criteria>
