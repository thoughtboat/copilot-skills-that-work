---
name: dotnet-architecture
description: 'Design patterns and architecture best practices for .NET/C# code.'
---

## Scope

Analyze `${selection}` if provided. If no selection is made, analyze ALL C# files (`**/*.cs`) in the workspace.

## Execution Rules

- Process **every file in scope**. Do not skip any.
- **DO NOT STOP** until all files have been reviewed.
- If context limits are reached, emit a **checkpoint summary** then await continuation.

## Design Patterns & Architecture

- Use **primary constructor syntax** for dependency injection.
- Implement the **Command Handler pattern** with generic base classes (e.g., `CommandHandler<TOptions>`).
- Use **interface segregation** with clear naming conventions (prefix interfaces with `I`).
- Place implementations in a separate `Impl/` subfolder. Create the folder if it does not exist.
- Follow the **Factory pattern** for complex object creation.

### Primary Constructor Example

```csharp
// Before
public class RiskService
{
    private readonly IRiskRepository _repository;
    public RiskService(IRiskRepository repository) => _repository = repository;
}

// After
public class RiskService(IRiskRepository repository)
{
    public async Task<Risk?> GetByIdAsync(Guid id, CancellationToken ct)
        => await repository.GetByIdAsync(id, ct).ConfigureAwait(false);
}
```

## Findings Summary Format

| File | Section Violated | Description | Recommended Fix |
|------|-----------------|-------------|-----------------|
| ...  | Design Patterns & Architecture | Implementation not in `Impl/` folder | Move class to `Impl/` subfolder |
