# Features Directory

This directory contains specifications for individual features. Each feature should have its own subdirectory with at least a `spec.md` file.

## Organization

```
features/
├── README.md                    (this file)
├── {feature-name}/
│   ├── spec.md                  (required: feature specification)
│   ├── context.md               (optional: feature-specific decisions)
│   └── memory.md                (optional: feature-specific learnings)
├── {another-feature}/
│   ├── spec.md
│   └── ...
└── ...
```

## Creating a New Feature

1. Create new directory: `features/{feature-name}/`
2. Use the template: [../../.github/templates/feature-spec.md](../../.github/templates/feature-spec.md)
3. Save as `spec.md`
4. Fill in all required sections:
   - Overview & goals
   - User workflows
   - Acceptance criteria
   - API design (if backend)
   - Component design (if frontend)
   - Edge cases & error handling
   - Testing strategy
5. Link the spec from [docs/changelog.md](../changelog.md)

## Tips for Good Specs

✅ **DO**:
- Be specific about behavior and edge cases
- Include acceptance criteria that are testable
- Design APIs/components before coding
- Include error scenarios
- Get approval before implementation starts

❌ **DON'T**:
- Leave acceptance criteria vague
- Skip edge cases
- Assume implementation details
- Mix multiple features in one spec
- Ignore error handling

## Quick Links

- **Template**: [../../.github/templates/feature-spec.md](../../.github/templates/feature-spec.md)
- **Instructions**: [../../.github/COPILOT-USAGE.md](../../.github/COPILOT-USAGE.md)
- **Changelog**: [../changelog.md](../changelog.md)

---

**Last Updated**: February 23, 2026
