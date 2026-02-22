# Path Instructions: tests/ (Test Files)

This file provides specific guidance for writing tests across the project (unit, integration, E2E).

## Test Organization

**Directory Structure**:
```
tests/
├── unit/               (isolated unit tests)
├── integration/        (workflow tests)
├── e2e/                (end-to-end tests)
├── fixtures/           (test data and mocks)
└── helpers/            (test utilities)
```

**File Naming**:
- `fileName.test.ts` — Unit test for `fileName.ts`
- `fileName.integration.test.ts` — Integration tests
- `fileName.e2e.test.ts` — E2E tests

## Test Template

### Unit Test

```typescript
import { calculateTotal } from '../src/services/order.service';
import { ValidationError } from '../src/errors';

describe('calculateTotal', () => {
  describe('happy path', () => {
    it('should calculate total with tax', () => {
      const items = [
        { id: '1', price: 100, quantity: 2 },
        { id: '2', price: 50, quantity: 1 },
      ];
      const result = calculateTotal(items, { taxRate: 0.1 });
      expect(result).toEqual(275); // (100*2 + 50) * 1.1
    });
  });

  describe('edge cases', () => {
    it('should handle empty items', () => {
      const result = calculateTotal([], { taxRate: 0.1 });
      expect(result).toEqual(0);
    });

    it('should handle zero tax rate', () => {
      const items = [{ id: '1', price: 100, quantity: 1 }];
      const result = calculateTotal(items, { taxRate: 0 });
      expect(result).toEqual(100);
    });
  });

  describe('error cases', () => {
    it('should throw on negative quantity', () => {
      const items = [{ id: '1', price: 100, quantity: -1 }];
      expect(() => calculateTotal(items, { taxRate: 0.1 })).toThrow(
        ValidationError
      );
    });
  });
});
```

### Integration Test

```typescript
import { createUser, getUserById } from '../src/services/user.service';
import { User } from '../src/models/User';

describe('User Service - Complete Workflow', () => {
  let testUser: User;

  beforeEach(async () => {
    // Reset database
    await seedDatabase();
  });

  it('should create user and retrieve it', async () => {
    // Arrange
    const userData = {
      email: 'test@example.com',
      password: 'secure123',
      name: 'Test User',
    };

    // Act
    const createdUser = await createUser(userData);
    const retrievedUser = await getUserById(createdUser.id);

    // Assert
    expect(retrievedUser).toEqual(
      expect.objectContaining({
        email: 'test@example.com',
        name: 'Test User',
      })
    );
  });

  afterEach(async () => {
    // Cleanup
    await cleanupDatabase();
  });
});
```

## Naming Conventions

**Format**: `test_{functionName}_given_{condition}_expects_{behavior}`

**Examples**:
```javascript
it('should calculate total given items and tax rate expects correct sum', () => {});
// Shortened (still clear):
it('calculates total with tax correctly', () => {});

it('should return empty array given no items expects empty', () => {});
// Shortened:
it('returns empty array when no items', () => {});

it('should throw ValidationError given negative quantity expects error', () => {});
// Shortened:
it('throws on negative quantity', () => {});
```

## Setup & Teardown

### Common Setup Patterns

```typescript
// Per-test setup
beforeEach(() => {
  // Reset mocks
  jest.clearAllMocks();
});

// Per-describe setup
beforeAll(() => {
  // Start test server/database
  testServer.start();
});

// Cleanup
afterEach(() => {
  jest.restoreAllMocks();
});

afterAll(() => {
  testServer.stop();
});
```

### Test Database Seeding

```typescript
// Use fixtures for consistent test data
const seedUsers = async () => {
  return Promise.all([
    createTestUser({ email: 'admin@test.com', role: 'admin' }),
    createTestUser({ email: 'user@test.com', role: 'user' }),
  ]);
};

describe('Authorization', () => {
  beforeEach(async () => {
    await seedDatabase();
    await seedUsers();
  });

  afterEach(async () => {
    await cleanupDatabase();
  });
});
```

## Mocking Strategies

### When to Mock

✅ **DO Mock**:
- External APIs (payment processors, email services)
- Database (in unit tests)
- File system operations
- Time/clock functions
- Random number generators

❌ **DON'T Mock**:
- Core business logic
- Real database (in integration tests)
- The thing you're testing
- Cryptographic functions (use actual)

### Mock Patterns

```typescript
// Mock external service
jest.mock('../src/services/emailService');
import { sendEmail } from '../src/services/emailService';

it('should send welcome email after signup', async () => {
  (sendEmail as jest.Mock).mockResolvedValue({ ok: true });
  
  await createUser({ email: 'test@example.com' });
  
  expect(sendEmail).toHaveBeenCalledWith(
    expect.objectContaining({ to: 'test@example.com' })
  );
});
```

```typescript
// Mock with different return values per call
const getUserById = jest.fn();
getUserById
  .mockResolvedValueOnce({ id: '1', name: 'User 1' }) // First call
  .mockResolvedValueOnce({ id: '2', name: 'User 2' }) // Second call
  .mockRejectedValueOnce(new Error('Server error'));  // Third call

// Spy on existing function (don't replace)
jest.spyOn(userService, 'createUser').mockResolvedValue({ id: '1' });
```

## Assertions

### Common Assertions

```typescript
// Equality
expect(result).toBe(10);              // Exact match (primitives)
expect(result).toEqual(expected);     // Deep equality (objects)

// Existence
expect(result).toBeDefined();
expect(result).not.toBeDefined();
expect(result).toBeNull();

// Truthiness
expect(result).toBeTruthy();
expect(result).toBeFalsy();

// Arrays
expect(array).toContain('item');
expect(array).toHaveLength(3);
expect(array).toEqual(['a', 'b', 'c']);

// Objects
expect(obj).toHaveProperty('name');
expect(obj).toEqual(expect.objectContaining({ id: '1' }));

// Functions & errors
expect(fn).toHaveBeenCalled();
expect(fn).toHaveBeenCalledWith(arg1, arg2);
expect(fn).toHaveBeenCalledTimes(3);
expect(() => fn()).toThrow(MyError);
```

## Test Organization

### Describe Blocks Structure

```typescript
describe('UserService', () => {
  describe('createUser', () => {
    describe('happy path', () => {
      it('should create user with valid input', () => {});
      it('should hash password before storing', () => {});
    });

    describe('edge cases', () => {
      it('should handle whitespace in email', () => {});
      it('should handle very long names', () => {});
    });

    describe('error cases', () => {
      it('should throw on duplicate email', () => {});
      it('should throw on missing required field', () => {});
    });
  });

  describe('listUsers', () => {
    // Similar structure
  });
});
```

## Coverage Requirements

| Code Type | Minimum | Target |
|-----------|---------|--------|
| Business logic | 80% | 90%+ |
| Utilities | 75% | 85%+ |
| Components | 70% | 80%+ |
| API handlers | 75% | 85%+ |
| Configuration | N/A | N/A (don't test) |

**Skip Testing**:
- Import statements
- Third-party library code
- Configuration files
- Code you don't control

## Common Patterns

### Testing Async Code

```typescript
// Promise-based
it('should fetch user data', async () => {
  const result = await fetchUser('123');
  expect(result).toEqual(expectedUser);
});

// Return promise (also valid)
it('should fetch user data', () => {
  return fetchUser('123').then(result => {
    expect(result).toEqual(expectedUser);
  });
});
```

### Testing Callbacks

```typescript
it('should call onSuccess callback', (done) => {
  const onSuccess = jest.fn();
  
  fetchUserAsync('123', onSuccess);
  
  // Must call done() to end test
  setTimeout(() => {
    expect(onSuccess).toHaveBeenCalled();
    done();
  }, 100);
});
```

### Testing Errors

```typescript
// Promise rejection
it('should reject on network error', async () => {
  jest.spyOn(api, 'get').mockRejectedValue(new Error('Network error'));
  
  await expect(fetchUser('123')).rejects.toThrow('Network error');
});

// Thrown error
it('should throw on invalid input', () => {
  expect(() => {
    createUser(null);
  }).toThrow(ValidationError);
});
```

## Test Utilities

### Custom Test Helpers

```typescript
// tests/helpers/testHelpers.ts
export const createTestUser = (overrides?: Partial<User>): User => ({
  id: 'test-id',
  email: 'test@example.com',
  name: 'Test User',
  createdAt: new Date(),
  ...overrides,
});

export const createTestRequest = (overrides?: Partial<Request>) => ({
  body: {},
  params: {},
  headers: {},
  user: createTestUser(),
  ...overrides,
});
```

Usage:
```typescript
const user = createTestUser({ email: 'custom@example.com' });
const req = createTestRequest({ params: { id: '123' } });
```

## Anti-Patterns to Avoid

❌ **DON'T**:
- Test implementation details (how, not what)
- Create coupled tests (one test depends on another)
- Skip error case testing
- Use `any` types in test code
- Test third-party library behavior
- Have tests without assertions
- Ignore test coverage reports
- Test through UI if testing logic (unit test instead)

✅ **DO**:
- Test behavior (user perspective)
- Make tests independent and reorderable
- Test happy path + errors + edge cases
- Type test data and mocks
- Test your code, assume libraries work
- Every test should verify something
- Review and improve coverage monthly
- Use integration tests for workflows

## Running Tests

```bash
# All tests
npm test

# Specific file
npm test -- user.service.test.ts

# Watch mode
npm test -- --watch

# Coverage report
npm test -- --coverage

# Single test
npm test -- --testNamePattern="should create user"
```

---

**Instructions Version**: 1.0  
**Last Updated**: February 23, 2026
