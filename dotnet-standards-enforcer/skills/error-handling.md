---
name: dotnet-error-handling
description: 'Error handling and structured logging best practices for .NET/C#.'
---

## Scope

Analyze `${selection}` if provided. If no selection is made, analyze ALL C# files (`**/*.cs`) in the workspace.

## Execution Rules

- Process **every file in scope**. Do not skip any.
- **DO NOT STOP** until all files have been reviewed.
- If context limits are reached, emit a **checkpoint summary** then await continuation.

## Error Handling & Logging

- Use **structured logging** with `Microsoft.Extensions.Logging` (`ILogger<T>`).
- Log method entries, exits, and key decision points with appropriate log levels (`LogDebug`, `LogInformation`, `LogWarning`, `LogError`).
- Use **message templates** (not string interpolation) to avoid unnecessary allocations: `_logger.LogInformation("Processing risk {RiskId}", id)`.
- Throw **specific, descriptive exceptions** rather than `Exception` or `ApplicationException`.
- Use `try`/`catch` only for expected failure scenarios; let unexpected exceptions propagate to global middleware.
- Use `ILogger` scopes (`using (_logger.BeginScope(...)`) for correlated log context.

### Example

```csharp
// Before
Console.WriteLine("Error: " + ex.Message);
throw new Exception("Something went wrong");

// After
_logger.LogError(ex, "Failed to retrieve risk {RiskId}", id);
throw new RiskNotFoundException(id);
```

## Findings Summary Format

| File | Section Violated | Description | Recommended Fix |
|------|-----------------|-------------|-----------------|
| ...  | Error Handling & Logging | String interpolation used in log call | Replace with structured message template |
