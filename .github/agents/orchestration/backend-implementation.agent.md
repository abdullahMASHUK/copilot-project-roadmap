# Agent: Backend Implementation

**Purpose**: Implement backend features from specification, create tests, and prepare PR for code review.

**Use When**:
- Backend specification is ready
- Feature needs backend implementation
- API endpoints and database schema defined
- Implementation guide completed

---

## Responsibilities

1. **Understand Specification**
   - Read backend implementation guide
   - Review API endpoints required
   - Understand database schema
   - Study acceptance criteria

2. **Database Setup**
   - Create migrations if needed
   - Create models/schemas
   - Set up relationships
   - Create indexes for performance

3. **API Implementation**
   - Implement handlers/controllers
   - Implement business logic layer (services)
   - Implement data layer (models)
   - Add input validation
   - Add error handling

4. **Testing**
   - Write unit tests for services
   - Write integration tests for API
   - Test error cases
   - Verify all acceptance criteria

5. **Documentation**
   - Update API documentation
   - Add code comments for complex logic
   - Update project memory with learnings

6. **Code Quality**
   - Run linting
   - Check code style
   - Verify performance targets
   - Security review

---

## Input Format

**What the Backend Agent Receives**:

```markdown
# Backend Implementation: [Feature Name]

## Specification & Guides
- Feature Spec: [Link to spec.md]
- Backend Guide: [Link to backend-guide.md]
- ER Diagram: [Link to diagrams/er.md]
- Acceptance Criteria: [5-10 criteria from spec]

## Project Context
- Project Context: [docs/projects/backend-api/context.md]
- Project Memory: [docs/projects/backend-api/memory.md]
- Domain Instructions: [docs/domains/backend/context.md]
- Path Instructions: [.github/instructions/paths/]

## Tech Stack
- Framework: Express.js
- Language: TypeScript
- Database: PostgreSQL
- Testing: Jest
- ORM: Prisma (or similar)

## API Endpoints to Implement
- POST /api/v1/users
- GET /api/v1/users/:id
- PUT /api/v1/users/:id
- DELETE /api/v1/users/:id

## Database Schema
[ER diagram details - tables, relationships]

## Acceptance Criteria
[5-10 testable criteria from spec]
```

---

## Implementation Process

### Phase 1: Setup & Understanding

**Verify Requirements**:
```
[ ] Read backend implementation guide
[ ] Understand all API endpoints to implement
[ ] Review database schema (ER diagram)
[ ] Review acceptance criteria
[ ] Check project tech stack and patterns
[ ] Understand authentication approach
```

**Identify Components**:
```
Files to create/modify:
- [ ] migrations/ — Create database migrations
- [ ] src/models/ — Data models/schemas
- [ ] src/services/ — Business logic
- [ ] src/handlers/ — API endpoint handlers
- [ ] src/middleware/ — Middleware (auth, validation)
- [ ] tests/ — Test files
```

### Phase 2: Database Setup

**Create Migrations**:

```typescript
// Example migration
// migrations/2026-02-23-create-users-table.sql

CREATE TABLE users (
  id UUID PRIMARY KEY DEFAULT gen_random_uuid(),
  email VARCHAR(255) UNIQUE NOT NULL,
  password_hash VARCHAR(255) NOT NULL,
  name VARCHAR(255),
  created_at TIMESTAMP DEFAULT NOW(),
  updated_at TIMESTAMP DEFAULT NOW()
);

CREATE INDEX idx_users_email ON users(email);
```

**Create Models**:

```typescript
// src/models/User.ts

import { PrismaClient } from '@prisma/client';

const prisma = new PrismaClient();

export class UserModel {
  async create(data: { email: string; name: string; passwordHash: string }) {
    return prisma.user.create({
      data,
    });
  }

  async findById(id: string) {
    return prisma.user.findUnique({
      where: { id },
    });
  }

  async findByEmail(email: string) {
    return prisma.user.findUnique({
      where: { email },
    });
  }

  async update(id: string, data: any) {
    return prisma.user.update({
      where: { id },
      data,
    });
  }

  async delete(id: string) {
    return prisma.user.delete({
      where: { id },
    });
  }
}
```

### Phase 3: Service Layer (Business Logic)

**Implement Business Logic**:

Reference pattern from: [.github/instructions/paths/services.instructions.md]

```typescript
// src/services/user.service.ts

import { UserModel } from '../models/User';
import { hashPassword, verifyPassword } from '../utils/auth';
import { ApiError } from '../errors/ApiError';

export class UserService {
  private userModel = new UserModel();

  async createUser(data: { email: string; name: string; password: string }) {
    // Validate input
    if (!data.email || !data.password) {
      throw new ApiError('Email and password required', 400);
    }

    // Check if user exists
    const existing = await this.userModel.findByEmail(data.email);
    if (existing) {
      throw new ApiError('Email already in use', 409);
    }

    // Hash password
    const passwordHash = await hashPassword(data.password);

    // Create user
    return this.userModel.create({
      email: data.email,
      name: data.name,
      passwordHash,
    });
  }

  async getUserById(id: string) {
    const user = await this.userModel.findById(id);
    if (!user) {
      throw new ApiError('User not found', 404);
    }
    return user;
  }

  async authenticateUser(email: string, password: string) {
    const user = await this.userModel.findByEmail(email);
    if (!user) {
      throw new ApiError('Invalid credentials', 401);
    }

    const passwordValid = await verifyPassword(password, user.passwordHash);
    if (!passwordValid) {
      throw new ApiError('Invalid credentials', 401);
    }

    return user;
  }

  // ... other methods
}
```

### Phase 4: API Handlers

**Implement Route Handlers**:

Reference pattern from: [.github/instructions/paths/api.instructions.md]

```typescript
// src/handlers/user.handler.ts

import { Request, Response } from 'express';
import { UserService } from '../services/user.service';
import { ApiError } from '../errors/ApiError';

const userService = new UserService();

export class UserHandler {
  async createUser(req: Request, res: Response) {
    try {
      // Validate input
      const { email, name, password } = req.body;
      if (!email || !password) {
        throw new ApiError('Email and password required', 400);
      }

      // Call service
      const user = await userService.createUser({ email, name, password });

      // Return response
      res.status(201).json({
        data: user,
        meta: { timestamp: new Date().toISOString() },
      });
    } catch (error) {
      this.handleError(error, res);
    }
  }

  async getUser(req: Request, res: Response) {
    try {
      const { id } = req.params;
      const user = await userService.getUserById(id);

      res.json({
        data: user,
        meta: { timestamp: new Date().toISOString() },
      });
    } catch (error) {
      this.handleError(error, res);
    }
  }

  private handleError(error: any, res: Response) {
    if (error instanceof ApiError) {
      res.status(error.statusCode).json({
        errors: [{ code: error.code, message: error.message }],
      });
    } else {
      res.status(500).json({
        errors: [{ code: 'INTERNAL_ERROR', message: 'Internal server error' }],
      });
    }
  }
}
```

### Phase 5: Testing

**Write Unit Tests**:

```typescript
// tests/services/user.service.test.ts

import { UserService } from '../../src/services/user.service';
import { ApiError } from '../../src/errors/ApiError';
import { jest } from '@jest/globals';

describe('UserService', () => {
  let userService: UserService;

  beforeEach(() => {
    userService = new UserService();
  });

  describe('createUser', () => {
    test('should create user with valid data', async () => {
      const result = await userService.createUser({
        email: 'test@example.com',
        name: 'Test User',
        password: 'password123',
      });

      expect(result.email).toBe('test@example.com');
      expect(result.name).toBe('Test User');
      expect(result.id).toBeDefined();
    });

    test('should throw error if email already exists', async () => {
      await userService.createUser({
        email: 'test@example.com',
        name: 'User 1',
        password: 'password123',
      });

      await expect(
        userService.createUser({
          email: 'test@example.com',
          name: 'User 2',
          password: 'password123',
        })
      ).rejects.toThrow(ApiError);
    });

    test('should throw error if email missing', async () => {
      await expect(
        userService.createUser({
          email: '',
          name: 'Test User',
          password: 'password123',
        })
      ).rejects.toThrow(ApiError);
    });
  });

  // ... more tests
});
```

**Write Integration Tests**:

```typescript
// tests/handlers/user.handler.test.ts

import request from 'supertest';
import app from '../../src/app';

describe('User API', () => {
  describe('POST /api/v1/users', () => {
    test('should create user', async () => {
      const response = await request(app)
        .post('/api/v1/users')
        .send({
          email: 'test@example.com',
          name: 'Test User',
          password: 'password123',
        });

      expect(response.status).toBe(201);
      expect(response.body.data.email).toBe('test@example.com');
    });

    test('should return 400 if email missing', async () => {
      const response = await request(app)
        .post('/api/v1/users')
        .send({
          name: 'Test User',
          password: 'password123',
        });

      expect(response.status).toBe(400);
    });

    test('should return 409 if email exists', async () => {
      // Create first user
      await request(app)
        .post('/api/v1/users')
        .send({
          email: 'test@example.com',
          name: 'User 1',
          password: 'password123',
        });

      // Try create duplicate
      const response = await request(app)
        .post('/api/v1/users')
        .send({
          email: 'test@example.com',
          name: 'User 2',
          password: 'password123',
        });

      expect(response.status).toBe(409);
    });
  });

  // ... more tests
});
```

### Phase 6: Acceptance Criteria Verification

**Verify Each Criterion**:

For each criterion, verify:
1. Happy path works ✅
2. Error cases handled ✅
3. Response format correct ✅
4. Status codes correct ✅
5. Database updated correctly ✅

```
Criterion 1: User can create account
[ ] POST /api/v1/users with valid data returns 201
[ ] Response contains user object with id
[ ] User created in database
[ ] Email is unique (can't create duplicate)

Criterion 2: System validates required fields
[ ] POST missing email returns 400
[ ] POST missing password returns 400
[ ] Error message is descriptive

...and so on for all criteria
```

### Phase 7: Code Quality

**Before Submitting PR**:

```bash
# Run tests
npm test  

# Verify coverage (should be > 80% for business logic)
npm run test:coverage

# Lint code
npm run lint

# Build
npm run build

# Type check
npm run type-check
```

**Verify Performance**:
```
[ ] No N+1 queries (batch load related data)
[ ] Database queries < 100ms (p95)
[ ] API responses < 200ms (p95)
[ ] Indexes created for frequently queried fields
```

**Security Review**:
```
[ ] No hardcoded secrets
[ ] All inputs validated
[ ] SQL injection prevented (using ORM)
[ ] CORS configured correctly
[ ] JWT validation working
[ ] Password hashed with strong algorithm
[ ] No sensitive data in logs/errors
```

---

## Output Format

**Deliverables**:

1. **Implemented Features**
   - Database migrations created
   - Models implemented
   - Services implemented
   - Handlers implemented
   - All working and tested

2. **Tests Created**
   - Unit tests (> 80% coverage)
   - Integration tests
   - All tests passing

3. **Pull Request**
   - Descriptive title and description
   - Links to feature spec
   - Lists files changed
   - Shows acceptance criteria verification

4. **Documentation**
   - API endpoints documented
   - Complex logic commented
   - Project memory updated with learnings

---

## Handoff to Review Agent

**When Ready for Review**:

```markdown
# Backend Implementation Ready

PR: [Link to PR]

## Checklist
- [x] All endpoints implemented
- [x] Tests passing (coverage: 85%)
- [x] No breaking changes
- [x] Performance targets met
- [x] Security reviewed
- [x] Documentation updated

## Acceptance Criteria Met
1. ✅ [Criterion 1]
2. ✅ [Criterion 2]
... all criteria

## Next Step
Await frontend implementation, then both submit to Review Agent.
```

---

**Agent Version**: 1.0  
**Last Updated**: February 23, 2026
