# GitHub Copilot Project Instructions

## File Generation Restrictions
- Do NOT create markdown files (.md) to plan, summarize, or document your work unless explicitly requested by the user.
- Do NOT generate temporary summary files, markdown logs, or change logs in the workspace.
- Keep all explanations and plans directly inside the chat interface.

## Language Standards
- ALL code comments, docstrings, and headers MUST be written in English.
- Use English for all variable names, functions, classes, and projects.

---

## C# & .NET Coding Standards

## Cross-Language Boundaries
- For mixed-language projects (for example, C# + TypeScript), apply each language's style rules only to source files written in that language.
- For auto-generated cross-language boundary files (for example, OpenAPI-generated TypeScript clients from a C# API), do not manually refactor for style conformance.
- Place generated files in a `generated/` folder whenever possible.
- Exclude generated files from linting and formatting checks.

### Structure & Anti-Patterns
- Do NOT use C# top-level statements for program entry points. Always use the classic structure with an explicit `Program` class and a `static void Main(string[] args)` method.

### Code Style & `this.`
- Explicitly use the `this.` qualifier for all access to instance fields, properties, and methods within a class.
- Use file-scoped namespaces (`namespace MyNamespace;`) where appropriate.
- Use pattern matching and switch expressions wherever possible.
- Always use explicit access modifiers such as `private`, `public`, and `internal`.
- **For C# only:** Use 4 spaces for indentation. Do not use tabs.
- **For C# only:** Opening braces `{` must be on a new line (Allman style).

### Naming Conventions & Fields
- Do NOT use an underscore prefix (`_`) for private fields.
- Private fields must use standard camelCase, for example: `private ILogger logger;`
- Follow PascalCase for classes, records, structs, interfaces, methods, and public members.
- Prefix interfaces with `I`, for example: `IUserService`.

### Type Safety & Explicit Typing
- ALWAYS use explicit types for variables, parameters, and return values. NEVER use `var`.
- Use explicit return types for all methods: `private string GetName() { ... }`, not `private var GetName() { ... }`
- Use explicit parameter types: `private void ProcessData(string data, int count) { ... }`
- Declare field types explicitly: `private ILogger logger;`, not `private var logger;`
- Use explicit generic types: `private List<User> users = new List<User>();`, not `private var users = new { ... };`
- Use explicit collection initializers: `private Dictionary<string, int> mapping = new Dictionary<string, int> { ... };`

### Constructor Patterns
- Use either **standard constructors** or **primary constructors** (both are acceptable).

**Standard Constructor Pattern (traditional approach):**
- Assign constructor parameters to private fields using `this.` qualifier.
- Provide XML documentation for constructors and their parameters.

```csharp
/// <summary>
/// Initializes a new instance of the <see cref="CommandDispatcher"/> class.
/// </summary>
/// <param name="commands">The collection of available commands.</param>
/// <param name="logger">The logger instance.</param>
public CommandDispatcher(IEnumerable<ICommand> commands, ILogger<CommandDispatcher> logger)
{
    this.commands = commands;
    this.logger = logger;
}
```

**Primary Constructor Pattern (C# 12+, modern approach):**
- Use primary constructor parameters directly without declaring private backing fields.
- Access parameters via `this.` qualifier within the class body.
- In C# 12+, primary constructor parameters are accessible throughout the class lifetime.
- Provide XML documentation for the class and its primary constructor parameters.

```csharp
/// <summary>
/// Service that dispatches commands to registered handlers.
/// </summary>
/// <param name="commands">The collection of available commands.</param>
/// <param name="logger">The logger instance.</param>
public class CommandDispatcher(IEnumerable<ICommand> commands, ILogger<CommandDispatcher> logger)
    : ICommandDispatcher
{
    /// <inheritdoc/>
    public async Task<string> DispatchAsync(string rawCommand)
    {
        ICommand? command = this.commands.FirstOrDefault(c => c.CommandName == commandName);

        if (command == null)
        {
            this.logger.LogWarning("Unknown command: {CommandName}", commandName);
            return $"ERROR: Unknown command '{commandName}'";
        }

        try
        {
            this.logger.LogInformation("Executing command: {CommandName}", commandName);
            return await command.ExecuteAsync(args);
        }
        catch (Exception ex)
        {
            this.logger.LogError(ex, "Error executing command: {CommandName}", commandName);
            return "ERROR: Command execution failed";
        }
    }
}
```

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

---

## TypeScript & Vue.js Coding Standards

### Compiler & Strict Mode
- Enable `strict: true` in `tsconfig.json`. This includes:
  - `noImplicitAny: true`
  - `strictNullChecks: true`
  - `strictFunctionTypes: true`
  - `strictBindCallApply: true`
  - `strictPropertyInitialization: true`
  - `noImplicitThis: true`
  - `alwaysStrict: true`

### Naming Conventions
- Use **camelCase** for variables, functions, and method names: `const userId = 123;`
- Use **PascalCase** for classes, interfaces, types, and components: `interface UserService`, `class AuthHandler`, `class MyComponent extends Vue`
- Use **SCREAMING_SNAKE_CASE** for constants: `const MAX_RETRIES = 3;`
- Prefix boolean variables with `is`, `has`, or `can`: `isLoading = false;`, `hasError = true;`

### Async/Await & Promises
- Always use `async/await` instead of `.then()` chains.
- Mark async methods explicitly: `async fetchUser() { ... }`
- Handle errors with `try/catch` blocks, not `.catch()`.
- Never use bare `Promise` without error handling.

### Documentation Standards
- Use **JSDoc** for all exported classes, methods, and complex types.
- Format: Use `/** ... */` blocks with `@param`, `@returns`, `@throws` tags.

```typescript
/**
 * User service component that handles authentication and user data.
 */
@Component
export default class UserService extends Vue {
  /**
   * Fetches a user by ID from the API.
   * @param userId - The unique identifier of the user.
   * @returns A promise that resolves to the user object.
   * @throws {Error} If the API request fails.
   */
  async fetchUser(userId: number): Promise<User> {
    // ...
  }
}
```

### Imports & Module Style
- Use **ES6 import syntax**: `import { Vue, Component } from 'vue-facing-decorator';`
- Organize imports in this order:
  1. External dependencies (Vue, vue-facing-decorator, libraries)
  2. Internal modules and utilities
  3. Local components and services
- Use destructuring where applicable: `import { Vue, Component, Prop } from 'vue-facing-decorator';`
- Avoid default exports for utility functions; use named exports.

### Code Style & Formatting
- **For TypeScript only:** Use 2 spaces for indentation (Vue.js convention).
- Use single quotes for strings: `const name = 'John';`
- Use trailing commas in multiline objects/arrays.
- Use `const` by default, `let` when reassignment is needed. Never use `var`.
- **For TypeScript only:** Opening braces `{` stay on the same line (K&R style).

### Linting & Formatting
- Format with **Prettier** (2-space indentation).
- Lint with **ESLint** using Vue.js rules.
- Run `npm run lint` and `npm run format` before committing.
- Fix linting errors automatically: `npm run lint:fix`

### Vue.js with vue-facing-decorator

#### Component Structure
- Always extend `Vue` class: `export default class MyComponent extends Vue { ... }`
- Use `@Component` decorator for all components.
- Use `@Component({ components: { ChildComponent } })` for child component registration.

#### Properties & State Management
- Use `@Prop()` decorator for props with type annotations:

```typescript
@Prop({ type: String, required: true })
private readonly userId!: string;

@Prop({ type: Number, default: 10 })
private readonly limit: number = 10;
```

- Use class properties for reactive data:

```typescript
private message: string = 'Hello';
private count: number = 0;
private items: Item[] = [];
```

- Always provide type annotations for properties.

#### Methods & Lifecycle Hooks
- Use lifecycle hooks as class methods:

```typescript
mounted(): void {
  this.loadData();
}

destroyed(): void {
  this.cleanup();
}
```

- Mark methods as `private`, `protected`, or `public` explicitly.
- Use `void` return type for methods that don't return a value.

#### Computed Properties
- Use `@Watch` decorator for watchers:

```typescript
@Watch('userId')
private onUserIdChange(newValue: string): void {
  this.loadUserData(newValue);
}
```

- Use getter methods for computed values:

```typescript
get isValid(): boolean {
  return this.name.length > 0 && this.email.includes('@');
}
```

#### Event Handling
- Use `@Emit` decorator for custom events:

```typescript
@Emit('submit')
private onSubmit(): FormData {
  return this.formData;
}
```

- Or use `this.$emit()` explicitly:

```typescript
private handleClick(): void {
  this.$emit('click', { id: this.userId });
}
```

### Type Safety
- Never use `any` type. Use `unknown` and type-guard if necessary.
- Define explicit return types for all methods: `private getName(): string { ... }`
- Use union types instead of optional values where possible: `type Status = 'pending' | 'success' | 'error';`
- Use interfaces for object shapes: `interface User { id: number; name: string; }`
- Use generic types for collections: `private items: Item[] = [];` or `Map<string, User>`

### Example Component Structure

```typescript
import { Vue, Component, Prop, Watch, Emit } from 'vue-facing-decorator';
import UserService from '@/services/UserService';

/**
 * User profile component displaying user information.
 */
@Component
export default class UserProfile extends Vue {
  @Prop({ type: String, required: true })
  private readonly userId!: string;

  private user: User | null = null;
  private isLoading: boolean = false;
  private hasError: boolean = false;

  /**
   * Lifecycle hook - component is mounted.
   */
  mounted(): void {
    this.loadUser();
  }

  /**
   * Loads user data from the API.
   */
  private async loadUser(): Promise<void> {
    this.isLoading = true;
    this.hasError = false;
    try {
      this.user = await UserService.fetchUser(this.userId);
    } catch (error) {
      this.hasError = true;
      console.error('Failed to load user:', error);
    } finally {
      this.isLoading = false;
    }
  }

  /**
   * Watches for userId changes and reloads data.
   */
  @Watch('userId')
  private onUserIdChange(newValue: string): void {
    this.loadUser();
  }

  /**
   * Emits user update event.
   */
  @Emit('user-updated')
  private onUserUpdate(): User | null {
    return this.user;
  }

  /**
   * Computed property for display name.
   */
  get displayName(): string {
    return this.user?.name ?? 'Unknown User';
  }
}
```
