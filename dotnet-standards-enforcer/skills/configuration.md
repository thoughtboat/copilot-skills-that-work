---
name: dotnet-configuration
description: 'Configuration and settings best practices for .NET/C#.'
---

## Scope

Analyze `${selection}` if provided. If no selection is made, analyze ALL C# files (`**/*.cs`) in the workspace.

## Execution Rules

- Process **every file in scope**. Do not skip any.
- **DO NOT STOP** until all files have been reviewed.
- If context limits are reached, emit a **checkpoint summary** then await continuation.

## Configuration & Settings

- Use **strongly-typed configuration classes** with data annotations.
- Apply validation attributes: `[Required]`, `[Range]`, `[MinLength]`, etc.
- Bind configuration via `IConfiguration.GetSection("Key").Get<TOptions>()` or `services.Configure<TOptions>()`.
- Support `appsettings.json` and `appsettings.{Environment}.json` overrides.

### Example

```csharp
// Strongly-typed options class
public class DatabaseOptions
{
    [Required]
    public string ConnectionString { get; init; } = string.Empty;

    [Range(1, 300)]
    public int CommandTimeoutSeconds { get; init; } = 30;
}

// Registration in Program.cs
builder.Services
    .AddOptions<DatabaseOptions>()
    .BindConfiguration("Database")
    .ValidateDataAnnotations()
    .ValidateOnStart();
```

## Findings Summary Format

| File | Section Violated | Description | Recommended Fix |
|------|-----------------|-------------|-----------------|
| ...  | Configuration & Settings | Raw `IConfiguration["Key"]` string access | Replace with strongly-typed options class bound via `Configure<T>()` |
