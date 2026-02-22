# Prompt: refactor-slice

**Purpose**: Improve code quality, readability, and maintainability while preserving functionality.

**When to Use**:
- Simplifying complex functions
- Removing duplicate code
- Improving component structure
- Enhancing readability
- Reducing technical debt
- Optimizing performance

## Usage

Include this in your Copilot chat with the relevant code:

```
@copilot refactor-slice

Code location: [path/to/file.ts](path/to/file.ts#L10-L50)
Domain context: [docs/domains/{domain}/context.md](../../docs/domains/{domain}/context.md)
```

## Refactoring Goals (Priority Order)

1. **Preserve Behavior** — 100% of existing functionality must work
2. **Improve Readability** — Code is easier to understand
3. **Reduce Complexity** — Lower cyclomatic complexity
4. **Optimize Performance** — Faster execution, less memory (if applicable)
5. **Follow Conventions** — Align with domain standards

## Refactoring Checklist

- [ ] Current behavior documented before changes
- [ ] Tests cover current functionality
- [ ] All tests pass before refactoring
- [ ] All tests pass after refactoring
- [ ] No functionality removed or changed
- [ ] Code follows domain conventions
- [ ] Comments/documentation updated
- [ ] Performance impact analyzed (if relevant)

## Common Refactoring Patterns

### Extract Function
```
Objective: Break large function into smaller, reusable functions
Benefit: Easier to test, maintain, and understand
Risk: Very low if tests verify same behavior
```

### Simplify Conditional Logic
```
Objective: Convert nested if/else to guard clauses or ternary
Benefit: Improved readability, fewer branches
Risk: Low if tests cover all conditions
```

### Remove Duplication (DRY)
```
Objective: Consolidate repeated code into shared function
Benefit: Single source of truth, easier maintenance
Risk: Medium — ensure extracted function generalizes correctly
```

### Rename Variables/Functions
```
Objective: Use clearer, more descriptive names
Benefit: Self-documenting code
Risk: Very low if you use refactoring tools
```

### Replace Magic Numbers
```
Objective: Extract constants for magic numbers
Benefit: Self-documenting, easier to maintain
Risk: Very low
```

## Example Interaction

> I want to refactor this complex authentication logic:
>
> [src/auth/login.ts](src/auth/login.ts#L15-L65)
>
> Goals:
> - Extract validation logic into separate function
> - Simplify the password hashing flow
> - Improve readability without changing behavior
>
> Maintain these patterns from [docs/domains/backend/context.md](../../docs/domains/backend/context.md):
> - Error handling with custom error types
> - Logging at appropriate levels
> - Service injection pattern

## Before & After Pattern

**Before** (complex, hard to test):
```typescript
function login(email, password) {
  if (!email || !password) return { error: 'Invalid input' };
  const user = db.query(`SELECT * FROM users WHERE email = ?`, [email]);
  if (!user) return { error: 'User not found' };
  const hash = bcrypt.hash(password, user.salt);
  if (hash !== user.password_hash) return { error: 'Invalid password' };
  const token = jwt.sign({ id: user.id }, SECRET);
  db.query(`UPDATE users SET last_login = NOW() WHERE id = ?`, [user.id]);
  return { token, user: { id: user.id, email: user.email } };
}
```

**After** (clear, testable, maintainable):
```typescript
function login(email: string, password: string): Result<LoginSuccess, LoginError> {
  validateInput(email, password);
  const user = findUserByEmail(email);
  verifyPassword(password, user);
  const token = generateAuthToken(user);
  recordLoginTime(user.id);
  return { token, user: sanitizeUser(user) };
}
```

## Performance Refactoring

Only refactor for performance when:
1. Bottleneck is measured (profiled)
2. Baseline metrics are documented
3. Expected improvement is significant (>10%)
4. Improvement is verified post-refactor
5. Changes documented in memory.md

## Output Checklist

- [ ] Original behavior preserved (all tests pass)
- [ ] Code is more readable
- [ ] Complexity reduced
- [ ] Follows domain conventions
- [ ] No performance regression
- [ ] Comments/docs updated
- [ ] Ready for merge

---

**Prompt Version**: 1.0  
**Last Updated**: February 23, 2026
