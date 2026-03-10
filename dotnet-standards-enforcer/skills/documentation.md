---
name: dotnet-documentation
description: 'XML documentation best practices for .NET/C# code.'
---

## Scope

Analyze `${selection}` if provided. If no selection is made, analyze ALL C# files (`**/*.cs`) in the workspace.

## Execution Rules

- Process **every file in scope**. Do not skip any.
- **DO NOT STOP** until all files have been reviewed.
- If context limits are reached, emit a **checkpoint summary** then await continuation.

## Documentation & Structure

- Create comprehensive XML documentation comments for all public classes, interfaces, methods, and properties.
- Include `<summary>`, `<param name="...">`, `<returns>`, and `<exception cref="...">` tags where applicable.
- Follow the established namespace structure: `{Core|Console|App|Service}.{Feature}`.

### Example

```csharp
/// <summary>
/// Retrieves a risk by its unique identifier.
/// </summary>
/// <param name="id">The unique identifier of the risk.</param>
/// <param name="cancellationToken">Token to cancel the operation.</param>
/// <returns>The matching <see cref="Risk"/>, or <c>null</c> if not found.</returns>
/// <exception cref="ArgumentException">Thrown when <paramref name="id"/> is empty.</exception>
public async Task<Risk?> GetByIdAsync(Guid id, CancellationToken cancellationToken)
```

## Findings Summary Format

| File | Section Violated | Description | Recommended Fix |
|------|-----------------|-------------|-----------------|
| ...  | Documentation & Structure | Missing XML doc on public method `GetByIdAsync` | Add `<summary>`, `<param>`, `<returns>` tags |
