# Path Instructions: src/services/ (Business Logic Services)

This file provides specific guidance for creating service classes and functions that contain business logic.

## Service Organization

**Directory Structure**:
```
src/services/
├── user.service.ts          (user-related business logic)
├── auth.service.ts          (authentication logic)
├── post.service.ts          (post-related logic)
├── email.service.ts         (email sending)
├── payment.service.ts       (payment processing)
└── index.ts                 (re-exports)
```

**File Naming**: `{entity}.service.ts` or `{domain}.service.ts`

## Service Template

```typescript
import * as userModel from '../models/User';
import { ValidationError, NotFoundError, AuthError } from '../errors';
import Logger from '../utils/logger';

/**
 * User Service
 * 
 * Handles all user-related business logic including:
 * - User creation and validation
 * - Password hashing and verification
 * - User profile updates
 */
export const userService = {
  /**
   * Create a new user with validation and hashing
   * @param data - User creation data
   * @returns Created user (without password hash)
   * @throws ValidationError if data invalid
   * @throws Error if email already exists
   */
  async createUser(data: {
    email: string;
    password: string;
    name: string;
  }): Promise<Omit<User, 'password_hash'>> {
    // Validate input
    if (!data.email || !data.password || !data.name) {
      throw new ValidationError(
        'email, password, and name are required'
      );
    }

    // Check for duplicates
    const existing = await userModel.findByEmail(data.email);
    if (existing) {
      throw new Error('Email already in use');
    }

    // Hash password
    const hashedPassword = await hashPassword(data.password);

    // Create user
    const user = await userModel.create({
      email: data.email,
      password_hash: hashedPassword,
      name: data.name,
    });

    Logger.info('User created', { userId: user.id, email: user.email });

    // Return without password hash
    const { password_hash, ...userWithoutPassword } = user;
    return userWithoutPassword;
  },

  /**
   * Get user by ID with error handling
   * @param id - User ID
   * @returns User data
   * @throws NotFoundError if user doesn't exist
   */
  async getUserById(id: string): Promise<User> {
    if (!id) {
      throw new ValidationError('User ID is required');
    }

    const user = await userModel.findById(id);
    if (!user) {
      throw new NotFoundError(`User not found: ${id}`);
    }

    return user;
  },

  /**
   * Update user profile
   * @param id - User ID
   * @param updates - Fields to update
   * @returns Updated user
   */
  async updateUser(
    id: string,
    updates: Partial<{
      name: string;
      bio: string;
    }>
  ): Promise<User> {
    const user = await this.getUserById(id); // Checks existence

    const updated = await userModel.update(id, {
      ...updates,
      updated_at: new Date(),
    });

    Logger.info('User updated', { userId: id, updates });
    return updated;
  },

  /**
   * List users with pagination
   * @param options - Pagination options
   * @returns Users and metadata
   */
  async listUsers(options: {
    limit?: number;
    offset?: number;
    sortBy?: 'created_at' | 'name';
  } = {}): Promise<{
    users: User[];
    total: number;
    limit: number;
    offset: number;
  }> {
    const limit = Math.min(options.limit || 20, 100); // Max 100
    const offset = options.offset || 0;
    const sortBy = options.sortBy || 'created_at';

    const [users, total] = await Promise.all([
      userModel.list({ limit, offset, sortBy }),
      userModel.count(),
    ]);

    return { users, total, limit, offset };
  },
};

// Export type
export type UserService = typeof userService;
```

## Service Patterns

### Single Responsibility

```typescript
// ✅ Good - each service has single concern
export const userService = { /* user operations */ };
export const emailService = { /* email operations */ };
export const paymentService = { /* payment operations */ };

// ❌ Bad - mixed concerns
export const userService = {
  createUser() { /* ... */ },
  sendWelcomeEmail() { /* ... */ },        // Should be in emailService
  processPayment() { /* ... */ },          // Should be in paymentService
};
```

### Dependency Injection

```typescript
// ✅ Good - dependencies passed in
export const createUserService = (
  userModel: UserModel,
  emailService: EmailService,
  logger: Logger,
) => ({
  async createUser(data) {
    const user = await userModel.create(data);
    await emailService.sendWelcome(user.email);
    logger.info('User created', { userId: user.id });
    return user;
  },
});

// Usage
const userService = createUserService(userModel, emailService, logger);
```

### Transaction Management

```typescript
// ✅ Good - transaction wraps multiple operations
export const userService = {
  async createUserWithProfile(userData, profileData) {
    return db.transaction(async (trx) => {
      // Both operations in same transaction
      const user = await userModel.create(userData, trx);
      const profile = await profileModel.create(
        { ...profileData, userId: user.id },
        trx
      );
      return { user, profile };
    });
  },
};
```

### Error Handling

```typescript
// ✅ Good - specific error types
export const userService = {
  async authenticateUser(email: string, password: string) {
    const user = await userModel.findByEmail(email);
    
    if (!user) {
      throw new ValidationError(`User not found: ${email}`);
    }

    const isValid = await verifyPassword(password, user.password_hash);
    
    if (!isValid) {
      throw new AuthError('Invalid password');
    }

    return user;
  },
};

// ❌ Bad - generic errors
export const userService = {
  async authenticateUser(email: string, password: string) {
    try {
      const user = await userModel.findByEmail(email);
      const isValid = await verifyPassword(password, user.password_hash);
      if (!isValid) throw new Error('Auth failed');
      return user;
    } catch (err) {
      throw new Error('Something went wrong');
    }
  },
};
```

### Caching Pattern

```typescript
// ✅ Good - cache expensive operations
import cache from '../utils/cache';

export const userService = {
  async getUserById(id: string) {
    const cacheKey = `user:${id}`;
    
    // Check cache first
    let user = await cache.get(cacheKey);
    if (user) return user;

    // Get from database
    user = await userModel.findById(id);
    if (!user) throw new NotFoundError('User not found');

    // Cache for 5 minutes
    await cache.set(cacheKey, user, 300);
    
    return user;
  },

  async updateUser(id: string, updates) {
    const user = await userModel.update(id, updates);
    
    // Invalidate cache
    await cache.delete(`user:${id}`);
    
    return user;
  },
};
```

## Documentation

### JSDoc for Functions

```typescript
/**
 * Create a new user with validation and hashing
 * 
 * Validates input, checks for duplicates, hashes password,
 * and creates user in database.
 * 
 * @param {Object} data - User creation data
 * @param {string} data.email - User email (must be unique)
 * @param {string} data.password - Plain text password (6+ chars)
 * @param {string} data.name - User full name
 * 
 * @returns {Promise<User>} Created user (without password_hash)
 * 
 * @throws {ValidationError} If any field missing or invalid
 * @throws {Error} If email already in use
 * 
 * @example
 * ```typescript
 * const user = await userService.createUser({
 *   email: 'john@example.com',
 *   password: 'securepassword123',
 *   name: 'John Doe'
 * });
 * ```
 */
async createUser(data: CreateUserRequest): Promise<User> {
  // ...
}
```

## Testing Services

```typescript
describe('userService', () => {
  // Mock dependencies
  const mockUserModel = {
    findByEmail: jest.fn(),
    create: jest.fn(),
    findById: jest.fn(),
  };

  const userService = createUserService(mockUserModel);

  describe('createUser', () => {
    it('should create user with valid input', async () => {
      const userData = {
        email: 'test@example.com',
        password: 'password123',
        name: 'Test User',
      };

      mockUserModel.findByEmail.mockResolvedValue(null); // No duplicate
      mockUserModel.create.mockResolvedValue({
        id: '1',
        ...userData,
      });

      const result = await userService.createUser(userData);

      expect(result).toHaveProperty('id');
      expect(mockUserModel.create).toHaveBeenCalled();
    });

    it('should throw on duplicate email', async () => {
      mockUserModel.findByEmail.mockResolvedValue({ id: 'existing' });

      await expect(
        userService.createUser({
          email: 'existing@example.com',
          password: 'password123',
          name: 'Test',
        })
      ).rejects.toThrow();
    });
  });
});
```

## Anti-Patterns to Avoid

❌ **DON'T**:
- Put API logic in services (handlers should validate input)
- Mix multiple concerns in one service
- Hardcode configuration or magic numbers
- Forget error handling
- Make services tightly coupled
- Return raw database objects (expose details)
- Forget logging for debugging

✅ **DO**:
- Keep services focused (single responsibility)
- Inject dependencies
- Throw specific error types
- Document expected behavior in JSDoc
- Use transactions for multi-step operations
- Transform data before returning
- Log important operations

---

**Instructions Version**: 1.0  
**Last Updated**: February 23, 2026
