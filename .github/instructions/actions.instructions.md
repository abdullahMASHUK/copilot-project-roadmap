# DevOps / Actions Instructions

This document guides automated deployment, testing, and infrastructure work. It covers GitHub Actions workflows, CI/CD pipelines, and infrastructure-as-code patterns.

## Context References

- **Architecture**: [docs/_global/context.md](../../docs/_global/context.md) — Infrastructure overview, deployment targets
- **Memory**: [docs/domains/backend/memory.md](../../docs/domains/backend/memory.md) — Deployment gotchas, outage history
- **Memory**: [docs/domains/frontend/memory.md](../../docs/domains/frontend/memory.md) — Build optimization notes

## Workflow Standards

### GitHub Actions

**Location**: `.github/workflows/*.yml`

**Naming Convention**:
- `test.yml` — Run tests on push/PR
- `deploy-staging.yml` — Deploy to staging environment
- `deploy-production.yml` — Deploy to production (manual trigger recommended)
- `security-scan.yml` — Run security checks

**Best Practices**:
- Pin action versions (e.g., `action-name@v1.2.3` not `@latest`)
- Use environment variables for configuration
- Log meaningful messages for debugging
- Add conditional steps (only run on certain branches)
- Use secrets for sensitive data (API keys, tokens, etc.)

### Triggers

**On Every Commit (PR)**: Tests, linting, build validation
**On Push to Main**: Staging deployment (optional)
**Manual Trigger**: Production deployments (require approval)
**Scheduled**: Nightly tests, snapshots, cleanup jobs

## Common Tasks

### Creating a New Workflow

1. Use template structure from existing workflows
2. Define trigger events (push, pull_request, schedule, workflow_dispatch)
3. Set up matrix strategies for multiple Node versions / OS
4. Include clear logging and failure notifications
5. Store as `.github/workflows/{purpose}.yml`

### Adding a Deployment Step

1. Authenticate with credentials (stored in GitHub Secrets)
2. Build application (depends on framework)
3. Run tests to prevent broken deployments
4. Upload artifacts if needed
5. Deploy to target environment
6. Run smoke tests post-deployment
7. Notify team of success/failure

### Debugging a Failed Workflow

1. Check logs in GitHub Actions UI
2. Review recent commits that might have triggered failure
3. Verify environment variables and secrets are accessible
4. Test locally using act (local GitHub Actions runner)
5. Add debugging output as needed
6. Update `docs/domains/backend/memory.md` with findings

## Optimization

### Build Speed
- Cache dependencies (npm cache, Docker layers, etc.)
- Use matrix builds to parallelize tests
- Skip expensive steps on certain conditions
- Reference optimization notes in `docs/domains/{backend|frontend}/memory.md`

### Cost Management
- Use runners that match your workload (standard, GPU, etc.)
- Cancel in-progress workflows when new push happens
- Avoid redundant jobs (reuse artifacts)
- Clean up old artifacts

## Security

### Secrets Management
- Store sensitive data in GitHub Secrets (never in code)
- Reference: `${{ secrets.SECRET_NAME }}`
- Limit secrets access to specific workflows
- Rotate credentials regularly

### Dependency Security
- Use `dependabot` for automated dependency updates
- Review and test updates before merging
- Scan for vulnerabilities in CI/CD

### Permissions
- Use least-privilege execution
- Token scopes should be minimal
- Separate read-only and write access

## Infrastructure as Code

### Configuration
- Infrastructure defined in version control
- Changes go through PR review process
- Recreate environments from code (disaster recovery)

### Versioning
- Tag stable releases
- Document breaking changes
- Maintain changelog

## Monitoring & Alerting

### Post-Deployment Checks
- Run health checks against deployed app
- Monitor key metrics (response time, error rate, etc.)
- Alert team on failures

### Logging
- Centralize logs for easy debugging
- Include timestamps, error codes, stack traces
- Make logs searchable and retainable

## Common Environment Variables

(To be defined in context.md)

Example structure:
```yaml
env:
  REGISTRY: ghcr.io
  IMAGE_NAME: ${{ github.repository }}
  NODE_VERSION: 18.x
```

## Emergency Procedures

**Deployment Failed**:
1. Revert to last known good version
2. Investigate root cause in logs
3. Document in memory file
4. Fix and redeploy

**Production Outage**:
1. Immediate rollback if possible
2. Post-incident review document
3. Update docs with learnings
4. Plan preventive measures

## Anti-Patterns to Avoid

❌ **DON'T**:
- Hardcode secrets in workflows
- Use `@latest` for action versions
- Deploy without running tests
- Ignore workflow failures silently
- Manually deploy when automation exists
- Skip monitoring after deployment

✅ **DO**:
- Use GitHub Secrets for sensitive data
- Pin action versions explicitly
- Gate production deployments with tests
- Alert team on failures
- Automate everything possible
- Monitor deployed applications

## Questions?

- **How to add a new deployment target**: Reference existing deployment workflows
- **Workflow failing**: Check logs + recent commits + memory.md
- **Security concern**: Review secrets management + GitHub permission settings
- **Performance issue**: Check cache configuration + job parallelization

---

**Instructions Version**: 1.0  
**Last Updated**: February 23, 2026  
**Applies To**: DevOps engineers, Infrastructure specialists, Tech leads
