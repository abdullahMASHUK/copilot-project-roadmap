# Prompt: security-review

**Purpose**: Audit code for vulnerabilities, security flaws, and best practice violations.

**When to Use**:
- Before merging security-sensitive features
- Reviewing authentication/authorization code
- Handling user input or sensitive data
- API security concerns
- Dependency vulnerability assessment

## Usage

Include this in your Copilot chat with the relevant code:

```
@copilot security-review

Code location: [path/to/file.ts](path/to/file.ts)
Context: [docs/domains/backend/context.md](../../docs/domains/backend/context.md)
Previous security issues: [docs/domains/backend/memory.md](../../docs/domains/backend/memory.md)
```

## Review Areas

### Input Validation
- [ ] All user inputs are validated
- [ ] Type checking enforced
- [ ] Length/range limits applied
- [ ] Dangerous characters escaped

### Authentication & Authorization
- [ ] Passwords hashed securely (bcrypt, scrypt, Argon2)
- [ ] Sessions managed properly
- [ ] Token expiration implemented
- [ ] Permission checks on all endpoints
- [ ] No hardcoded credentials

### Data Protection
- [ ] Sensitive data encrypted (PII, payment info, API keys)
- [ ] HTTPS enforced
- [ ] No logging of sensitive data
- [ ] Proper secrets management
- [ ] Data access controlled

### Database Security
- [ ] SQL injection prevention (parameterized queries)
- [ ] No unnecessary data exposure
- [ ] Row-level security enforced
- [ ] Backups secured

### API Security
- [ ] Rate limiting implemented
- [ ] CORS properly configured
- [ ] No information leakage in errors
- [ ] API versioning considered
- [ ] Throttling for expensive operations

### Dependency Security
- [ ] No known vulnerabilities
- [ ] Dependencies up-to-date
- [ ] Supply chain risks assessed
- [ ] Minimal dependency footprint

### Code Security
- [ ] No eval() or dynamic code execution
- [ ] No hardcoded secrets
- [ ] Error messages don't expose internals
- [ ] Proper exception handling
- [ ] XSS prevention (frontend)

## Output Checklist

- [ ] Vulnerabilities identified and categorized (Critical/High/Medium/Low)
- [ ] Fixes suggested for each issue
- [ ] False positives noted
- [ ] Test cases for security scenarios provided
- [ ] Documentation updated with security notes
- [ ] Compliance requirements verified

## Example Interaction

> I'm adding password reset functionality. Please review for security issues:
>
> [src/auth/password-reset.ts](src/auth/password-reset.ts)
>
> Reference our security patterns:
> - [docs/domains/backend/context.md](../../docs/domains/backend/context.md)
> - [docs/domains/backend/memory.md](../../docs/domains/backend/memory.md#security)
>
> Focus on: authentication tokens, password hashing, email verification tokens.

## Integration with implement-from-spec

Run **security-review** after **implement-from-spec** for high-risk features:

1. Build with implement-from-spec
2. Security review the result
3. Fix any issues found
4. Document lessons in memory.md

---

**Prompt Version**: 1.0  
**Last Updated**: February 23, 2026
