# GitHub Copilot Project Instructions

## File Generation Restrictions
- Do NOT create markdown files (.md) to plan, summarize, or document your work unless explicitly requested by the user.
- Do NOT generate temporary summary files, markdown logs, or change logs in the workspace.
- Keep all explanations and plans directly inside the chat interface.

## Language Standards
- ALL code comments, docstrings, and headers MUST be written in English.
- Use English for all variable names, functions, classes, and projects.

## C# & .NET Coding Standards

### Structure & Anti-Patterns
- Do NOT use C# top-level statements for program entry points. Always use the classic structure with an explicit `Program` class and a `static void Main(string[] args)` method.

### Code Style & `this.`
- Explicitly use the `this.` qualifier for all access to instance fields, properties, and methods within a class.
- Use file-scoped namespaces (`namespace MyNamespace;`) where appropriate.
- Use pattern matching and switch expressions wherever possible.
- Always use explicit access modifiers such as `private`, `public`, and `internal`.
- Use 4 spaces for indentation. Do not use tabs.
- Opening braces `{` must be on a new line.

### Naming Conventions & Fields
- Do NOT use an underscore prefix (`_`) for private fields.
- Private fields must use standard camelCase, for example: `private ILogger logger;`
- Follow PascalCase for classes, records, structs, interfaces, methods, and public members.
- Prefix interfaces with `I`, for example: `IUserService`.

### XML Documentation Standards
- Every interface, public class, method, and property MUST include precise C# XML documentation using `/// <summary>`.
- Use this format:

```csharp
/// <summary>
/// Service for handling network configuration commands.
/// </summary>
public interface INetworkConfigService
{
    /// <summary>
    /// Changes the IP address of a network interface.
    /// </summary>
    /// <param name="interfaceName">Name of the network interface.</param>
    /// <param name="ipAddress">IP address to set.</param>
    /// <returns>True if successful; otherwise, false.</returns>
    Task<bool> ChangeIpAddressAsync(string interfaceName, string ipAddress);
}
```

### Additional Guidelines
- Constructor injection is required; never use service locator patterns.
- Use async/await for all I/O operations. Avoid blocking calls like `.Result` or `.Wait()`.
- Use nullable reference types (`#nullable enable`) where applicable.
