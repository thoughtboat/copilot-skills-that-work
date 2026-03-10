---
name: dotnet-standards-enforcer
description: 'Master orchestrator — enforces all .NET/C# code standards and automatically applies fixes across the solution/project.'
---

## Scope

Analyze `${selection}` if provided. If no selection is made, analyze ALL C# files (`**/*.cs`) in the workspace.

## Execution Rules

- Process **every file in scope**. Do not skip any.
- Execute **each sub-skill below in order**, sequentially, for every file.
- After completing all sub-skills, output a **consolidated findings summary table** grouped by file and category.
- For each finding, **write the corrected code directly to the file** using the file editing tools. Use `// ...existing code...` to preserve unchanged sections.
- **APPLY ALL CHANGES after the summary table** — do not stop at summarising. Every finding must result in an implemented edit.
- **DO NOT STOP** until every sub-skill has been applied to every file in scope and every finding has been fixed.
- If context limits are reached, emit a **checkpoint summary** listing:
  - Sub-skills completed
  - Files fully reviewed
  - Remaining files and sub-skills
  Then await the next prompt to continue from where you left off.

## Sub-Skills (execute in order)

| # | Sub-Skill | File | Focus Area |
|---|-----------|------|------------|
| 1 | Documentation | [skills/documentation.md](skills/documentation.md) | XML docs, namespace structure |
| 2 | Architecture | [skills/architecture.md](skills/architecture.md) | Design patterns, Impl folder, primary constructors |
| 3 | DI & Services | [skills/di-services.md](skills/di-services.md) | DI registration, service interfaces, null guards |
| 4 | Localization | [skills/localization.md](skills/localization.md) | Hard-coded strings, .resx files, ResourceManager |
| 5 | Async Patterns | [skills/async-patterns.md](skills/async-patterns.md) | async/await, ConfigureAwait, CancellationToken |
| 6 | Configuration | [skills/configuration.md](skills/configuration.md) | Strongly-typed options, data annotations |
| 7 | Error Handling | [skills/error-handling.md](skills/error-handling.md) | Structured logging, specific exceptions |
| 8 | Code Quality | [skills/code-quality.md](skills/code-quality.md) | SOLID, naming, constants, usings, disposal |

## Consolidated Findings Summary Format

After all sub-skills are complete, produce a single Markdown table:

| File | Sub-Skill | Section Violated | Description | Recommended Fix |
|------|-----------|-----------------|-------------|-----------------|
| ...  | ...       | ...             | ...         | ...             |
## Apply Findings

After outputting the summary table, apply **every finding** in order:

1. Open the identified file.
2. Write the corrected code directly to the file.
3. Preserve all unchanged code using `// ...existing code...`.
4. Confirm each applied fix with a `✅` checklist item.
5. Do not skip any finding — the skill is not complete until all fixes are written to disk.