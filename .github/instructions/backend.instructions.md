# Backend Development Instructions

This document guides backend development work when using Copilot. It establishes patterns, standards, and conventions for backend engineers on the team.

## Context References

Always reference these files when working on backend tasks:

- **Context**: [docs/domains/backend/context.md](../../docs/domains/backend/context.md) — Architecture, tech stack, API standards
- **Memory**: [docs/domains/backend/memory.md](../../docs/domains/backend/memory.md) — Known issues, workarounds, performance notes
- **Global**: [docs/_global/context.md](../../docs/_global/context.md) — Project-wide architecture

## Code Standards

### Language & Framework
- Primary language: (to be defined in context.md)
- Framework: (to be defined in context.md)
- Package manager: (to be defined in context.md)

### Style & Conventions
- Follow project linting rules (`.eslintrc`, `.pylintrc`, etc.)
- Use consistent naming: `camelCase` for functions/variables, `PascalCase` for classes
- Comment complex logic; avoid obvious comments
- Keep functions focused on single responsibility

### Database
- All schema changes must be versioned migrations
- Reference: [docs/domains/backend/context.md](../../docs/domains/backend/context.md) for DB schema
- Check: [docs/domains/backend/memory.md](../../docs/domains/backend/memory.md) for known performance issues

### API Design
- RESTful endpoints: `GET /resource`, `POST /resource`, `PUT /resource/:id`, `DELETE /resource/:id`
- Response format: `{ data, meta, errors }`
- Error codes: 400 (bad input), 401 (auth), 403 (permission), 404 (not found), 500 (server)
- Always validate input; never trust client data

### Error Handling
- Log errors with context (what failed, why, relevant IDs)
- Use consistent error codes across the team
- Expose minimal error details to client (no stack traces)
- Document error scenarios in feature specs

## Testing Requirements

### Unit Tests
- Minimum 80% code coverage
- Test happy path, edge cases, and error conditions
- Use mocking for external dependencies (database, APIs, etc.)

### Integration Tests
- Test complete workflows (request → database → response)
- Use test database or fixtures
- Clean up after each test

### Naming Convention
- `test_function_name_given_condition_expects_behavior`
- Example: `test_authenticate_given_invalid_password_expects_unauthorized`

## Common Tasks

### Implementing a Feature
1. Read the feature spec: `docs/features/{feature}/spec.md`
2. Use prompt: **implement-from-spec**
3. Copilot should reference backend context automatically
4. Include tests (unit + integration)
5. Update `docs/domains/backend/memory.md` with any learnings

### Security Review
1. Use prompt: **security-review**
2. Check for: SQL injection, XSS, auth bypass, data leaks
3. Reference: [docs/domains/backend/context.md](../../docs/domains/backend/context.md) security section

### Refactoring
1. Use prompt: **refactor-slice**
2. Maintain 100% of existing behavior
3. Run full test suite before submitting

### Performance Optimization
1. Profile first (don't optimize blindly)
2. Document baseline metrics before changes
3. Verify improvement with repeat profiling
4. Add performance expectation to `docs/domains/backend/memory.md`

## Development Workflow

### Before Coding
- [ ] Feature spec is clear and approved
- [ ] Have context files open/referenced
- [ ] Understand existing patterns from memory

### During Coding
- [ ] Follow style conventions
- [ ] Write tests as you go (TDD friendly)
- [ ] Reference memory when stuck
- [ ] Use Copilot for implementation, not design

### Before Submitting
- [ ] All tests pass
- [ ] Code coverage meets threshold
- [ ] No security vulnerabilities (run security-review)
- [ ] Updated memory.md with learnings
- [ ] Commits are clear and descriptive

## Anti-Patterns to Avoid

❌ **DON'T**:
- Hardcode configuration values
- Skip input validation
- Ignore error cases
- Write same logic twice (DRY principle)
- Remove logging for "cleanliness"
- Make large, monolithic functions
- Ignore test coverage reports

✅ **DO**:
- Use environment variables for config
- Validate all inputs
- Handle errors explicitly
- Refactor duplicated code
- Keep logging strategic
- Break functions into smaller units
- Review test coverage before submitting

## Useful Commands

(Define project-specific commands in docs/domains/backend/context.md, e.g., "npm test", "pytest", etc.)

## Questions?

- **Architectural decision**: Check `docs/_global/memory.md`
- **Runtime bug**: Check `docs/domains/backend/memory.md`
- **Unsure of pattern**: Review recent features in `docs/features/`
- **Stuck on implementation**: Use Copilot with the **implement-from-spec** prompt

---

**Instructions Version**: 1.0  
**Last Updated**: February 23, 2026  
**Applies To**: Backend engineers, full-stack developers
