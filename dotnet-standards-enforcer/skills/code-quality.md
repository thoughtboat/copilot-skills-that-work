---
name: dotnet-code-quality
description: 'Code quality and SOLID principles best practices for .NET/C#.'
---

## Scope

Analyze `${selection}` if provided. If no selection is made, analyze ALL C# files (`**/*.cs`) in the workspace.

## Execution Rules

- Process **every file in scope**. Do not skip any.
- **DO NOT STOP** until all files have been reviewed.
- If context limits are reached, emit a **checkpoint summary** then await continuation.

## Code Quality

- Ensure **SOLID principles** compliance (Single Responsibility, Open/Closed, Liskov, Interface Segregation, Dependency Inversion).
- Avoid code duplication — extract shared logic to base classes or utility methods.
- **Remove unused `using` directives** and sort the remaining ones alphabetically.
- Follow consistent naming conventions:
  - `PascalCase` for types, methods, properties, and constants.
  - `camelCase` for parameters and local variables.
  - `_camelCase` for private fields.
- Use meaningful names that reflect **domain concepts** (e.g., `riskImpactScore` not `val`).
- Keep methods **focused and cohesive** — single responsibility per method.
- Implement `IDisposable` / `IAsyncDisposable` patterns for types holding unmanaged resources.
- Move all constants to a **separate `static` class** (e.g., `RiskDomainConstants`).
- Format code according to C# conventions: consistent spacing, braces on new lines, no trailing whitespace.

### Constants Example

```csharp
// Before — inline magic strings/numbers
if (risk.Score > 8) { ... }

// After — extracted to constants class
public static class RiskDomainConstants
{
    public const int CriticalScoreThreshold = 8;
}

if (risk.Score > RiskDomainConstants.CriticalScoreThreshold) { ... }
```

## Findings Summary Format

| File | Section Violated | Description | Recommended Fix |
|------|-----------------|-------------|-----------------|
| ...  | Code Quality | Magic number used inline | Extract to `RiskDomainConstants` static class |
