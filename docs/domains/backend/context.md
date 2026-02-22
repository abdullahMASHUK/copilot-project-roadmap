# Backend Domain Context

This document describes backend architecture, technology choices, patterns, and conventions. Reference this when building backend features, APIs, and services.

**Last Updated**: February 23, 2026

---

## Architecture Overview

[Describe backend architecture at high level]

### Services & Components

| Component | Purpose | Technology | Owner |
|-----------|---------|-----------|-------|
| [Service Name] | [What it does] | [Tech stack] | [Team/person] |

### API Design

**Version**: v1

**Base URL**: `https://api.example.com/v1`

**Response Format**:
```json
{
  "data": {...},
  "meta": {
    "timestamp": "ISO-8601",
    "version": "1.0"
  },
  "errors": null
}
```

**Error Format**:
```json
{
  "data": null,
  "errors": [
    {
      "code": "ERROR_TYPE",
      "message": "Human-readable error message",
      "field": "field_name" // optional
    }
  ]
}
```

### Authentication & Authorization

**Method**: [JWT / OAuth / Sessions / Other]

**Token Expiration**: [Duration]

**Default Permission**: Deny unless explicitly allowed

**Roles** (define if applicable):
- Admin
- User
- Guest

---

## Database

### Schema Overview

[Describe overall data model]

### Key Tables

| Table | Purpose | Owner | Notes |
|-------|---------|-------|-------|
| `users` | User accounts | [Team] | [Notes] |

### Naming Conventions

- **Tables**: `snake_case`, plural (e.g., `user_profiles`)
- **Columns**: `snake_case` (e.g., `created_at`, `is_active`)
- **Primary Keys**: `id` (UUID or auto-increment per table)
- **Foreign Keys**: `{singular_table}_id` (e.g., `user_id`)
- **Indexes**: Added for foreign keys and frequently queried columns

### Migrations

- **Tool**: [Knex / Alembic / Sequelize / Other]
- **Storage**: `migrations/` directory
- **Naming**: `YYYY-MM-DD-HH-mm-description.js`
- **Testing**: All migrations tested forward and backward

### Backup & Recovery

- **Frequency**: [Daily / Continuous / Other]
- **Retention**: [Duration]
- **Recovery RTO**: [Target Recovery Time]
- **Recovery RPO**: [Target Recovery Point]

---

## Services & Utilities

### Authentication Service

**Location**: `src/services/auth.ts`

**Key Functions**:
- `authenticate(email, password)` — Login user
- `verifyToken(token)` — Validate JWT token
- `generateToken(user)` — Create new token

**Patterns**:
- Always hash passwords with bcrypt (saltRounds: 10)
- Tokens expire in [duration]
- [Other patterns]

### [Other Service Name]

[Similar structure as above]

---

## Code Organization

### Directory Structure

```
src/
├── handlers/          # HTTP request handlers
├── services/          # Business logic
├── models/            # Data models / schemas
├── middleware/        # Express middleware
├── utils/             # Utility functions
├── config/            # Configuration
└── tests/             # Test files
```

### Patterns & Standards

#### Error Handling

Use custom error types extending base `AppError`:

```typescript
class ValidationError extends AppError {
  constructor(message: string, details?: Record<string, string>) {
    super(message, 400, details);
  }
}
```

#### Logging

Use structured logging with context:

```typescript
logger.info('User created', { userId: user.id, email: user.email });
logger.error('Database error', { error: err.message, userId });
```

#### Database Operations

Use transactions for multi-step operations:

```typescript
const result = await db.transaction(async (trx) => {
  // Multiple operations in transaction
});
```

#### API Validation

Validate all inputs:

```typescript
const schema = z.object({
  email: z.string().email(),
  password: z.string().min(8),
});

const validated = schema.parse(req.body);
```

---

## Performance Considerations

### Query Optimization

- **Indexes**: Add on foreign keys and frequently queried columns
- **N+1 Queries**: Batch load related data, don't query per item
- **Pagination**: Limit large result sets with offset/limit
- **Caching**: Cache read-heavy operations (Redis)

### Caching Strategy

**Cache Layer**: [Redis / In-Memory / Other]

**What to Cache**:
- User profiles
- Configuration data
- Computed aggregations

**What NOT to Cache**:
- User authentication tokens (use database)
- Frequently changing data
- Real-time data

**Cache Invalidation**:
- Time-based: TTL of [duration]
- Event-based: Invalidate on data mutation
- Explicit: Manual invalidation via API

### Database Performance

**Slow Query Threshold**: 100ms

**Monitoring**: [Tool used to profile queries]

**Common Bottlenecks**:
- Missing indexes
- Inefficient joins
- Large result sets

---

## Deployment & Operations

### Environment Variables

| Variable | Purpose | Example |
|----------|---------|---------|
| `DATABASE_URL` | PostgreSQL connection | `postgres://user:pass@host/db` |
| `JWT_SECRET` | Token signing key | `[keep secret]` |
| `NODE_ENV` | Environment | `production` |

### Monitoring & Alerting

**Metrics**:
- API response time (p50, p95, p99)
- Error rate
- Database connection pool usage

**Logging Tool**: [CloudWatch / DataDog / Splunk / Other]

**Log Levels**:
- DEBUG: Detailed information for developers
- INFO: General operational information
- WARN: Warning conditions (e.g., high response time)
- ERROR: Error conditions (e.g., database failure)

**Alerts**:
- Error rate > 0.1%
- Response time p95 > 500ms
- Database connection pool > 80%

### Health Checks

**Endpoint**: `GET /health`

**Response**:
```json
{
  "status": "ok",
  "checks": {
    "database": "ok",
    "cache": "ok"
  }
}
```

---

## Security

### Input Validation

- Validate all inputs (type, length, format)
- Use schema validation library
- Never trust client data

### Authentication

- Require authentication on all protected endpoints
- Use JWT tokens with appropriate expiration
- Invalidate tokens on logout
- Refresh token rotation for long-lived sessions

### Authorization

- Check user permissions on every protected endpoint
- Default deny (unless explicitly allowed)
- Log all permission checks in audit trail

### Secrets Management

- No hardcoded secrets
- Use environment variables
- Rotate secrets regularly
- Audit access to secrets

### SQL Injection Prevention

- Use parameterized queries (never string concatenation)
- Example: `db.query('SELECT * FROM users WHERE id = ?', [userId])`

---

## Testing

### Unit Tests

**Location**: `tests/unit/`

**Coverage**: ≥ 85% for business logic

**Pattern**:
```typescript
describe('Service', () => {
  it('should handle happy path', () => {
    // Arrange, Act, Assert
  });
});
```

### Integration Tests

**Location**: `tests/integration/`

**Scope**: Test complete workflows with real database

**Database**: Use test database (reset between tests)

### Load Testing

**Tool**: [k6 / JMeter / Locust / Other]

**Scenarios**:
- Baseline load at expected peak
- Burst load (2x peak)
- Sustained high load for [duration]

---

## Development Workflow

### Before Coding

- [ ] Clear understanding of feature requirements
- [ ] API design reviewed with team
- [ ] Database schema approved
- [ ] Test plan documented

### During Coding

- [ ] Follow code patterns
- [ ] Write tests alongside code
- [ ] Use logging for debugging
- [ ] Check for N+1 queries

### Before Submitting PR

- [ ] All tests pass
- [ ] Linting passes
- [ ] Security review complete
- [ ] Database migrations tested
- [ ] Performance acceptable

---

## Useful Commands

```bash
# Database
npm run db:migrate        # Run migrations
npm run db:rollback       # Revert migration
npm run db:seed           # Seed test data

# Testing
npm test                  # Run all tests
npm run test:unit         # Unit tests only
npm run test:integration  # Integration tests
npm run test:coverage     # Coverage report

# Development
npm run dev               # Start dev server
npm run lint              # Run linter
npm run format            # Auto-format code

# Monitoring
npm run db:profile        # Profile database queries
npm run analyze:bundle    # Analyze code size
```

---

## Questions?

- **API design question**: Check existing endpoints in [docs/features/](../features/)
- **Performance issue**: Check [backend/memory.md](./memory.md)
- **Database schema question**: Check migrations folder
- **Stuck on implementation**: Refer to [similar service in codebase]

---

**Domain Context Version**: 1.0  
**Last Updated**: February 23, 2026  
**Maintained By**: [Backend Team Lead]
