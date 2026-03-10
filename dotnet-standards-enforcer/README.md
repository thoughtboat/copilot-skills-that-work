# dotnet-standards-enforcer Skill

A GitHub Copilot **skill** that acts as a master orchestrator, enforcing code standards across every C# file in the workspace by running nine ordered sub-skills and automatically applying all fixes.

---

## Supported versions

| Platform | Version |
|----------|---------|
| .NET     | **8.0 +** |
| C#       | **14** |

---

## What it does

When invoked, the skill:

1. Scopes the analysis to either the current **selection** or **all `*.cs` files** in the workspace.
2. Runs nine ordered sub-skills against every file in scope.
3. Outputs a **consolidated findings summary table** (file · sub-skill · violation · recommended fix).
4. **Writes every fix directly to disk** — it does not stop at suggestions.
5. If the context window is exhausted mid-run it emits a **checkpoint summary** so the review can be resumed in the next prompt.

---

## Sub-skills

| # | Sub-skill | Focus area |
|---|-----------|------------|
| 1 | **Documentation** | XML doc comments (`<summary>`, `<param>`, `<returns>`, `<exception>`), namespace structure |
| 2 | **Architecture** | Primary constructors, Command Handler pattern, `Impl/` folder placement, interface segregation |
| 3 | **DI & Services** | Constructor injection, `ArgumentNullException.ThrowIfNull` guards, service lifetimes, `IServiceCollection` extension methods |
| 4 | **Localization** | Hard-coded strings → `LogMessages.resx` / `ErrorMessages.resx`, `ResourceManager` access |
| 5 | **Async Patterns** | `async`/`await`, `ConfigureAwait(false)`, `CancellationToken` propagation, no `.Result`/`.Wait()` |
| 6 | **Configuration** | Strongly-typed options classes, data annotation validation, `appsettings.{Environment}.json` |
| 7 | **Error Handling** | Structured logging (`ILogger<T>`), message templates, specific exception types, log scopes |
| 8 | **Code Quality** | SOLID principles, naming conventions, unused `using` removal, constants extraction, `IDisposable` |

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
Read .github/skills/dotnet-standards-enforcer/skills/async-patterns.md and apply it to all C# files.
```

---

## Output format

After analysis the skill produces a single Markdown table, then patches every file:

| File | Sub-Skill | Section Violated | Description | Recommended Fix |
|------|-----------|-----------------|-------------|-----------------|
| `RiskService.cs` | Async Patterns | Async/Await Patterns | `.Result` blocking call | Convert to `await` with `ConfigureAwait(false)` |
| `Program.cs` | Configuration | Configuration & Settings | Raw `IConfiguration["Key"]` access | Replace with strongly-typed options |

Each row is followed by a `✅` confirmation once the fix has been written to disk.

---

## Resuming after a context limit

If the model hits its context window during a large run, it will print a checkpoint like:

```
## Checkpoint
Sub-skills completed : 1–5
Files fully reviewed : RiskService.cs, CategoryService.cs
Remaining files      : RiskRepository.cs, MongoDbContext.cs
Remaining sub-skills : 6 (Configuration), 7 (Error Handling), 8 (Perf & Security), 9 (Code Quality)
```

Simply reply **"continue"** and the skill will pick up from where it left off.

---

## Folder structure

```
.github/skills/dotnet-standards-enforcer/
├── README.md               ← this file
├── SKILL.md                ← orchestrator loaded by Copilot
└── skills/
    ├── documentation.md
    ├── architecture.md
    ├── di-services.md
    ├── localization.md
    ├── async-patterns.md
    ├── configuration.md
    ├── error-handling.md
    └── code-quality.md
```
