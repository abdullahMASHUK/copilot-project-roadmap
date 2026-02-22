# AI Copilot Project Roadmap

> Multi-specialist agent system for intelligent feature development

---

## 🎯 Pick Your Path (Preference Points)

| Role | Start Here | Time |
|---|---|---|
| 📚 **New Team Member** | [Project Structure Guide](docs/guides/PROJECT-STRUCTURE-GUIDE.md) | 20 min |
| 🔨 **Developer** | [Backend](docs/domains/backend/context.md) or [Frontend](docs/domains/frontend/context.md) + [Instructions](.github/instructions/) | 15 min |
| 🤖 **Using Agents** | [Agent System Overview](.github/agents/roles/README.md) | 10 min |
| 🏗️ **Architecture** | [Planning Agent](.github/agents/plan.agent.md) | 10 min |

---

## 📑 Index (Jump to Section)

| Quick Ref | Purpose |
|---|---|
| [Quick Start](#quick-start-for-humans) | Fresh? Start here |
| [The Exchange](#the-exchange) | What you give, what you get |
| [9 Agents](#how-it-works-9-agents) | Who does what |
| [Directories](#directories) | Where is everything |
| [Guides](#guides-for-humans) | Deep dive docs |
| [File Types](#all-11-file-types) | What each file is for |
| [Token Efficiency](#-token-efficiency-important-for-sustainability) | Keep context minimal |

---

## Quick Start for Humans

New to this project?

1. **[📖 Project Structure Guide](docs/guides/PROJECT-STRUCTURE-GUIDE.md)** — All 11 file types + how they work
2. **[💬 Communication Guide](docs/guides/COMMUNICATION-GUIDE.md)** — How to talk to agents
3. **[🔄 Workflow Diagrams](docs/guides/COMMUNICATION-WORKFLOW.md)** — Visual examples

---

## The Exchange

| **You Provide** | **Agents Deliver** |
|---|---|
| Clear requirements (SRS or spec) | Complete feature (code + tests + docs) |
| Context & constraints | Production-ready implementation |
| Domain knowledge | Security audit & compliance |
| Approval at phases | Quality assured delivable |
| Feedback | Fully documented API |

**Flow**: Write requirements → Agents build end-to-end → You review → Ship

---

## How It Works: 9 Agents

### The Pipeline (14-16 days typical)

```
Your Requirements
    ↓
PROJECT MANAGER (break down tasks)
    ↓
ARCHITECT (design approach)
    ↓
[4 PARALLEL BUILDERS + DATABASE + SECURITY]
  ├─ API Designer (OpenAPI spec)
  ├─ Backend Specialist (Express code)
  ├─ Frontend Specialist (React UI)
  └─ Data Analyst (database)
    ↓
SECURITY EXPERT (audit code)
    ↓
DOCUMENTATION (API docs)
    ↓
REVIEW/QA (verify everything)
    ↓
✅ DONE
```

### Agents by Phase

**Planning (2)**: Project Manager · Architect  
**Implementation (5)**: API Designer · Backend · Frontend · Data Analyst · Security Expert  
**Support (2)**: Documentation · Review/QA  

### Learn More
- [Complete System Guide](.github/agents/roles/README.md)
- [Agent Registry](.github/agents/roles/AGENT-REGISTRY.md)  
- [Which Agent to Use](.github/agents/roles/AGENT-SELECTION-GUIDE.md)
- [How They Handoff](.github/agents/roles/HANDOFF-PROTOCOL.md)

---

## Directories

```
.github/
  agents/
    roles/               ← 9 Agent profiles (USE THIS)
    orchestration/       ← Alternative workflow
  instructions/          ← Coding guidelines
  
docs/
  guides/                ← For humans (structure, workflows, comms)
  _global/               ← Architecture context & decisions
  domains/               ← Backend/Frontend specific
  projects/              ← Project-specific docs
  features/              ← Feature specs
```

---

## Guides for Humans

### For Onboarding
- [Project Structure](docs/guides/PROJECT-STRUCTURE-GUIDE.md) — What each file does
- [Communication](docs/guides/COMMUNICATION-GUIDE.md) — How to talk to agents
- [Workflow Diagrams](docs/guides/COMMUNICATION-WORKFLOW.md) — See examples

### For Development
- **Backend**: [Context](docs/domains/backend/context.md) + [Instructions](.github/instructions/backend.instructions.md)
- **Frontend**: [Context](docs/domains/frontend/context.md) + [Instructions](.github/instructions/frontend.instructions.md)
- **DevOps**: [Instructions](.github/instructions/actions.instructions.md)

### For Using Agents
- [Role-Based System](.github/agents/roles/README.md) — How the 9-agent system works (RECOMMENDED)
- [Orchestration System](.github/agents/orchestration/WORKFLOW.md) — Alternative workflow
- [Planning Agent](.github/agents/plan.agent.md) — For architecture decisions

### For Context & History
- [Global Context](docs/_global/context.md) — Tech stack & architecture
- [Global Memory](docs/_global/memory.md) — Decisions & constraints  
- [Changelog](docs/changelog.md) — Recent updates
- [Architecture Decisions](.github/decisions/) — ADRs

### ⚡ Token Efficiency (Important)
- [Token Efficiency Guide](docs/guides/TOKEN-EFFICIENCY-GUIDE.md) — Minimal context = Better quality

---

## All 11 File Types

| Type | Purpose | Example |
|---|---|---|
| INSTRUCTIONS | How to code | `.github/instructions/backend.instructions.md` |
| CONTEXT | System architecture | `docs/_global/context.md` |
| MEMORY | Lessons learned | `docs/_global/memory.md` |
| AGENT | Specialist role | `.github/agents/roles/.../backend-api-specialist.agent.md` |
| REGISTRY | Agent index | `.github/agents/roles/AGENT-REGISTRY.md` |
| SELECTION | Pick agent | `.github/agents/roles/AGENT-SELECTION-GUIDE.md` |
| HANDOFF | Workflow | `.github/agents/roles/HANDOFF-PROTOCOL.md` |
| TEMPLATE | Doc template | `.github/templates/feature-spec.md` |
| FEATURE SPEC | What to build | `docs/features/{name}/spec.md` |
| GUIDE | How-to | `docs/guides/COMMUNICATION-GUIDE.md` |
| CHANGELOG | What changed | `docs/changelog.md` |

**[Full Guide →](docs/guides/PROJECT-STRUCTURE-GUIDE.md)**

---

## Key Principles

- ✅ **Context-Driven** — Decisions documented & referenced
- ✅ **Explicit** — Patterns written, not assumed  
- ✅ **Hierarchical** — Global → Domain → Project → Feature
- ✅ **Versioned** — All docs in git
- ✅ **Learning** — Memory files updated after each feature

---

## Example: Payment Feature

1. Define requirements (SRS)
2. Project Manager breaks down tasks
3. Architect designs system
4. 4 agents work in parallel:
   - API spec (OpenAPI)
   - Backend routes & services
   - Frontend form & UI
   - Database schema
5. Security audit (PCI compliance)
6. Automatic API documentation
7. Quality verification
8. **Result**: Production-ready feature

---

## ⚡ Token Efficiency (Important for Sustainability)

**Why This Matters**: Our hierarchical structure is designed to minimize token usage while maximizing code quality.

| Naive Approach | Our Approach |
|---|---|
| Load everything (150KB) | Load only what's needed (10-15KB) |
| All 9 agents every time | Select relevant agents |
| Full history in context | Lessons only in memory |
| Lower code quality (confusion) | Higher code quality (focus) |

**Result**: 70-80% token reduction + Better output

**Estimated Savings**: Payment feature development  
- Naive: ~2000 tokens  
- Optimized: ~600 tokens (70% savings)

👉 **[Full Token Efficiency Guide →](docs/guides/TOKEN-EFFICIENCY-GUIDE.md)** — Learn how to keep context minimal and quality high

---

**Status**: Production Ready ✅  
**Version**: 1.0  
**Last Updated**: Feb 23, 2026
