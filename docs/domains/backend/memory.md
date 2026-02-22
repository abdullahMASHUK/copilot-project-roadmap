# Backend Domain Memory

This document records backend-specific learnings, gotchas, performance notes, and patterns discovered through implementation. Update this continuously after each sprint and significant feature.

**Last Updated**: February 23, 2026

---

## Recent Updates

(Add new learnings at top with date)

### [YYYY-MM-DD] — [Learning Title]

**Context**: [Where/when this was discovered]

**Issue / Pattern**: [What we learned]

**Action Taken**: [What we changed or implemented]

**Impact**: [Performance, reliability, developer experience, etc.]

**Reference**: [Link to PR, commit, or feature spec]

---

## Known Issues & Workarounds

### [Component/Service]: [Issue Name]

**Symptom**: [What happens when you encounter this]

**Root Cause**: [Why it occurs]

**Workaround**: [How to avoid or handle it]

**Long-term Fix**: [Plan to resolve permanently, if any]

**Status**: [Active / Monitoring / Resolved]

---

## Performance Notes

### Query Optimization

**Pattern**: Batch load related data instead of per-item queries

**Example**:
```javascript
// ❌ Bad: N+1 query problem
const users = await db('users');
for (const user of users) {
  user.posts = await db('posts').where('user_id', user.id);
}

// ✅ Good: Batch load
const users = await db('users');
const postsMap = await db('posts')
  .whereIn('user_id', users.map(u => u.id))
  .then(posts => groupBy(posts, 'user_id'));
users.forEach(u => u.posts = postsMap[u.id] || []);
```

**Impact**: Reduced query count from N to 1, response time < 100ms

---

### Database Connection Pooling

**Pattern**: Configure connection pool size based on workload

**Config**:
```javascript
const pool = {
  min: 2,      // Minimum connections to maintain
  max: 10,     // Maximum connections allowed
};
```

**Monitoring**: Watch pool.waitingCount for exhaustion

---

### Caching Strategy for User Profiles

**Pattern**: Cache user data with 5-minute TTL

**Key**: `user:{userId}`

**Invalidation**: Clear on profile update

**Impact**: 300% faster user lookups, reduced database load

**Caution**: Don't cache permission-dependent data (stale permissions)

---

## Patterns & Best Practices

### Service Organization

**Pattern**: One service per domain responsibility

**Example**:
- `AuthService` — Authentication only
- `UserService` — User CRUD and profile
- `PermissionService` — Authorization checks

**Benefit**: Single responsibility, easier testing, reusable

---

### Error Handling Pattern

**Pattern**: Custom error types with context

```typescript
class ValidationError extends AppError {
  constructor(field: string, message: string) {
    super(`Validation failed: ${field}`, 400, { field, message });
    this.name = 'ValidationError';
  }
}
```

**Benefit**: Consistent error handling, detailed error info, proper status codes

---

### Database Transaction Pattern

**Pattern**: Transaction for multi-step operations

```typescript
const result = await db.transaction(async (trx) => {
  // Update user
  const user = await trx('users').update(...).where('id', userId);
  // Create audit log
  await trx('audit_logs').insert({ userId, action: 'update' });
  return user;
});
```

**Benefit**: Data consistency, atomic operations, automatic rollback on error

---

## Testing Patterns

### Test Database Seeding

**Pattern**: Seed common data for all tests

```typescript
beforeEach(async () => {
  await seedDatabase();
  testUser = await createTestUser();
});

afterEach(async () => {
  await cleanupDatabase();
});
```

**Benefit**: Consistent test state, faster test execution

---

### Mock External Services

**Pattern**: Mock third-party APIs and services

```typescript
jest.mock('./email-service', () => ({
  sendEmail: jest.fn().mockResolvedValue({ ok: true }),
}));
```

**Benefit**: Fast tests, no external dependencies, deterministic behavior

---

## Team Agreements

### Code Review Checklist for Backend

- [ ] Tests added (unit + integration)
- [ ] No hardcoded secrets or sensitive data
- [ ] Input validation on all endpoints
- [ ] Error handling for all paths
- [ ] Database queries optimized (checked for N+1)
- [ ] Logging added where helpful
- [ ] Performance acceptable (< 200ms p95)
- [ ] Documentation / comments updated

### Performance Expectations

| Metric | Target | Current |
|--------|--------|---------|
| API Response Time (p95) | < 200ms | [TBD] |
| Database Query (p95) | < 100ms | [TBD] |
| Error Rate | < 0.1% | [TBD] |

---

## Lessons From Incidents

### Incident: Database Connection Pool Exhaustion

**Date**: [Date if applicable]

**Symptoms**: Requests hang, timeout after 30 seconds

**Root Cause**: Connection pool max was too low (2 connections) for concurrent load

**Resolution**: Increased max to 10, added monitoring

**Prevention**: Added alerts on connection pool usage > 80%

**Status**: Resolved

---

## Future Considerations

### Planned Optimizations

- [ ] [Optimization 1 and why]
- [ ] [Optimization 2]

### Technology Experiments

- [ ] [Experiment 1]
- [ ] Evaluate [tool/technology] for [use case]

### Scaling Concerns

**At 10x Users**: [Bottlenecks we expect]

**At 100x Users**: [Architecture changes needed]

---

## Quick Reference

### Common Commands

```bash
# Database health check
npm run db:status

# Find slow queries
npm run db:slow-queries

# Profile endpoint
npm run profile /api/endpoint

# Memory usage
node --prof app.js && node --prof-process isolate-*.log > profile.txt
```

### Emergency Procedures

**Database down**:
1. Check connection pool (not maxed)
2. Verify network connectivity
3. Check database service status
4. If persistent, escalate to DBA

**Memory leak suspected**:
1. Check heap snapshot
2. Look for unclosed connections
3. Check for circular references
4. Profile with --inspect flag

---

**Memory Version**: 1.0  
**Last Updated**: February 23, 2026  
**Maintained By**: [Backend Team Lead]
