# Prompt: implement-from-spec

**Purpose**: Convert feature specifications into production-ready code with tests.

**When to Use**: 
- Implementing a new feature from a finalized spec
- Building API endpoints
- Creating page components
- Adding business logic

## Usage

Include this in your Copilot chat along with the relevant spec file:

```
@copilot implement-from-spec

Feature spec: [docs/features/{feature}/spec.md](../../docs/features/{feature}/spec.md)
Domain context: [docs/domains/{domain}/context.md](../../docs/domains/{domain}/context.md)
Domain memory: [docs/domains/{domain}/memory.md](../../docs/domains/{domain}/memory.md)
```

## Execution Steps

1. **Clarify Requirements**
   - Confirm acceptance criteria are clear
   - Identify any ambiguous specifications
   - Ask about edge cases not in spec

2. **Review Context**
   - Reference domain conventions
   - Check for similar implementations (DRY principle)
   - Identify relevant patterns from memory

3. **Design (if needed)**
   - Outline approach before coding
   - Consider error handling
   - Plan test coverage

4. **Implementation**
   - Write production code
   - Follow domain standards
   - Include error handling
   - Add inline comments for complex logic

5. **Testing**
   - Unit tests (functions, components, services)
   - Integration tests (workflows, API contracts)
   - Error case tests
   - Target 80%+ coverage

6. **Code Review**
   - Check against spec (did you miss anything?)
   - Verify tests pass
   - Run linting
   - Confirm accessibility (frontend) or security (backend)

## Output Checklist

- [ ] All acceptance criteria met
- [ ] Tests pass (unit + integration)
- [ ] Code follows domain conventions
- [ ] Error handling implemented
- [ ] No security vulnerabilities (backend)
- [ ] Accessible (frontend)
- [ ] Documented (comments, function signatures)
- [ ] Ready for merge

## Example Interaction

> I'm implementing the password reset feature. Here's the spec I need to build:
>
> [docs/features/password-reset/spec.md](../../docs/features/password-reset/spec.md)
>
> Please refer to:
> - [docs/domains/backend/context.md](../../docs/domains/backend/context.md) for API patterns
> - [docs/domains/backend/memory.md](../../docs/domains/backend/memory.md) for known issues
>
> Implement the API endpoints with full test coverage.

---

**Prompt Version**: 1.0  
**Last Updated**: February 23, 2026
