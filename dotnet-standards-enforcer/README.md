# dotnet-standards-enforcer Skill

A GitHub Copilot **skill** that acts as a master orchestrator, enforcing code standards across every C# file in the workspace by running eight ordered sub-skills and automatically applying all fixes.

---

## Supported versions

| Platform | Version |
|----------|---------|
| .NET     | **6.0 +** |
| C#       | **10 +** |

> **Version-gated guidance** — The skill reads every `*.csproj` and identifies the lowest `<TargetFramework>` across the workspace. Sub-skills that rely on .NET 8 APIs (e.g. `IExceptionHandler`, Keyed DI services, `IProblemDetailsService`) are only applied when **all** targeted projects support `net8.0` or later. Projects targeting only `net6.0` or `net7.0` receive equivalent guidance using APIs available on those frameworks.

---

## What it does

When invoked, the skill:

1. **Inspects every `*.csproj`** in the workspace to detect `<LangVersion>` and `<TargetFramework>` before touching any source. Sub-skills that rely on C# 10-14 (and newer) features are only applied when the detected language version supports them. .NET 8-only runtime APIs are similarly skipped when the lowest target framework is `net6.0` or `net7.0`, with compatible alternatives suggested instead.
2. Scopes the analysis to either the current **selection** or **all `*.cs` files** in the workspace, skipping excluded paths (see [Excluded files](#excluded-files) below).
3. Runs eight ordered sub-skills against every file in scope.
4. Outputs a **consolidated findings summary table** (file · sub-skill · severity · violation · recommended fix).
5. **Writes every fix directly to disk** — it does not stop at suggestions.
6. If the context window is exhausted mid-run it emits a **checkpoint summary** so the review can be resumed in the next prompt.

---

## Excluded files

The following paths are always skipped to avoid breaking generated or migrated code:

| Pattern | Reason |
|---------|--------|
| `**/*.g.cs`, `**/*.Designer.cs` | Auto-generated source |
| `**/obj/**`, `**/bin/**` | Build output |
| `**/Migrations/**` | EF Core migration files |

**Test projects** (any project whose name contains `Tests`, `Specs`, or `Test`) have reduced coverage:

- Sub-skill **4 (Localization)** is skipped as it produces noise in test code.
- All other sub-skills run normally.

---

## Sub-skills

| # | Sub-skill | Focus area |
|---|-----------|------------|
| 1 | **Documentation** | XML doc comments (`<summary>`, `<param>`, `<returns>`, `<exception>`), `<inheritdoc/>`, namespace structure |
| 2 | **Architecture** | Primary constructors, records for DTOs/value objects, handler pattern, interface segregation |
| 3 | **DI & Services** | Constructor injection, `ArgumentNullException.ThrowIfNull` guards, service lifetimes, captive dependency detection, Keyed Services |
| 4 | **Localization** | User-facing strings only → `ErrorMessages.resx`, `IStringLocalizer<T>` (log templates must stay as static literals) |
| 5 | **Async Patterns** | `async`/`await`, `ConfigureAwait(false)` context, `CancellationToken` propagation, `ValueTask`, `IAsyncEnumerable<T>` |
| 6 | **Configuration** | Strongly-typed options classes, `IOptions` / `IOptionsSnapshot` / `IOptionsMonitor` selection, `sealed` + `required` |
| 7 | **Error Handling** | Structured logging, `[LoggerMessage]` source generator, `IExceptionHandler`, `ProblemDetails`, specific exception types |
| 8 | **Code Quality** | SOLID, nullable annotations, file-scoped namespaces, pattern matching, naming, constants, `IDisposable` |

---

## How to use in VS Code

### Prerequisites

- [GitHub Copilot](https://marketplace.visualstudio.com/items?itemName=GitHub.copilot) extension installed and signed in.
- [GitHub Copilot Chat](https://marketplace.visualstudio.com/items?itemName=GitHub.copilot-chat) extension installed.

### Option A — Analyse the whole solution (agent mode)

1. Open the **Copilot Chat** panel (`Ctrl+Alt+I`).
2. Switch to **Agent mode** using the mode picker at the bottom of the chat input.
3. Type:

   ```
   /dotnet-standards-enforcer
   ```

   Copilot will load the skill from `.github/skills/dotnet-standards-enforcer/SKILL.md`, scan every `*.cs` file, and apply all fixes.

### Option B — Analyse a selection only

1. Highlight one or more C# files / code blocks in the editor.
2. Open Copilot Chat and, in **Agent mode**, type:

   ```
   /dotnet-standards-enforcer review the selected code
   ```

   The skill will scope its analysis to the highlighted selection.

### Option C — Run a single sub-skill

Reference the sub-skill file directly when you only need one category checked:

```
Read .github/skills/dotnet-standards-enforcer/references/async-patterns.md and apply it to all C# files.
```

---

## Output format

After analysis the skill produces a single Markdown table, then patches every file:

| File | Sub-Skill | Severity | Description | Recommended Fix |
|------|-----------|----------|-------------|-----------------|
| `RiskService.cs` | Async Patterns | Critical | `.Result` blocking call | Convert to `await` with `ConfigureAwait(false)` |
| `Program.cs` | Configuration | Warning | Raw `IConfiguration["Key"]` access | Replace with strongly-typed options |

Each row is followed by a `✅` confirmation once the fix has been written to disk.

---

## Resuming after a context limit

If the model hits its context window during a large run, it will print a checkpoint like:

```
## Checkpoint
Sub-skills completed : 1–5
Files fully reviewed : RiskService.cs, CategoryService.cs
Remaining files      : RiskRepository.cs, MongoDbContext.cs
Remaining sub-skills : 6 (Configuration), 7 (Error Handling), 8 (Code Quality)
```

Simply reply **"continue"** and the skill will pick up from where it left off.

---

## Folder structure

```
.github/skills/dotnet-standards-enforcer/
├── README.md               ← this file
├── SKILL.md                ← orchestrator loaded by Copilot
└── references/
    ├── documentation.md
    ├── architecture.md
    ├── di-services.md
    ├── localization.md
    ├── async-patterns.md
    ├── configuration.md
    ├── error-handling.md
    └── code-quality.md
```
