# Project Structure & File Types Guide

**For humans learning how to work with this Copilot-enabled project**

This guide explains all the different kinds of files in this project, what they're for, and how to properly create or update them. Use this reference when you're unsure about where something should go or how to maintain it.

---

## Quick Overview: File Types at a Glance

| File Type | Location | Purpose | When to Create | When to Update |
|-----------|----------|---------|----------------|----------------|
| **Instructions** | `.github/instructions/` | Tell Copilot HOW to code in a specific domain | New domain/role | Best practices change |
| **Context** | `docs/*/context.md` | Background info: tech stack, architecture, conventions | New domain/project | Architecture changes |
| **Memory** | `docs/*/memory.md` | Lessons learned: decisions, patterns, gotchas | After first implementation | After each new learning |
| **Agent** | `.github/agents/roles/**/*.agent.md` | Define specialized agent: role, responsibilities, process | New specialist role | Never (immutable) |
| **Templates** | `docs/templates/` & `.github/templates/` | Starting points for specs, features, issues | New pattern needed | When pattern evolves |
| **Feature Spec** | `docs/features/*/` | Complete specification for a feature | Starting new feature | During implementation |
| **Guide** | `docs/guides/` | How-to for humans: workflows, communication, structure | New workflow needed | When process changes |
| **Changelog** | `docs/changelog.md` | Record of what's been updated | Initial setup | After each major update |
| **README** | Every directory | Overview of what's in this folder | When creating folder | Quarterly review |

---

## Directory Structure Explained

```
project-root/
│
├── .github/                          ← GitHub and Copilot configuration
│   ├── copilot-instructions.md       ← Main Copilot config (reference point)
│   ├── instructions/                 ← Domain-specific Copilot instructions
│   │   ├── backend.instructions.md
│   │   ├── frontend.instructions.md
│   │   └── actions.instructions.md
│   ├── agents/                       ← Specialized agent definitions
│   │   ├── roles/                    ← Role-based agents (new system!)
│   │   │   ├── README.md
│   │   │   ├── AGENT-REGISTRY.md
│   │   │   ├── planning/
│   │   │   ├── implementation/
│   │   │   └── support/
│   │   └── orchestration/            ← Old orchestration system
│   └── templates/                    ← GitHub issue/PR templates
│
├── docs/                             ← Documentation & context
│   ├── guides/                       ← How-to guides for humans
│   │   ├── COMMUNICATION-GUIDE.md    ← How to communicate with Copilot
│   │   ├── PROJECT-STRUCTURE-GUIDE.md ← You are here!
│   │   └── AGENT-REFERENCE.md        ← Quick agent reference
│   ├── _global/                      ← Global project context
│   │   ├── context.md                ← Global architecture & tech stack
│   │   └── memory.md                 ← Global decisions & patterns
│   ├── domains/                      ← Domain-specific context
│   │   ├── backend/
│   │   │   ├── context.md            ← Backend tech stack, conventions
│   │   │   └── memory.md             ← Backend learned patterns
│   │   └── frontend/
│   │       ├── context.md
│   │       └── memory.md
│   ├── projects/                     ← Project-specific context
│   │   ├── backend-api/
│   │   │   ├── context.md
│   │   │   └── memory.md
│   │   └── frontend-web/
│   │       ├── context.md
│   │       └── memory.md
│   ├── features/                     ← Feature specifications
│   │   ├── user-authentication/
│   │   │   └── spec.md
│   │   └── payment-processing/
│   │       └── spec.md
│   ├── templates/                    ← Document templates
│   │   ├── feature-spec.md
│   │   ├── instruction-template.md
│   │   └── context-template.md
│   ├── changelog.md                  ← What's changed in the project
│   └── README.md                     ← Docs directory overview
│
└── src/                              ← Your actual code
```

---

## File Type Details

### 1. INSTRUCTIONS Files (`.github/instructions/`)

**What**: Copilot coding rules and conventions for a specific domain (backend, frontend, etc)

**Location**: `.github/instructions/{domain}.instructions.md`

**Purpose**: 
- Tell Copilot how to write code in this domain
- Document coding conventions and best practices
- Prevent common mistakes
- Keep code consistent

**When to Create**:
- New domain or technology area added to project
- New programming language
- New framework being used

**When to Update**:
- Best practices change
- Team discovers new patterns
- New frameworks adopted

**What's Inside** (Example structure):
```markdown
# Backend Instructions

## Code Style
- Use TypeScript for all backend code
- Use Express.js for HTTP servers
- Follow Airbnb JavaScript style guide

## Architecture Patterns
- Use service layer pattern
- Dependency injection for services
- Repository pattern for data access

## Best Practices
- Always validate input
- Use environment variables for config
- Log all errors with context

## Common Mistakes
- Don't hardcode secrets
- Don't query database in routes
- Don't catch and ignore errors
```

**Example Files**:
- [`.github/instructions/backend.instructions.md`](.github/instructions/backend.instructions.md)
- [`.github/instructions/frontend.instructions.md`](.github/instructions/frontend.instructions.md)

**How Copilot Uses It**:
When building something in backend, Copilot reads this file to understand conventions and apply them consistently.

---

### 2. CONTEXT Files (`docs/*/context.md`)

**What**: Background information about a domain or project (what, why, tech stack, architecture)

**Location**: 
- Global: `docs/_global/context.md`
- Domain: `docs/domains/{domain}/context.md`
- Project: `docs/projects/{project}/context.md`

**Purpose**:
- Explain the system architecture
- Document technology choices and reasoning
- Record current setup and dependencies
- Provide context for decision-making

**When to Create**:
- New project started
- New domain added
- Major architecture is defined

**When to Update**:
- Architecture changes
- New technology adopted
- Major refactoring completed
- Quarterly review for accuracy

**What's Inside** (Example structure):
```markdown
# Backend Context

## Architecture Overview
- Monolithic Express backend
- PostgreSQL database
- Redis for caching

## Technology Stack
- Runtime: Node.js 18+
- Framework: Express.js 4.18
- Language: TypeScript 4.9
- Database: PostgreSQL 14
- Cache: Redis 7

## Design Decisions
- Why Express?: Lightweight, fast, great middleware ecosystem
- Why TypeScript?: Type safety, better IDE support
- Why PostgreSQL?: ACID compliance, complex queries

## Key Systems
1. Authentication: JWT tokens, httpOnly cookies
2. API: RESTful, versioned
3. Database: Migrations managed by Knex

## Conventions
- Services layer for business logic
- Middleware for cross-cutting concerns
- Error codes instead of HTTP status codes
```

**Example Files**:
- [`docs/_global/context.md`](docs/_global/context.md)
- [`docs/domains/backend/context.md`](docs/domains/backend/context.md)

**How Copilot Uses It**:
When implementing a feature, Copilot reads context to understand the system architecture and make consistent decisions.

---

### 3. MEMORY Files (`docs/*/memory.md`)

**What**: Lessons learned, important decisions, patterns, and gotchas specific to a domain

**Location**:
- Global: `docs/_global/memory.md`
- Domain: `docs/domains/{domain}/memory.md`
- Project: `docs/projects/{project}/memory.md`

**Purpose**:
- Record decisions made and WHY
- Capture lessons learned from mistakes
- Document patterns that work well
- Flag known issues and workarounds

**When to Create**:
- First time introducing memory system for domain
- Project learns major lessons

**When to Update**:
- After each feature implementation (add learnings)
- After fixing bugs (add what we learned)
- After making architectural decisions (add context)

**What's Inside** (Example structure):
```markdown
# Backend Memory

## Decisions Made

### Decision 1: Using JWT for Auth (Date)
- Why: Stateless, scalable, no session storage
- Alternative Considered: Sessions in Redis
- Trade-off: Less flexible but more scalable

### Decision 2: Service Layer Architecture
- Why: Testable, reusable, separation of concerns
- Lesson: Don't put business logic in routes
- Pattern: Routes → Services → Database

## Lessons Learned

### Lesson 1: Always Validate Input
- Issue: Bug from unexpected input format
- Fix: Added validation middleware
- Takeaway: Validate early, validate often

### Lesson 2: Never Trust external APIs
- Issue: Stripe timeout caused app crash
- Fix: Added timeout handling and retries
- Takeaway: All external calls need fallback

## Patterns That Work

### Pattern 1: Error Code Objects
- Use codes (card_declined) not HTTP status
- Separate error codes from status codes
- Frontend can handle by code

### Pattern 2: Idempotency Keys
- Payment endpoints use idempotency keys
- Prevents duplicate charges on retry
- Add to all state-changing endpoints

## Known Issues & Workarounds

### Issue 1: Stripe Webhook Race Condition
- Problem: Webhook arrives before charge completes
- Workaround: Add small delay before checking Stripe API
- Epic planned to fix: See ISSUE #234

### Issue 2: PostgreSQL Connection Pooling
- Problem: Pool exhaustion during high load
- Workaround: Reduce pool size, add timeouts
- Permanent fix: Migrate to connection pooler
```

**Example Files**:
- [`docs/_global/memory.md`](docs/_global/memory.md)

**How Copilot Uses It**:
When implementing a feature, Copilot reads memory to avoid known pitfalls and apply proven patterns.

---

### 4. AGENT Files (`.github/agents/roles/*.agent.md`)

**What**: Complete definition of a specialized agent: their role, responsibilities, process, and examples

**Location**: `.github/agents/roles/{phase}/{agent-name}.agent.md`

**Purpose**:
- Define agent expertise and responsibilities
- Specify input format expected
- Specify output format delivered
- Provide step-by-step process
- Document success criteria

**When to Create**:
- New specialist role identified (e.g., new expert needed)
- Current agents don't cover new domain

**When to Update**:
- ❌ **Never** - Agents are immutable once created (versioning happens via registry)

**What's Inside** (Complete structure):
```markdown
# {Agent Name} Agent

## Agent Profile
- Title, specialty, phase, timeline
- Input/output specifications
- When needed, when optional

## Responsibilities
- 5-7 key responsibilities

## Input Format
- What this agent receives from previous agent

## Output Format
- What this agent delivers to next agent

## 7-Step Process
- Detailed step-by-step process agent follows

## Quality Checklist
- What agent validates before handoff

## Common Patterns
- Reusable snippets and examples

## Success Criteria
- How to know agent completed successfully

## Example
- Real-world example (e.g., Payment feature)
```

**Example Files**:
- [`.github/agents/roles/planning/project-manager.agent.md`](.github/agents/roles/planning/project-manager.agent.md)
- [`.github/agents/roles/implementation/backend-api-specialist.agent.md`](.github/agents/roles/implementation/backend-api-specialist.agent.md)

**How Copilot Uses It**:
Copilot reads these agents to understand how to perform specialized work (designing APIs, writing secure code, etc).

---

### 5. REGISTRY & SELECTION Files

**What**: Master index and selection framework for all agents

**Location**: `.github/agents/roles/AGENT-REGISTRY.md` and `.github/agents/roles/AGENT-SELECTION-GUIDE.md`

**Purpose**:
- **Registry**: List all available agents and their capabilities
- **Guide**: Help Copilot intelligently select which agents needed for a feature

**When to Create**:
- When first establishing agent system
- Once in project lifecycle

**When to Update**:
- When new agent added
- When agent responsibility changes

**What's Inside**:

**AGENT-REGISTRY.md**:
```markdown
# All 9 agents
- Who they are
- What they do
- Timeline
- When to use them
```

**AGENT-SELECTION-GUIDE.md**:
```markdown
# Decision Framework
- Feature type → agent mapping
- Execution sequences
- Parallelization rules
```

**Example Files**:
- [`.github/agents/roles/AGENT-REGISTRY.md`](.github/agents/roles/AGENT-REGISTRY.md)
- [`.github/agents/roles/AGENT-SELECTION-GUIDE.md`](.github/agents/roles/AGENT-SELECTION-GUIDE.md)

---

### 6. HANDOFF PROTOCOL File

**What**: Definition of how agents hand off work to each other sequentially

**Location**: `.github/agents/roles/HANDOFF-PROTOCOL.md`

**Purpose**:
- Define validation gates between agents
- Specify what gets passed at each handoff
- Document communication format
- Define escalation paths

**When to Create**:
- When establishing multi-agent workflow

**When to Update**:
- When adding new agent
- When workflow changes

**What's Inside**:
```markdown
# 9 Handoff Points
- Project Manager → Architect
- Architect → API Designer
- API Designer → Backend Specialist
- ... (6 more)

# Each handoff includes:
- What gets passed
- Validation checks
- Communication template
- What to do if validation fails
```

**Example File**:
- [`.github/agents/roles/HANDOFF-PROTOCOL.md`](.github/agents/roles/HANDOFF-PROTOCOL.md)

---

### 7. TEMPLATE Files (`docs/templates/` & `.github/templates/`)

**What**: Starting point documents for creating new specifications, features, or issues

**Location**: 
- Document templates: `docs/templates/`
- GitHub templates: `.github/templates/`

**Purpose**:
- Ensure consistency across specs
- Provide structure so nothing is missed
- Speed up creation of new things
- Capture best practices

**When to Create**:
- New pattern discovered that will repeat
- Team wants to standardize something
- New type of artifact that needs consistent format

**When to Update**:
- Pattern evolves
- Example in template becomes outdated
- New best practices learned

**What's Inside** (Example):
```markdown
# Feature Specification Template

## Feature Name
## Overview
## Requirements (Functional & Non-functional)
## Acceptance Criteria
## Dependencies
## Timeline Estimate
## Open Questions
```

**Example Files**:
- `.github/templates/feature-spec.md`
- `.github/templates/instruction-template.md`

**How to Use**:
1. Copy template
2. Fill in blanks
3. Follow the structure
4. Delete any sections not needed

---

### 8. FEATURE SPECIFICATION Files (`docs/features/*/spec.md`)

**What**: Complete specification for a single feature (requirements, design, acceptance criteria)

**Location**: `docs/features/{feature-name}/spec.md`

**Purpose**:
- Document what a feature should do
- Communicate requirements clearly
- Capture acceptance criteria
- Reference for implementation

**When to Create**:
- Starting work on significant feature
- Feature complex enough to need planning
- Stakeholders need sign-off

**When to Update**:
- Requirements change
- Scope clarified
- New acceptance criteria discovered

**What's Inside** (Example):
```markdown
# Feature: Payment Processing

## Overview
Users can pay with credit cards

## Requirements
- Support Visa, Mastercard, AmEx
- Process refunds
- Handle failures gracefully
- Maintain audit trail

## Acceptance Criteria
- [ ] User can enter card details
- [ ] Payment succeeds with response
- [ ] Failed payment shows error
- [ ] Refund processes within 24 hours

## Design
- Use Stripe for payment processing
- Webhook handler for payment status

## Timeline
- 14-16 days for full feature
```

**Example Files**:
- `docs/features/user-authentication/spec.md`

---

### 9. GUIDE Files (`docs/guides/`)

**What**: How-to guides for humans learning to work with this project

**Location**: `docs/guides/{guide-name}.md`

**Purpose**:
- Help humans understand workflows
- Explain how to communicate with Copilot
- Document processes and patterns
- Reference for new team members

**When to Create**:
- New workflow established
- Team discovers better way to work
- New team members need onboarding

**When to Update**:
- Process changes
- Better examples found
- Quarterly review

**What's Inside** (Example):
```markdown
# Steps to Implement a Feature

## Step 1: Define Requirements
## Step 2: Create Feature Spec
## Step 3: Select Agents
## Step 4: Execute with Copilot
## Step 5: Review & Merge
```

**Example Files**:
- [`docs/guides/COMMUNICATION-GUIDE.md`](docs/guides/COMMUNICATION-GUIDE.md) — How to talk to Copilot
- [`docs/guides/PROJECT-STRUCTURE-GUIDE.md`](docs/guides/PROJECT-STRUCTURE-GUIDE.md) — You are here!

---

### 10. CHANGELOG File (`docs/changelog.md`)

**What**: Record of what's been updated in project structure, docs, and processes

**Location**: `docs/changelog.md`

**Purpose**:
- Track what's changed
- Show what's new to team
- Reference for "what was updated when"
- Onboarding reference

**When to Create**:
- Initial setup of project
- First time establishing changelog practice

**When to Update**:
- After major update (new agent, new context, new guide)
- Monthly summary
- When team needs to know what changed

**What's Inside** (Example):
```markdown
# Changelog

## February 23, 2026
- Added 9 role-based agents
- Created AGENT-REGISTRY.md
- Created AGENT-SELECTION-GUIDE.md
- Created HANDOFF-PROTOCOL.md

## February 20, 2026
- Moved communication guides to docs/guides/
- Updated copilot-instructions with new links

## January 15, 2026
- Initial infrastructure setup
- Created context files for domains
```

**Example File**:
- [`docs/changelog.md`](docs/changelog.md)

---

### 11. README Files (Every directory)

**What**: Overview of what's in each directory

**Location**: `README.md` in each folder that seems complex

**Purpose**:
- Explain what this folder contains
- Guide to files in this folder
- Quick reference for folder purpose

**When to Create**:
- New major directory created
- Directory has multiple files that need explaining

**When to Update**:
- New file added to directory
- Purpose of directory changes
- Quarterly review

**What's Inside** (Example):
```markdown
# Backend Systems

This directory contains all backend code and documentation.

## Directories
- `/src` - Source code
- `/tests` - Test files
- `/migrations` - Database migrations

## Important Files
- `package.json` - Dependencies
- `tsconfig.json` - TypeScript config

## Getting Started
- Run `npm install`
- Run `npm run dev`
```

---

## Relationship Between File Types

Here's how these files work together:

```
User has a feature idea
            ↓
Writes FEATURE SPEC (docs/features/)
            ↓
Copilot reads COPILOT-INSTRUCTIONS to know HOW to code
Copilot reads CONTEXT to understand WHAT to build
Copilot reads MEMORY to avoid KNOWN PITFALLS
            ↓
Copilot reads AGENT-REGISTRY & AGENT-SELECTION-GUIDE
Copilot picks appropriate AGENTS
            ↓
Each AGENT executes their role per their AGENT FILE
Agents follow HANDOFF-PROTOCOL to pass work
            ↓
Feature complete with code + tests + docs + security
            ↓
Update MEMORY with lessons learned
Update CHANGELOG with what happened
```

---

## How to Maintain This Project

### Weekly Tasks (Quick)
- Review CHANGELOG
- Check if MEMORY needs updates after features complete

### Monthly Tasks (Medium)
- Update CONTEXT if architecture changed
- Review CHANELOG monthly summary
- Update INSTRUCTIONS if new patterns discovered

### Quarterly Tasks (Comprehensive)
- Full review of all CONTEXT files for accuracy
- Update MEMORY with all learned lessons
- Review all AGENT definitions for relevance
- Update GUIDES based on feedback

### After Each Feature (Required)
- Update MEMORY with lessons learned
- Add entry to CHANGELOG
- Update CONTEXT if architecture changed
- Update INSTRUCTIONS if new patterns discovered

---

## Quick Reference: Where to Find Things

| I'm looking for... | Look here |
|--------------------|-----------|
| How to code in backend | `.github/instructions/backend.instructions.md` |
| What's our tech stack | `docs/_global/context.md` or `docs/domains/backend/context.md` |
| What have we learned | `docs/_global/memory.md` or domain memory files |
| What agents are available | `.github/agents/roles/AGENT-REGISTRY.md` |
| How to pick agents | `.github/agents/roles/AGENT-SELECTION-GUIDE.md` |
| How agents hand off work | `.github/agents/roles/HANDOFF-PROTOCOL.md` |
| What a specific agent does | `.github/agents/roles/{phase}/{agent-name}.agent.md` |
| How to communicate with Copilot | `docs/guides/COMMUNICATION-GUIDE.md` |
| How to structure documents | `docs/templates/` |
| What's been updated recently | `docs/changelog.md` |
| How to get started | `docs/guides/` and `README.md` files |

---

## Best Practices for File Management

### ✅ DO

- **Keep CONTEXT updated** when architecture changes
- **Add to MEMORY** after learning important lessons
- **Update CHANGELOG** when major things change
- **Use INSTRUCTIONS** to keep code consistent
- **Follow TEMPLATES** when creating new documents
- **Document decisions** in MEMORY when making important choices
- **Keep files close to code** they describe (backend context near backend code)
- **Review quarterly** to ensure files are still accurate

### ❌ DON'T

- **Don't hardcode** decisions—document WHY in MEMORY
- **Don't leave failing** AGENT files or TEMPLATES
- **Don't update CHANGELOG** for every small change (monthly is fine)
- **Don't create** files without clear purpose
- **Don't ignore** CONTEXT when implementing (it has important patterns)
- **Don't skip** updating MEMORY (future you will need it)
- **Don't let CONTEXT** get outdated (review quarterly)

---

## Creating New Files the Right Way

### New Domain? Create:
1. `.github/instructions/{domain}.instructions.md`
2. `docs/domains/{domain}/context.md`
3. `docs/domains/{domain}/memory.md`
4. `docs/domains/{domain}/README.md`

### New Project? Create:
1. `docs/projects/{project}/context.md`
2. `docs/projects/{project}/memory.md`
3. `docs/projects/{project}/README.md`

### New Feature? Create:
1. `docs/features/{feature}/spec.md`
2. Use AGENT system to implement
3. Update MEMORY after completion
4. Update CHANGELOG

### New Agent? Create:
1. `.github/agents/roles/{phase}/{agent-name}.agent.md`
2. Update `AGENT-REGISTRY.md`
3. Update `AGENT-SELECTION-GUIDE.md` if applicable
4. Update `docs/changelog.md`

---

## File Templates to Copy

### New CONTEXT File Template
```markdown
# {Domain/Project} Context

## Overview
## Technology Stack
## Architecture
## Key Systems
## Conventions
## Current Focus
```

### New MEMORY File Template
```markdown
# {Domain/Project} Memory

## Decisions Made
## Lessons Learned
## Patterns That Work
## Known Issues & Workarounds
```

### New INSTRUCTIONS File Template
```markdown
# {Domain} Instructions

## Code Style
## Architecture Patterns
## Best Practices
## Common Mistakes
## When to Ask for Help
```

All templates available in: `docs/templates/`

---

## Getting Help

**If you're unsure about:**
- **How to code something** → Read `.github/instructions/{domain}.instructions.md`
- **Architecture decisions** → Read `docs/*/context.md`
- **Past mistakes to avoid** → Read `docs/*/memory.md`
- **How agents work** → Read `.github/agents/roles/README.md`
- **How to communicate** → Read `docs/guides/COMMUNICATION-GUIDE.md`
- **What's in project** → Read this file or relevant `README.md`
- **What changed** → Read `docs/changelog.md`

**If file doesn't exist, create it** following this guide and the templates in `docs/templates/`

---

## Summary

This project has **11 main file types**, each serving a specific purpose:

1. **INSTRUCTIONS** — Tell Copilot HOW to code
2. **CONTEXT** — Explain WHAT the system is
3. **MEMORY** — Remember lessons learned
4. **AGENTS** — Define specialized roles
5. **REGISTRY** — Index of all agents
6. **SELECTION GUIDE** —Pick the right agent
7. **HANDOFF PROTOCOL** — Agent workflow
8. **TEMPLATES** — Starting point documents
9. **FEATURE SPECS** — What to build
10. **GUIDES** — How-tos for humans
11. **CHANGELOG** — What changed

**Key principle**: Different files for different purposes = organized, maintainable, understandable project.

---

**Last Updated**: February 23, 2026  
**Guide Version**: 1.0  
**For Questions**: See `docs/guides/COMMUNICATION-GUIDE.md`

