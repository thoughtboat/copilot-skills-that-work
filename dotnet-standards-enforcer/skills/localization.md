---
name: dotnet-localization
description: 'Resource management and localization best practices for .NET/C#.'
---

## Scope

Analyze `${selection}` if provided. If no selection is made, analyze ALL C# files (`**/*.cs`) in the workspace.

## Execution Rules

- Process **every file in scope**. Do not skip any.
- **DO NOT STOP** until all files have been reviewed.
- If context limits are reached, emit a **checkpoint summary** then await continuation.

## Resource Management & Localization

- Move all hard-coded strings to `.resx` resource files. Create the files if they do not exist.
- Maintain two separate resource files per feature:
  - `LogMessages.resx` — log message strings
  - `ErrorMessages.resx` — error/exception message strings
- Use `ResourceManager` to access strings at runtime.
- Access resources via `_resourceManager.GetString("MessageKey")`.

### Resource File Structure

```
Shared/
  Resources/
    LogMessages.resx
    ErrorMessages.resx
```

### Example

```csharp
// Before
throw new InvalidOperationException("Risk not found.");
_logger.LogInformation("Fetching risk with id {Id}.", id);

// After
throw new InvalidOperationException(_errorMessages.GetString("RiskNotFound"));
_logger.LogInformation(_logMessages.GetString("FetchingRisk"), id);
```

## Findings Summary Format

| File | Section Violated | Description | Recommended Fix |
|------|-----------------|-------------|-----------------|
| ...  | Resource Management & Localization | Hard-coded error string in exception | Move to `ErrorMessages.resx` and access via `ResourceManager` |
