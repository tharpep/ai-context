# C# Development Standards

## Overview

C# development standards for .NET applications, following established guidelines and industry best practices.

## Naming Conventions

### Classes and Types
- PascalCase for public classes, interfaces, enums, and delegates
- Use descriptive names that clearly indicate purpose
- Avoid abbreviations unless they are widely understood

```csharp
// Good
public class UserAccountService
public interface IUserRepository
public enum UserStatus

// Avoid
public class UsrAcctSvc
public interface IUsrRepo
```

### Methods and Properties
- PascalCase for public methods and properties
- Use verb-noun pattern for methods
- Use noun pattern for properties

```csharp
// Good
public string GetUserById(int userId)
public bool IsActive { get; set; }

// Avoid
public string user(int id)
public bool active { get; set; }
```

### Fields and Variables
- camelCase for private fields (prefix with underscore)
- camelCase for local variables and method parameters
- Use descriptive names that indicate purpose

```csharp
// Good
private readonly ILogger<UserService> _logger;
private string _connectionString;
public void ProcessUser(string userName, int userId)

// Avoid
private ILogger<UserService> logger;
private string connStr;
public void ProcessUser(string u, int id)
```

## Code Structure

### File Organization
- One class per file
- File name matches class name
- Use namespaces to organize related classes

### Class Member Order
1. Fields (private first, then public)
2. Constructors
3. Properties
4. Methods (public first, then private)

```csharp
public class UserService
{
    // Fields
    private readonly ILogger<UserService> _logger;
    private readonly IUserRepository _userRepository;
    
    // Constructors
    public UserService(ILogger<UserService> logger, IUserRepository userRepository)
    {
        _logger = logger ?? throw new ArgumentNullException(nameof(logger));
        _userRepository = userRepository ?? throw new ArgumentNullException(nameof(userRepository));
    }
    
    // Properties
    public bool IsInitialized { get; private set; }
    
    // Methods
    public async Task<User> GetUserAsync(int userId)
    {
        // Implementation
    }
    
    private void ValidateUser(User user)
    {
        // Implementation
    }
}
```

## Async/Await Patterns

### Best Practices
- Use `Task` and `Task<T>` for async methods
- Use `ConfigureAwait(false)` in library code
- Avoid `.Result`, `.Wait()`, or `.GetAwaiter().GetResult()`
- Use `CancellationToken` for long-running operations

```csharp
// Good
public async Task<User> GetUserAsync(int userId, CancellationToken cancellationToken = default)
{
    try
    {
        var user = await _userRepository.GetByIdAsync(userId, cancellationToken).ConfigureAwait(false);
        return user;
    }
    catch (Exception ex)
    {
        _logger.LogError(ex, "Failed to get user {UserId}", userId);
        throw;
    }
}

// Avoid
public User GetUser(int userId)
{
    return _userRepository.GetByIdAsync(userId).Result; // Deadlock risk
}
```

## Error Handling

### Exception Handling
- Use specific exception types
- Log exceptions with structured logging
- Include correlation IDs in error messages
- Never swallow exceptions silently

```csharp
public async Task<User> CreateUserAsync(CreateUserRequest request)
{
    try
    {
        ValidateUserRequest(request);
        var user = await _userRepository.CreateAsync(request).ConfigureAwait(false);
        _logger.LogInformation("User created successfully with ID {UserId}", user.Id);
        return user;
    }
    catch (ValidationException ex)
    {
        _logger.LogWarning("Validation failed for user creation: {ValidationErrors}", ex.Message);
        throw;
    }
    catch (Exception ex)
    {
        _logger.LogError(ex, "Unexpected error creating user");
        throw new UserCreationException("Failed to create user", ex);
    }
}
```

## Logging Standards

### Structured Logging
- Use structured logging with correlation IDs
- Include relevant context in log messages
- Use appropriate log levels
- Never log sensitive information

```csharp
// Good
_logger.LogInformation("Processing user {UserId} with status {UserStatus}", 
    user.Id, user.Status);

_logger.LogWarning("User {UserId} has invalid email format: {Email}", 
    user.Id, MaskEmail(user.Email));

// Avoid
_logger.LogInformation($"Processing user {user.Id}");
_logger.LogWarning($"User has invalid email: {user.Email}"); // PII exposure
```

## Testing Patterns

### Unit Test Structure
- Use AAA pattern (Arrange, Act, Assert)
- One test per scenario
- Descriptive test names
- Mock dependencies appropriately

```csharp
[Test]
public async Task GetUserAsync_WhenUserExists_ReturnsUser()
{
    // Arrange
    var userId = 123;
    var expectedUser = new User { Id = userId, Name = "Test User" };
    _mockUserRepository.Setup(x => x.GetByIdAsync(userId, It.IsAny<CancellationToken>()))
        .ReturnsAsync(expectedUser);
    
    // Act
    var result = await _userService.GetUserAsync(userId);
    
    // Assert
    Assert.That(result, Is.EqualTo(expectedUser));
    _mockUserRepository.Verify(x => x.GetByIdAsync(userId, It.IsAny<CancellationToken>()), Times.Once);
}
```

## Performance Considerations

### Memory Management
- Use `using` statements for disposable objects
- Prefer `StringBuilder` for string concatenation
- Use `Span<T>` and `Memory<T>` for high-performance scenarios
- Avoid unnecessary allocations

### LINQ Optimization
- Use `Where` before `Select` to reduce data
- Consider `AsParallel()` for CPU-intensive operations
- Use `FirstOrDefault()` instead of `Where().FirstOrDefault()`

```csharp
// Good
var activeUsers = users
    .Where(u => u.IsActive)
    .Select(u => new UserDto { Id = u.Id, Name = u.Name })
    .ToList();

// Avoid
var activeUsers = users
    .Select(u => new UserDto { Id = u.Id, Name = u.Name })
    .Where(u => u.IsActive)
    .ToList();
```

## Security Guidelines

### Input Validation
- Validate all external input
- Use parameterized queries
- Sanitize user input
- Implement proper authorization checks

```csharp
public async Task<User> UpdateUserAsync(int userId, UpdateUserRequest request)
{
    // Validate input
    if (request == null)
        throw new ArgumentNullException(nameof(request));
    
    if (string.IsNullOrWhiteSpace(request.Name))
        throw new ValidationException("Name is required");
    
    // Check authorization
    if (!await _authorizationService.CanUpdateUserAsync(userId))
        throw new UnauthorizedAccessException("User not authorized to update this user");
    
    // Proceed with update
    return await _userRepository.UpdateAsync(userId, request);
}
```

## Common Patterns

### Repository Pattern
```csharp
public interface IUserRepository
{
    Task<User> GetByIdAsync(int id, CancellationToken cancellationToken = default);
    Task<User> CreateAsync(CreateUserRequest request, CancellationToken cancellationToken = default);
    Task<User> UpdateAsync(int id, UpdateUserRequest request, CancellationToken cancellationToken = default);
    Task DeleteAsync(int id, CancellationToken cancellationToken = default);
}
```

### Service Pattern
```csharp
public class UserService : IUserService
{
    private readonly IUserRepository _userRepository;
    private readonly ILogger<UserService> _logger;
    
    public UserService(IUserRepository userRepository, ILogger<UserService> logger)
    {
        _userRepository = userRepository ?? throw new ArgumentNullException(nameof(userRepository));
        _logger = logger ?? throw new ArgumentNullException(nameof(logger));
    }
    
    // Implementation
}
```

## Tools and Configuration

### Required NuGet Packages
- Microsoft.Extensions.Logging
- Microsoft.Extensions.DependencyInjection
- Microsoft.Extensions.Configuration
- FluentValidation
- AutoMapper
- Moq (for testing)

### EditorConfig
```ini
[*.cs]
indent_style = space
indent_size = 4
end_of_line = crlf
charset = utf-8
trim_trailing_whitespace = true
insert_final_newline = true

# C# formatting
csharp_new_line_before_open_brace = all
csharp_new_line_before_else = true
csharp_new_line_before_catch = true
csharp_new_line_before_finally = true
csharp_new_line_before_members_in_object_initializers = true
csharp_new_line_before_members_in_anonymous_types = true
```

## References

- [Microsoft C# Coding Conventions](https://docs.microsoft.com/en-us/dotnet/csharp/programming-guide/inside-a-program/coding-conventions)
- [.NET Performance Best Practices](https://docs.microsoft.com/en-us/dotnet/fundamentals/performance/)
- [C# Async Best Practices](https://docs.microsoft.com/en-us/archive/msdn-magazine/2013/march/async-await-best-practices-for-asynchronous-programming)