# Token Efficiency Guide

**Why This Matters**: Minimal context = Better code quality + Lower costs. This structure prevents context bloat.

---

## The Problem

| Issue | Impact |
|---|---|
| Loading all context (50-100KB) | Agents confused, lower quality output |
| All 9 agent profiles loaded | 3600 lines when you need 400 |
| Full feature history in memory | 200KB instead of 1KB lessons |
| No selective loading | Every task wastes tokens |

---

## The Solution: Our Hierarchical Structure

### Load Only What You Need

```
Global Context (5KB - always relevant)
    ↓
Domain Context (3KB - if in that domain)
    ↓
Project Context (2KB - if on that project)
    ↓
Feature/Memory (1KB - specific to feature)
```

**Instead of**: Load all 100KB every time  
**Do**: Load only 5-13KB relevant context

### Example: Backend Agent Task

```
❌ INEFFICIENT (all context):
  Load: Global + Domain + Project + All agents + All memory + Everything
  = ~150KB tokens
  
✅ EFFICIENT (selective):
  Load: Global (5KB) + Backend domain (2KB) + Path instructions (1KB) + Backend agent (400KB) + Feature memory (1KB)
  = ~10KB tokens
  
Result: 93% token reduction, BETTER code quality
```

---

## How Our Files Support This

| Component | Token Savings |
|---|---|
| **Hierarchy** (Global→Domain→Project) | Only load relevant layers |
| **Memory files** | 1KB lessons vs 200KB full history |
| **Path instructions** | Load only your path (`components.instructions.md` not all paths) |
| **Focused domains** | Backend context separate from Frontend context |
| **Registry system** | Load 1 agent (400KB) not all 9 (3600KB) |
| **Selection guide** | Choose agent early, load minimally |

---

## Loading Best Practices

### For Backend Agent

```
Load:
  ✅ Global context (architecture, tech stack)
  ✅ Backend domain context (patterns, standards)
  ✅ Backend instructions
  ✅ Paths: api.instructions.md, services.instructions.md
  ✅ Backend agent profile
  ✅ Feature spec
  ✅ Backend memory (lessons learned)
  
Skip:
  ❌ Frontend context
  ❌ Frontend instructions
  ❌ All 9 agents (load just backend-api-specialist)
  ❌ Orchestration docs (if using role-based)
```

### For Frontend Agent

```
Load:
  ✅ Global context
  ✅ Frontend domain context
  ✅ Frontend instructions
  ✅ Paths: components.instructions.md, testing.instructions.md
  ✅ Frontend agent profile
  ✅ Feature spec
  ✅ Frontend memory
  
Skip:
  ❌ Backend context
  ❌ Backend instructions
  ❌ All 9 agents
  ❌ Database/data analyst docs
```

---

## Token Budget by Phase

### Typical Feature: Payment Processing

| Phase | Agent | Context | Tokens | Note |
|---|---|---|---|---|
| Planning | ProjectManager | Global + Feature spec | 8KB | Minimal context |
| Design | Architect | Global + Domain + Decisions | 10KB | Design thoughts |
| API | APIDesigner | Global + API path instructions + Agent profile | 8KB | Focused on OpenAPI |
| Backend | Backend Specialist | Global + Backend domain + Backend instructions + Agent | 13KB | Only backend |
| Frontend | Frontend Specialist | Global + Frontend domain + Frontend instructions + Agent | 13KB | Only frontend |
| Data | DataAnalyst | Global + Database instructions + Agent | 10KB | Only data |
| Security | SecurityExpert | Global + Memory (security only) + Code written so far | 12KB | Audit context |
| Docs | Documentation | Global + API spec + Code written + Agent | 10KB | Final docs |
| QA | ReviewQA | Global + Spec + Full implementation + Agent | 15KB | Complete review |

**Total**: ~99KB for entire feature development (vs 500KB+ if loading everything each phase)

---

## Archive Strategy

Keep projects lean by archiving:

### Archive Conditions
- Features shipped >6 months ago
- Memory entries >1 year old  
- Specs for deprecated patterns
- Old context that's no longer relevant

### Archive Locations
```
docs/
  features/
    current-features/    # Load these
    archive/
      2025/             # Shipped feature from 2025
      2024/             # Even older
```

---

## When to Load Full Context (Exceptions)

| Scenario | Why | Context Size |
|---|---|---|
| Entire codebase audit | Security comprehensive review | 50KB (OK, necessary) |
| Major refactoring | Need to understand all affected parts | 40KB (OK, few times/year) |
| Architecture redesign | System-wide rethinking | 60KB (OK, rare) |

**Key**: These are EXCEPTIONS, not default behavior

---

## Checklist: Before Asking Agent for Help

- [ ] Loaded global context? (Yes, once per session)
- [ ] Loaded only relevant domain? (No extra domains)
- [ ] Loaded only relevant agent(s)? (Not all 9)
- [ ] Pruned old memory entries? (Keep last ~5)
- [ ] Feature spec is clear? (Minimal context needed)
- [ ] Unnecessary files excluded? (Archive old stuff)

---

## Tools for Token Efficiency

### Check What You're Loading
```bash
# Count lines in context you're loading
wc -l docs/_global/context.md         # Global
wc -l docs/domains/backend/context.md # Domain
wc -l .github/agents/roles/... # Agent profiles
```

### Estimate Tokens
- Standard: ~4 tokens per word, ~1.3 words per line
- Rough: ~5-6 tokens per line of documentation

---

## Real Numbers

### Payment Feature Build

**Naive approach**: Load everything
```
Global context: 5KB × 6KB/token = 30 tokens
Domain contexts (all 2): 6KB × 6KB/token = 36 tokens
All 9 agents: 3.6KB × 6KB/token = 216 tokens
All memory: 10KB × 6KB/token = 60 tokens
... × 8 phases = MASSIVE token waste
```

**Optimized approach**: Selective loading
```
Per agent: 10-15KB × 6 tokens/KB = 60-90 tokens
× 8 agents = 480-720 tokens (instead of 2000+)
```

---

## Key Takeaways

✅ **Load selectively** — Only what the agent needs  
✅ **Use hierarchy** — Don't skip levels  
✅ **Archive regularly** — Keep active context small  
✅ **Choose agents early** — Load minimal context  
✅ **Memory is lessons** — Not history dump  

**Result**: 70-80% token savings + Better code quality

---

**For More**:
- [Project Structure Guide](PROJECT-STRUCTURE-GUIDE.md) — How to organize files
- [Copilot Instructions](.../../.github/copilot-instructions.md) — Coding guidelines

**Last Updated**: Feb 23, 2026
