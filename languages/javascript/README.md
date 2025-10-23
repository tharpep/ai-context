# JavaScript Development Standards

## Overview

JavaScript development standards for frontend and Node.js applications, following modern ES6+ practices and industry best practices.

## Naming Conventions

### Variables and Functions
- camelCase for variables and functions
- Use descriptive names that clearly indicate purpose
- Avoid abbreviations unless they are widely understood

```javascript
// Good
const userAccountService = new UserAccountService();
const isUserActive = checkUserStatus(userId);
const getUserById = async (id) => { /* implementation */ };

// Avoid
const uas = new UserAccountService();
const isAct = checkUserStatus(userId);
const getUsr = async (id) => { /* implementation */ };
```

### Constants
- UPPER_SNAKE_CASE for constants
- Use descriptive names that indicate the value's purpose

```javascript
// Good
const MAX_RETRY_ATTEMPTS = 3;
const DEFAULT_TIMEOUT_MS = 5000;
const API_BASE_URL = 'https://api.example.com';

// Avoid
const MAX = 3;
const TIMEOUT = 5000;
const URL = 'https://api.example.com';
```

### Classes and Constructors
- PascalCase for classes and constructors
- Use descriptive names that indicate the class purpose

```javascript
// Good
class UserAccountService {
    constructor(userRepository, logger) {
        this.userRepository = userRepository;
        this.logger = logger;
    }
}

// Avoid
class UAS {
    constructor(ur, l) {
        this.ur = ur;
        this.l = l;
    }
}
```

## Code Structure

### File Organization
- One class/component per file
- Use modules for code organization
- Group related functionality together

### Function Structure
- Use arrow functions for short, simple functions
- Use function declarations for complex functions
- Keep functions small and focused

```javascript
// Good - Arrow function for simple operations
const formatUserName = (user) => `${user.firstName} ${user.lastName}`;

// Good - Function declaration for complex logic
async function processUserData(userData) {
    try {
        const validatedData = validateUserData(userData);
        const processedData = await transformUserData(validatedData);
        return processedData;
    } catch (error) {
        logger.error('Failed to process user data', error);
        throw error;
    }
}
```

## Modern JavaScript Features

### ES6+ Features
- Use `const` and `let` instead of `var`
- Use template literals for string interpolation
- Use destructuring for object and array access
- Use spread operator for array and object operations

```javascript
// Good
const { userId, userName } = user;
const [firstUser, ...remainingUsers] = users;
const newUser = { ...user, status: 'active' };

// Avoid
var userId = user.userId;
var userName = user.userName;
var firstUser = users[0];
var remainingUsers = users.slice(1);
```

### Async/Await Patterns
- Use async/await instead of Promises with .then()
- Handle errors with try/catch blocks
- Use Promise.all() for parallel operations

```javascript
// Good
async function fetchUserData(userId) {
    try {
        const [user, preferences, settings] = await Promise.all([
            userService.getUser(userId),
            userService.getPreferences(userId),
            userService.getSettings(userId)
        ]);
        
        return { user, preferences, settings };
    } catch (error) {
        logger.error('Failed to fetch user data', { userId, error });
        throw error;
    }
}

// Avoid
function fetchUserData(userId) {
    return userService.getUser(userId)
        .then(user => {
            return userService.getPreferences(userId)
                .then(preferences => {
                    return userService.getSettings(userId)
                        .then(settings => {
                            return { user, preferences, settings };
                        });
                });
        });
}
```

## Error Handling

### Exception Handling
- Use try/catch blocks for async operations
- Create custom error types for specific scenarios
- Log errors with structured information
- Never swallow errors silently

```javascript
// Good
class UserNotFoundError extends Error {
    constructor(userId) {
        super(`User with ID ${userId} not found`);
        this.name = 'UserNotFoundError';
        this.userId = userId;
    }
}

async function getUserById(userId) {
    try {
        const user = await userRepository.findById(userId);
        if (!user) {
            throw new UserNotFoundError(userId);
        }
        return user;
    } catch (error) {
        if (error instanceof UserNotFoundError) {
            throw error;
        }
        logger.error('Database error fetching user', { userId, error: error.message });
        throw new Error('Failed to fetch user');
    }
}
```

## Logging Standards

### Structured Logging
- Use structured logging with context
- Include relevant information in log messages
- Use appropriate log levels
- Never log sensitive information

```javascript
// Good
logger.info('User login successful', { 
    userId: user.id, 
    timestamp: new Date().toISOString() 
});

logger.warn('Invalid login attempt', { 
    email: maskEmail(email), 
    ipAddress: request.ip 
});

// Avoid
logger.info(`User ${user.id} logged in`);
logger.warn(`Invalid login for ${email}`); // PII exposure
```

## Testing Patterns

### Unit Test Structure
- Use descriptive test names
- Follow AAA pattern (Arrange, Act, Assert)
- Mock dependencies appropriately
- Test both success and error cases

```javascript
// Good
describe('UserService', () => {
    let userService;
    let mockUserRepository;
    
    beforeEach(() => {
        mockUserRepository = {
            findById: jest.fn(),
            create: jest.fn(),
            update: jest.fn()
        };
        userService = new UserService(mockUserRepository);
    });
    
    describe('getUserById', () => {
        it('should return user when user exists', async () => {
            // Arrange
            const userId = 123;
            const expectedUser = { id: userId, name: 'Test User' };
            mockUserRepository.findById.mockResolvedValue(expectedUser);
            
            // Act
            const result = await userService.getUserById(userId);
            
            // Assert
            expect(result).toEqual(expectedUser);
            expect(mockUserRepository.findById).toHaveBeenCalledWith(userId);
        });
        
        it('should throw error when user not found', async () => {
            // Arrange
            const userId = 123;
            mockUserRepository.findById.mockResolvedValue(null);
            
            // Act & Assert
            await expect(userService.getUserById(userId))
                .rejects.toThrow('User not found');
        });
    });
});
```

## Performance Considerations

### Memory Management
- Avoid memory leaks with proper cleanup
- Use weak references when appropriate
- Clear timers and event listeners
- Avoid circular references

### Optimization Techniques
- Use `Object.freeze()` for immutable objects
- Use `Map` and `Set` for better performance with large datasets
- Avoid unnecessary DOM manipulations
- Use `requestAnimationFrame` for animations

```javascript
// Good
const userMap = new Map();
const activeUsers = new Set();

// Avoid
const userObject = {};
const activeUsersArray = [];
```

## Security Guidelines

### Input Validation
- Validate all external input
- Sanitize user input
- Use parameterized queries
- Implement proper authentication and authorization

```javascript
// Good
function validateUserInput(userData) {
    const { name, email, age } = userData;
    
    if (!name || typeof name !== 'string' || name.trim().length === 0) {
        throw new ValidationError('Name is required and must be a non-empty string');
    }
    
    if (!email || !isValidEmail(email)) {
        throw new ValidationError('Valid email is required');
    }
    
    if (age && (typeof age !== 'number' || age < 0 || age > 150)) {
        throw new ValidationError('Age must be a number between 0 and 150');
    }
    
    return {
        name: name.trim(),
        email: email.toLowerCase().trim(),
        age: age || null
    };
}
```

## Common Patterns

### Module Pattern
```javascript
// userService.js
class UserService {
    constructor(userRepository, logger) {
        this.userRepository = userRepository;
        this.logger = logger;
    }
    
    async getUserById(id) {
        // Implementation
    }
}

module.exports = UserService;
```

### Factory Pattern
```javascript
class UserFactory {
    static createUser(userData) {
        return {
            id: generateId(),
            ...userData,
            createdAt: new Date(),
            isActive: true
        };
    }
    
    static createAdminUser(userData) {
        return {
            ...this.createUser(userData),
            role: 'admin',
            permissions: ['read', 'write', 'delete']
        };
    }
}
```

## Tools and Configuration

### Required Dependencies
- Express.js (for Node.js applications)
- Jest (for testing)
- ESLint (for linting)
- Prettier (for formatting)
- Winston (for logging)

### ESLint Configuration
```json
{
    "extends": ["eslint:recommended", "prettier"],
    "env": {
        "browser": true,
        "node": true,
        "es6": true
    },
    "rules": {
        "no-var": "error",
        "prefer-const": "error",
        "no-unused-vars": "error",
        "no-console": "warn",
        "eqeqeq": "error",
        "curly": "error"
    }
}
```

### Prettier Configuration
```json
{
    "semi": true,
    "trailingComma": "es5",
    "singleQuote": true,
    "printWidth": 80,
    "tabWidth": 2,
    "useTabs": false
}
```

## References

- [MDN JavaScript Guide](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Guide)
- [JavaScript Best Practices](https://github.com/airbnb/javascript)
- [Node.js Best Practices](https://github.com/goldbergyoni/nodebestpractices)