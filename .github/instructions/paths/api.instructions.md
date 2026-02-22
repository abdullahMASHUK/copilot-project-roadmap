# Path Instructions: src/handlers/ (API Endpoints)

This file provides specific guidance for building API endpoints and route handlers.

## File Structure

**Naming Convention**:
```
src/handlers/
├── user.handler.ts         (one entity = one file)
├── post.handler.ts
├── auth.handler.ts
├── index.ts                (re-exports all handlers)
```

**File Name Format**: `{entity}.handler.ts` or `{entity}.controller.ts`

## Handler Template

```typescript
import { Request, Response } from 'express';
import * as userService from '../services/user.service';
import { ValidationError, NotFoundError } from '../errors';

/**
 * @route GET /api/v1/users/:id
 * @access Public
 * @description Get a user by ID
 */
export const getUser = async (req: Request, res: Response) => {
  try {
    const { id } = req.params;
    const user = await userService.getUserById(id);
    
    if (!user) {
      throw new NotFoundError('User not found');
    }

    res.json({
      data: user,
      meta: { timestamp: new Date().toISOString() },
    });
  } catch (error) {
    handleError(error, res);
  }
};

/**
 * @route POST /api/v1/users
 * @access Public
 * @description Create a new user
 * @body { email: string, password: string, name: string }
 */
export const createUser = async (req: Request, res: Response) => {
  try {
    const { email, password, name } = req.body;

    // Validation
    if (!email || !password || !name) {
      throw new ValidationError('email, password, and name required');
    }

    const user = await userService.createUser({ email, password, name });

    res.status(201).json({
      data: user,
      meta: { timestamp: new Date().toISOString() },
    });
  } catch (error) {
    handleError(error, res);
  }
};
```

## Best Practices

### Validation

- Validate in handler before passing to service
- Use schema validation library (zod, joi, etc.)
- Never pass raw `req.body` to service

```typescript
// ✅ Good
const schema = z.object({
  email: z.string().email(),
  password: z.string().min(8),
});
const validated = schema.parse(req.body);
await userService.createUser(validated);

// ❌ Bad
await userService.createUser(req.body);
```

### Error Handling

- Custom error types with appropriate HTTP status codes
- Consistent error response format
- Never expose stack traces to client

```typescript
// ✅ Good error handling
try {
  // ...
} catch (error) {
  if (error instanceof ValidationError) {
    res.status(400).json({ errors: [error.message] });
  } else if (error instanceof NotFoundError) {
    res.status(404).json({ errors: [error.message] });
  } else {
    logger.error('Unexpected error', error);
    res.status(500).json({ errors: ['Internal server error'] });
  }
}
```

### Request/Response Logging

- Log request method, path, status code
- Include user ID if authenticated
- Log errors with full context

```typescript
logger.info('Request processed', {
  method: req.method,
  path: req.path,
  statusCode: res.statusCode,
  userId: req.user?.id,
  duration: Date.now() - startTime,
});
```

### Async Errors

- Always wrap async code in try/catch
- Use middleware to catch uncaught errors
- Never leave promises unhandled

```typescript
// ✅ Good: Errors handled
export const handler = async (req: Request, res: Response) => {
  try {
    const result = await asyncOperation();
    res.json(result);
  } catch (error) {
    handleError(error, res);
  }
};
```

## Documentation

Add JSDoc comments to every handler:

```typescript
/**
 * @route GET /api/v1/users/:id
 * @access Public | Private | Admin
 * @description [What this endpoint does]
 * @param {string} id - User ID
 * @returns {Object} User object
 * @throws {NotFoundError} 404 if user doesn't exist
 */
```

## Testing

Test every handler:

```typescript
describe('User Handlers', () => {
  describe('getUser', () => {
    it('should return user when found', async () => {
      const req = { params: { id: '123' } };
      const res = { json: jest.fn() };
      
      jest.spyOn(userService, 'getUserById').mockResolvedValue({ id: '123', name: 'John' });
      
      await getUser(req as any, res as any);
      
      expect(res.json).toHaveBeenCalledWith(
        expect.objectContaining({
          data: expect.objectContaining({ id: '123' }),
        })
      );
    });

    it('should return 404 when user not found', async () => {
      // Similar test
    });
  });
});
```

## Anti-Patterns to Avoid

❌ **DON'T**:
- Put business logic in handlers (use services)
- Skip input validation
- Return raw database objects (expose IDs you shouldn't)
- Mix different concerns (auth, validation, business logic)
- Forget error handling
- Log sensitive data (passwords, tokens)

✅ **DO**:
- Keep handlers thin (delegation focused)
- Validate all inputs
- Transform responses before sending
- Separate concerns across handler → service → model
- Handle all error paths
- Log safely (sanitize sensitive data)

---

**Instructions Version**: 1.0  
**Last Updated**: February 23, 2026
