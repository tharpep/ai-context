# Design Principles

## Overview

This folder contains fundamental design principles that guide all architectural decisions and code organization.

## SOLID Principles

### Single Responsibility Principle (SRP)
- **Definition**: A class should have only one reason to change
- **Benefits**: Easier maintenance, testing, and understanding
- **Example**: Separate classes for user authentication and user profile management

### Open/Closed Principle (OCP)
- **Definition**: Software entities should be open for extension but closed for modification
- **Benefits**: Extensibility without breaking existing code
- **Example**: Plugin architecture, strategy pattern implementations

### Liskov Substitution Principle (LSP)
- **Definition**: Objects of a superclass should be replaceable with objects of its subclasses
- **Benefits**: Polymorphism works correctly, contracts are maintained
- **Example**: All implementations of IRepository should be interchangeable

### Interface Segregation Principle (ISP)
- **Definition**: Clients should not be forced to depend on interfaces they don't use
- **Benefits**: Loose coupling, focused interfaces
- **Example**: Separate interfaces for read and write operations

### Dependency Inversion Principle (DIP)
- **Definition**: High-level modules should not depend on low-level modules; both should depend on abstractions
- **Benefits**: Loose coupling, testability, flexibility
- **Example**: Services depend on repository interfaces, not concrete implementations

## Additional Principles

### DRY (Don't Repeat Yourself)
- **Definition**: Avoid code duplication
- **Benefits**: Easier maintenance, consistency
- **Implementation**: Extract common functionality into shared utilities

### KISS (Keep It Simple, Stupid)
- **Definition**: Prefer simple solutions over complex ones
- **Benefits**: Easier understanding, maintenance, and debugging
- **Implementation**: Choose the simplest solution that meets requirements

### YAGNI (You Aren't Gonna Need It)
- **Definition**: Don't implement functionality until it's actually needed
- **Benefits**: Avoid over-engineering, focus on current requirements
- **Implementation**: Implement features as they are required

### Composition over Inheritance
- **Definition**: Prefer object composition over class inheritance
- **Benefits**: More flexible, easier to test, less coupling
- **Implementation**: Use dependency injection and interfaces

## Code Organization Principles

### Separation of Concerns
- **Definition**: Each module should address a separate concern
- **Benefits**: Easier maintenance, testing, and understanding
- **Implementation**: Separate layers for data access, business logic, and presentation

### Dependency Injection
- **Definition**: Dependencies are provided to a class rather than created by it
- **Benefits**: Testability, flexibility, loose coupling
- **Implementation**: Use constructor injection for required dependencies

### Interface Segregation
- **Definition**: Create focused, cohesive interfaces
- **Benefits**: Loose coupling, easier testing
- **Implementation**: Separate interfaces for different responsibilities

## Quality Principles

### Testability
- **Definition**: Code should be easy to test
- **Benefits**: Confidence in changes, documentation of behavior
- **Implementation**: Use dependency injection, avoid static dependencies

### Maintainability
- **Definition**: Code should be easy to modify and extend
- **Benefits**: Lower cost of changes, faster development
- **Implementation**: Clear naming, documentation, modular design

### Readability
- **Definition**: Code should be self-documenting and easy to understand
- **Benefits**: Easier onboarding, maintenance, debugging
- **Implementation**: Clear naming, consistent formatting, comments

## Implementation Guidelines

### Class Design
```csharp
// Good: Single responsibility, clear interface
public class UserService
{
    private readonly IUserRepository _userRepository;
    private readonly ILogger<UserService> _logger;
    
    public UserService(IUserRepository userRepository, ILogger<UserService> logger)
    {
        _userRepository = userRepository ?? throw new ArgumentNullException(nameof(userRepository));
        _logger = logger ?? throw new ArgumentNullException(nameof(logger));
    }
    
    public async Task<User> GetUserAsync(int id, CancellationToken cancellationToken = default)
    {
        // Implementation
    }
}
```

### Interface Design
```csharp
// Good: Focused, cohesive interface
public interface IUserRepository
{
    Task<User> GetByIdAsync(int id, CancellationToken cancellationToken = default);
    Task<User> CreateAsync(User user, CancellationToken cancellationToken = default);
    Task UpdateAsync(User user, CancellationToken cancellationToken = default);
    Task DeleteAsync(int id, CancellationToken cancellationToken = default);
}
```

### Dependency Injection
```csharp
// Good: Dependencies injected, not created
public class UserController
{
    private readonly IUserService _userService;
    
    public UserController(IUserService userService)
    {
        _userService = userService ?? throw new ArgumentNullException(nameof(userService));
    }
}
```

## Anti-Patterns to Avoid

- **God Object**: Classes with too many responsibilities
- **Anemic Domain Model**: Classes with only data, no behavior
- **Tight Coupling**: Direct dependencies between concrete classes
- **Circular Dependencies**: Classes depending on each other
- **Premature Optimization**: Optimizing before understanding requirements
- **Copy-Paste Programming**: Duplicating code instead of extracting common functionality
- **Magic Numbers**: Using literal values instead of named constants
- **Long Parameter Lists**: Methods with too many parameters
- **Feature Envy**: Methods that use more features of other classes than their own

## References

- [SOLID Principles](https://en.wikipedia.org/wiki/SOLID)
- [Clean Code](https://www.amazon.com/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882)
- [Clean Architecture](https://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html)
- [Domain-Driven Design](https://en.wikipedia.org/wiki/Domain-driven_design)
