---
name: gsd:create-roadmap
description: Create roadmap with phases for the project
allowed-tools:
  - Read
  - Write
  - Bash
  - question
  - Glob
  - desktop-commander_get_file_info
---

<objective>
Create project roadmap with phase breakdown.

Roadmaps define what work happens in what order. Run after /gsd:new-project.
</objective>

<execution_context>
@~/.config/opencode/get-shit-done/workflows/create-roadmap.md
@~/.config/opencode/get-shit-done/templates/roadmap.md
@~/.config/opencode/get-shit-done/templates/state.md
</execution_context>

<context>
@.planning/PROJECT.md
@.planning/config.json
</context>

<process>

<step name="validate">
**Verify project exists:**
Use `desktop-commander_get_file_info(path=".planning/PROJECT.md")`:
- If file info returned → Continue (project exists)
- If error "not found" → ERROR: No PROJECT.md found. Run /gsd:new-project first. Stop execution.
</step>

<step name="check_existing">
**Check if roadmap already exists:**

Use `desktop-commander_get_file_info(path=".planning/ROADMAP.md")`:
- If file info returned → ROADMAP_EXISTS (proceed to ask user)
- If error "not found" → NO_ROADMAP (continue to create)

**If ROADMAP_EXISTS:**
Use question:
- header: "Roadmap exists"
- question: "A roadmap already exists. What would you like to do?"
- options:
  - "View existing" - Show current roadmap
  - "Replace" - Create new roadmap (will overwrite)
  - "Cancel" - Keep existing roadmap

If "View existing": `cat .planning/ROADMAP.md` and exit
If "Cancel": Exit
If "Replace": Continue with workflow
</step>

<step name="create_roadmap">
Follow the create-roadmap.md workflow starting from detect_domain step.

The workflow handles:
- Domain expertise detection
- Phase identification
- Research flags for each phase
- Confirmation gates (respecting config mode)
- ROADMAP.md creation
- STATE.md initialization
- Phase directory creation
- Git commit
</step>

<step name="done">
```
Roadmap created:
- Roadmap: .planning/ROADMAP.md
- State: .planning/STATE.md
- [N] phases defined

---

## ▶ Next Up

**Phase 1: [Name]** — [Goal from ROADMAP.md]

`/gsd:plan-phase 1`

*`/clear` first → fresh context window*

---

**Also available:**
- `/gsd:discuss-phase 1` — gather context first
- `/gsd:research-phase 1` — investigate unknowns
- Review roadmap

---
```
</step>

</process>

<output>
- `.planning/ROADMAP.md`
- `.planning/STATE.md`
- `.planning/phases/XX-name/` directories
</output>

<success_criteria>
- [ ] PROJECT.md validated
- [ ] ROADMAP.md created with phases
- [ ] STATE.md initialized
- [ ] Phase directories created
- [ ] Changes committed
</success_criteria>
