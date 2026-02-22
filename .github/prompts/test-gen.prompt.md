# Prompt: test-gen

**Purpose**: Generate comprehensive test coverage for code, including unit, integration, and edge case tests.

**When to Use**:
- Filling test gaps in existing code
- Writing tests for new features (TDD)
- Testing edge cases and error conditions
- Adding integration tests
- Improving test coverage metrics

## Usage

Include this in your Copilot chat with the code to test:

```
@copilot test-gen

Code to test: [path/to/file.ts](path/to/file.ts)
Feature spec: [docs/features/{feature}/spec.md](../../docs/features/{feature}/spec.md)
Domain context: [docs/domains/{domain}/context.md](../../docs/domains/{domain}/context.md)
```

## Test Types & Scope

### Unit Tests (60-70% of tests)
- Test individual functions/methods
- Mock external dependencies
- Focus on logic and edge cases
- Fast to run
- Easy to debug

**Minimum Coverage Per Function**:
- Happy path (normal input → expected output)
- Edge cases (boundary values, empty inputs, etc.)
- Error cases (invalid input, exceptions, etc.)
- At least 2-3 test cases per function

### Integration Tests (20-30% of tests)
- Test multiple functions/components together
- Test with real database (test DB)
- Test API workflows end-to-end
- Slower but closer to production
- Catch integration bugs

**Scenarios to Test**:
- Complete user workflows
- Multiple components interacting
- API request → process → response
- Database operations with side effects

### E2E Tests (Optional - 5-10% of tests)
- Test entire application workflows
- Simulate real user actions
- Browser-based or API-based
- Slowest but highest confidence
- Best for critical paths

## Testing Checklist

- [ ] Happy path tested
- [ ] All edge cases covered
- [ ] All error conditions tested
- [ ] External dependencies mocked (unit tests)
- [ ] Test names clearly describe what they test
- [ ] Tests are independent (can run in any order)
- [ ] Setup/teardown is clean
- [ ] Coverage ≥ 80% for critical code
- [ ] No skipped tests (unless documented)
- [ ] Tests run quickly (< 5 seconds for unit tests)

## Naming Convention

**Format**: `test_{functionName}_given_{condition}_expects_{behavior}`

**Examples**:
```
test_calculateTotal_given_empty_array_expects_zero
test_authenticateUser_given_invalid_password_expects_unauthorized_error
test_getUserProfile_given_nonexistent_user_expects_not_found
test_validateEmail_given_valid_email_expects_true
```

## Common Test Patterns

### Unit Test Template
```typescript
describe('functionName', () => {
  it('should handle happy path', () => {
    // Arrange: set up test data
    const input = ...;
    
    // Act: call function
    const result = functionUnderTest(input);
    
    // Assert: verify result
    expect(result).toEqual(expected);
  });
  
  it('should handle edge case', () => {
    // Similar structure
  });
  
  it('should throw on invalid input', () => {
    // Assert error thrown
  });
});
```

### Integration Test Template
```typescript
describe('Feature: User Login', () => {
  beforeEach(() => {
    // Set up test database
    seedDatabase();
  });
  
  it('should login user and return token', () => {
    const user = { email: 'test@example.com', password: '...' };
    const result = login(user);
    expect(result.token).toBeDefined();
  });
});
```

## Mocking Standards

### When to Mock
- External APIs (payment processors, email services)
- File system operations
- Database (in unit tests)
- Time/clock (for scheduling tests)
- Random number generators

### When NOT to Mock
- Core business logic
- Database (in integration tests)
- Actual HTTP endpoints being tested
- Cryptographic functions

## Examples to Cover

**Backend**:
- Valid input → correct response
- Invalid input → error handling
- Database constraints → proper errors
- Authorization failures → 403 forbidden
- Missing data → clear error message
- Concurrent operations → no race condition

**Frontend**:
- Component renders with props
- User interactions (click, input, submit)
- Loading state displayed
- Error state displayed
- Empty state handled
- Data updates correctly
- Navigation works

## Example Interaction

> Generate comprehensive tests for this authentication service:
>
> [src/services/auth.ts](src/services/auth.ts)
>
> Based on spec:
> - [docs/features/auth/spec.md](../../docs/features/auth/spec.md)
>
> Include:
> - Unit tests for each function
> - Integration test for full login workflow
> - Error cases (invalid password, user not found, etc.)
> - Edge cases (null inputs, malformed tokens, etc.)
>
> Target 85% coverage

## Output Checklist

- [ ] Unit tests written for all functions
- [ ] Integration tests for workflows
- [ ] All edge cases covered
- [ ] Error conditions tested
- [ ] Mocks used appropriately
- [ ] Test names clear and descriptive
- [ ] Coverage report ≥ 80%
- [ ] All tests pass
- [ ] No skipped tests
- [ ] Ready to commit

---

**Prompt Version**: 1.0  
**Last Updated**: February 23, 2026
