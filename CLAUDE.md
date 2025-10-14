# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Overview

**Lean-Clean Methodology** is a pragmatic framework for designing, prototyping, and operationalizing AI-driven systems. It blends Lean Product Development (iterative learning, minimal viable slices) with Clean Architecture (decoupled layers, testable boundaries, replaceable adapters) and Agentic Design (AI-assisted orchestration, observability, human-in-the-loop workflows).

**Current Status:** Living documentation repository (no implementation code yet)

## 🚨 Default Working Mode: Human-in-the-Loop Collaboration

**CRITICAL:** This repository requires a different working mode than typical code repositories.

**📖 Complete Workflow Guide:** See [`/plan/WORKFLOW.md`](plan/WORKFLOW.md) for detailed step-by-step instructions, file naming conventions, examples from practice, and troubleshooting guidance.

### This is a Living Methodology Repository

Unlike code repositories where Claude can make autonomous implementation decisions, this repository is about **defining the methodology itself**. Most work involves architectural decisions, framework design, terminology choices, and pattern definitions that require **intimate human collaboration**.

### Default Mode: Collaborative Decision-Making

**When working in this repository, you MUST:**

1. ❌ **DO NOT make unilateral decisions** about:
   - Architecture patterns and folder structures
   - Naming conventions and terminology
   - Framework definitions and layer responsibilities
   - Design patterns and paradigm choices
   - Any work where "there are multiple ways to do this"

2. ✅ **DO follow this process:**
   - **Identify ALL decisions upfront** before creating any documents or code
   - **Present options with trade-offs** for each decision
   - **Wait for explicit approval** before proceeding with implementation
   - **Document the rationale** for each choice after it's made

3. ✅ **DO use the Human-in-the-Loop template:**
   - Template location: `/plan/templates/hitl-decision-template.md`
   - Copy and customize the template for each major task
   - Follow the Decision Discovery → Present Options → Get Approval → Implement workflow

### Why This Mode is Critical

**Living methodology work is different from code implementation:**

- **Code repos:** "How to implement X?" (often one clear best practice)
- **This repo:** "Should we use pattern X or Y?" (multiple valid approaches, trade-offs matter)

**Mistakes are expensive here:**

- Unilateral architectural choices require rework across multiple documents
- Terminology decisions affect consistency throughout the methodology
- Pattern choices impact downstream framework definitions

**Example from Session 02:**

- Claude created framework with implicit choices about drivers/, gateways/, DTOs
- Required discovering 5 additional decisions after initial work
- Needed rework and split across multiple sessions
- Could have been avoided with upfront decision discovery

### When Autonomous Work is Appropriate

You CAN work autonomously for:
- ✅ Simple documentation formatting fixes
- ✅ Typo corrections and grammar improvements
- ✅ Adding examples to already-defined patterns
- ✅ Updating cross-references between documents
- ✅ Implementing already-decided designs

You CANNOT work autonomously for:
- ❌ Defining new architectural patterns
- ❌ Creating framework folder structures
- ❌ Choosing terminology or naming conventions
- ❌ Resolving conflicts between design approaches
- ❌ Making strategic methodology decisions

### How to Start Tasks in This Repo

**Template for Starting Work:**

```markdown
# Task: [Your Task Name]

## Decision-Making Mode: Human-in-the-Loop ⚠️

[Copy from /plan/templates/hitl-decision-template.md]

## Your First Task: Decision Discovery

Before writing ANY code or creating ANY documents:

1. Analyze the task requirements thoroughly
2. Identify ALL architectural decisions that need to be made
3. Research relevant context (read analysis docs, axioms, prior decisions)
4. Create a decisions document listing every choice point
5. Present the decisions document to me for review
```

**See `/plan/templates/hitl-decision-template.md` for the complete template.**

### Session Continuity

When resuming work across sessions:
- Reference `/plan/templates/session-starter-template.md` (if it exists)
- Load relevant decisions documents from previous sessions
- Review axioms and prior architectural decisions
- Confirm understanding before proceeding

### Quick Reference: Key Workflow Steps

**For detailed workflow, see `/plan/WORKFLOW.md`**

**When starting a task:**

1. ✅ Acknowledge Human-in-the-Loop mode explicitly
2. ✅ Read all documents listed in "Documents to Review"
3. ✅ Create decisions document: `/plan/_[task-name]-decisions-needed.md`
4. ✅ Present ALL decisions upfront before implementing
5. ✅ Wait for user approval on each decision
6. ✅ Document user choices and rationale
7. ✅ Implement only after all decisions resolved

**Key Resources:**

- **Workflow Guide:** `/plan/WORKFLOW.md` - Complete step-by-step process
- **HITL Template:** `/plan/templates/hitl-decision-template.md` - Start here for new tasks
- **Decision Template:** `/plan/templates/decision-document-template.md` - Structure for decisions document
- **Examples:** See `/plan/WORKFLOW.md` Section 6 for real examples from Session 02

**Common Pitfalls to Avoid:**

- ❌ Don't create files before presenting decisions
- ❌ Don't make assumptions about user preferences
- ❌ Don't discover decisions incrementally
- ❌ Don't proceed without explicit approval

See `/plan/WORKFLOW.md` for troubleshooting, anti-patterns, and detailed examples.

### Repository Organization

The repository uses a clear distinction between finalized and working documents:

**`/docs/` - Finalized Documents**

- Stable, approved architectural decisions and principles
- Foundational documents that inform all methodology work
- Examples: `lean-clean-axioms.md` (core architectural principles)
- These documents should be treated as authoritative references
- Changes to these documents require significant justification

**`/plan/` - Working Documents**

- Session notes and work-in-progress decisions
- Experimental ideas and explorations
- Decision documents being developed
- Templates for Human-in-the-Loop workflows
- Structure:
  - `plan/sessions/` - Session documentation files (`_session-*.md`)
  - `plan/drafts/` - Draft analysis and planning documents
  - `plan/templates/` - HITL and session templates
  - `plan/wip/` - Work in progress
  - `plan/archive/` - Historical content

**When to reference which:**

- Start methodology tasks by reading `/docs/` to understand finalized principles
- Document your decision-making process in `/plan/`
- Promote documents from `/plan/` to `/docs/` only after explicit approval

---

## Core Architecture Philosophy

The methodology follows an 8-phase approach (P0-P8) for turning rough requirements into implemented, testable PoCs:

### Phase Structure

- **P0: Context Framing** - Define the "why" and constraints
- **P1: Decomposition** - Break into atomic parts and distill minimal instructions
- **P2: Ideation & Concept Design** - Explore solution options
- **P3: Refinement & Steel Thread** - Define smallest testable slice
- **P4: Reconstitution & Roadmapping** - Map to epics and dependencies
- **P5: Implementation Planning** - Define executable specs (YAML use-case driven)
- **P6: Execution & Iteration** - Build and validate the PoC
- **P7: Reflection & Knowledge Capture** - Document learnings
- **P8: Agentic System Design** - Extend with monitoring, validation, and alerts

### Framework Structure (Finalized - Session 02)

The methodology supports three PoC types with progressive evolution:

**For complete folder structures, code examples, and migration paths, see:**

- [`/docs/framework-folder-structures.md`](docs/framework-folder-structures.md) - Authoritative framework structures for all 3 PoC types
- [`/docs/references/implementations.md`](docs/references/implementations.md) - Analysis of reference implementations

**Quick Reference - Pragmatic CA Structure:**

```
campaign-generator/
├─ app/
│  ├─ entities/                        # Domain models
│  │  └─ campaign.py
│  ├─ use_cases/                       # Business logic
│  │  └─ generate_campaign_uc.py
│  ├─ interface_adapters/              # Orchestrators & Presenters
│  │  ├─ orchestrators/                # Business workflow coordinators
│  │  │  └─ campaign_orchestrator.py
│  │  └─ presenters/                   # Response formatters
│  │     └─ campaign_presenter.py
│  ├─ adapters/                        # External services (OpenAI, S3)
│  │  ├─ imagegen/
│  │  │  ├─ protocol.py
│  │  │  ├─ fake.py                    # Fake for testing
│  │  │  └─ openai.py
│  │  └─ storage/
│  │     ├─ protocol.py
│  │     ├─ fake.py
│  │     └─ s3.py
│  └─ infrastructure/                  # Persistence (Postgres, MongoDB)
│     └─ repositories/
│        └─ campaign/
│           ├─ protocol.py
│           ├─ in_memory.py            # In-memory for testing
│           └─ postgres.py
│
├─ drivers/                            # Entry points (CLI + UI always)
│  ├─ cli/
│  │  └─ commands.py
│  ├─ rest/                            # When enterprise integration needed
│  │  ├─ main.py
│  │  └─ schemas/
│  └─ ui/
│     └─ streamlit/
│        └─ app.py
│
├─ tests/                              # Three-layer test structure
│  ├─ acceptance/                      # Stakeholder contracts (always fakes)
│  ├─ unit/                            # Isolated logic tests
│  └─ integration/                     # Real service tests
│
├─ tools/                              # Development utilities
│  ├─ validate.py
│  └─ init_db.py
│
├─ pyproject.toml
├─ Makefile
└─ docker-compose.yaml
```

**Key Patterns - Finalized in Session 02:**

- **Orchestrators not Controllers** - Stakeholder-friendly terminology (Decision 1)
- **Drivers layer** - CLI + UI always from day 1 (Decision 9: Enterprise PoC Reality)
- **Fakes in production code** - Two types: fake adapters + in-memory repositories (Decision 8)
- **Infrastructure split** - Separate `adapters/` (external services) and `infrastructure/` (persistence) (Decision 10)
- **Outside-In TDD** - Acceptance tests with stakeholders → fakes → real adapters (Decision 7)
- **Progressive Evolution** - Steel Thread → Pragmatic CA → Full CA with additive changes (Decision 12)

## Key Principles

1. **Decompose before you build** - Break problems into minimal atomic steps
2. **Design for substitution** - Interfaces and adapters are replaceable
3. **Build a steel thread** - Deliver minimal end-to-end path proving viability
4. **Instrument everything** - Specs, manifests, validation, and telemetry are first-class
5. **Automate learning** - Agents monitor, test, and report; humans approve and refine
6. **Document as you go** - YAML, Markdown, and schema serve as the living spec

## Documentation Syntax Conventions

The methodology uses specific syntax for agent compatibility:

- **Template variables**: `{{variable_name}}` (e.g., `{{project_name}}`, `{{timebox_hours}}`)
- **Structured data blocks**: Fenced YAML/JSON for machine-readable specs
- **Phase delimiters**: Markdown headers (`##`, `###`) for chunk-level parsing
- **Checklists**: `- [ ] Task description` for Boolean extraction and progress tracking
- **Fenced commands**: Triple backticks with language hints (e.g., ```bash)
- **Metadata front matter**: YAML blocks at top of files for versioning and pipeline ingestion

## Python Code Standards

When adding Python code to this repository (examples, scripts, utilities), follow the **Lean-Clean Python Style Guide**.

**Style Guide Location:** `../lean-clean-code/lean-clean-python-style-guide/AGENT_REFERENCE.md`

**When to Apply:**
- Example code in documentation
- Utility scripts in `/tools/`
- Test examples in methodology documentation
- Any Python snippets in Markdown files

**Process:**
1. Before writing Python code, read the style guide
2. Apply conventions consistently across all Python code in this repo
3. Reference the style guide when reviewing code
4. **If you discover a pattern not in the style guide:**
   - Log it using the template in AGENT_REFERENCE.md section "Capturing New Patterns"
   - Save to `/plan/_future-work.md` or current session notes
   - Tag with `[STYLE-GUIDE-ADDITION]`

**Note:** Since this is a documentation repository, code examples should prioritize clarity and teaching value while still following the style guide.

## Key Documentation Files

### Finalized Documents (`/docs/`)

- `lean-clean-axioms.md` - Core architectural principles and foundational decisions
- `framework-folder-structures.md` - Authoritative framework structures for all 3 PoC types (Steel Thread, Pragmatic CA, Full CA)
- `references/implementations.md` - Analysis of reference implementations with Session 02 alignment notes

### Methodology Core

- `README.md` - Complete v2.2 methodology documentation with all 8 phases
- `Lean-Clean-PoC-Playbook-v2-template.md` - Template for creating project-specific playbooks
- `lean-clean-methodology-v1.csv` & `lean-clean-methodology-v2.csv` - Phase tables

### Visual Documentation

- `images/IMAGE_ANALYSIS.md` - Comprehensive analysis of all visual assets
- `images/ca/` - Clean Architecture diagrams (Martin's classic + nikolovlazar's modern implementation)
- `images/acceptance-criteria/` - Given/When/Then and 4Cs framework visualizations
- `images/lpp/` - Lean Product Playbook pyramids (Product-Market Fit + 6-step process)

**Visual Assets Mapped to Methodology Phases**:

- **P0-P2** (Context/Ideation): Use Martin's CA diagram for conceptual design; LPP pyramid for defining MVP scope
- **P3-P5** (Planning): Use nikolovlazar's CA for implementation structure; Given/When/Then for acceptance criteria
- **P6** (Execution): Use nikolovlazar's CA as code organization blueprint
- **P7** (Reflection): Use Martin's CA for stakeholder communication
- **P8** (Agentic): Infrastructure layer pattern enables observability and monitoring hooks

## Technology Stack

### Core
- **Language**: Python (uv for dependency management)
- **CLI**: Typer or argparse
- **API**: FastAPI (optional)
- **Database**: PostgreSQL (approvals, runs, alerts)
- **Vector DB**: Weaviate (optional, for RAG)

### UI Options
- **Streamlit**: Simple dashboard for human-in-loop approvals (port 8501)
- **FastAPI**: REST API for approvals, runs, alerts
- **CLI**: Primary automation surface

### Testing
- **pytest**: Test suite framework

**Note:** Technology choices documented in the methodology are recommendations. Actual stack decisions will be made based on specific PoC requirements and learnings.

## Important Patterns

### Clean Architecture Layers

Following the **nikolovlazar modern pattern** (see `images/ca/`), the methodology emphasizes (finalized in Session 02):

**Layer Hierarchy** (bottom to top):

1. **Entities** (`app/core/entities/`): Domain models, business objects, errors
2. **Application** (`app/core/use_cases/` + `app/core/interfaces/`):
   - Use Cases implementing business rules
   - Infrastructure Interfaces defining contracts for external dependencies
3. **Infrastructure** (`app/adapters/`): Concrete implementations of interfaces (repositories, external service clients)
4. **Interface Adapters** (`app/interface_adapters/`): Orchestrators, Presenters, CLI handlers
5. **Frameworks & Drivers**: FastAPI, Typer CLI, Streamlit UI (entry points)

**Dependency Rule**:

- Dependencies point inward (toward Entities)
- Outer layers depend on inner layers, never the reverse
- Use Cases depend on Infrastructure Interfaces (defined in Application layer)
- Infrastructure implements those interfaces (Dependency Inversion Principle)
- External services (Database, APIs) consume Infrastructure layer

**Key Differences from Classic Martin Pattern**:

- **Explicit Infrastructure Layer**: Separated from Frameworks & Drivers
- **Infrastructure Interfaces in Application Layer**: Contracts defined by business needs, not technical implementation
- **Dependency Injection**: Explicitly used to wire implementations to interfaces at runtime
- See `images/IMAGE_ANALYSIS.md` section 2 for detailed comparison

### YAML-Driven Development

- Use-case specs are YAML files defining inputs, steps, and acceptance criteria
- Playbooks are YAML/Markdown capturing the full PoC journey through all 8 phases
- Agent tasks in Phase 8 are defined in `agent_task.P8.yaml`

**Acceptance Criteria Format** (Given/When/Then):

```yaml
use_case:
  name: RecoverPassword
  acceptance_criteria:
    - given: "User navigates to login page"
      when: "User selects forgot password option"
      and: "Enters valid email"
      then: "System sends recovery link to email"
```

See `images/acceptance-criteria/given-when-then-acceptance-criteria.webp` for detailed examples.

### Adapter Pattern for Substitution

All external dependencies should be behind adapters implementing core interfaces:

```python
# app/core/interfaces/storage.py
from abc import ABC, abstractmethod

class IStorageAdapter(ABC):
    @abstractmethod
    def save(self, path: str, content: bytes) -> str:
        pass

# app/adapters/storage/local.py
class LocalStorageAdapter(IStorageAdapter):
    def save(self, path: str, content: bytes) -> str:
        # Implementation for local filesystem
        pass

# app/adapters/storage/s3.py
class S3StorageAdapter(IStorageAdapter):
    def save(self, path: str, content: bytes) -> str:
        # Implementation for AWS S3
        pass
```

**Benefits**:

- Easy substitution for testing (use LocalStorage in tests, S3 in production)
- Technology-agnostic Use Cases
- Supports Phase 8 observability (wrap adapters with telemetry)

## Validation and CI

- Schemas validate playbooks, use-cases, and agent tasks
- GitHub Actions workflow (`.github/workflows/validate.yml`) runs validation on CI
- Always validate before committing changes to YAML specs

## References

### Foundational Works

- _Lean Product Playbook_ – Dan Olsen (Product-Market Fit methodology)
- _Clean Architecture_ – Robert C. Martin (architectural principles)

### Visual References

- Bob Martin's Clean Architecture diagram – Classic concentric circles pattern
- nikolovlazar's Clean Architecture diagram – Modern implementation with explicit Infrastructure layer
- Given/When/Then acceptance criteria – Behavior-Driven Development (BDD) format
- 4 Cs Framework (Card, Conversation, Confirmation, Context) – Ron Jeffries

### Technology Stack

- **Observability**: Arize / Phoenix (ML/LLM observability and tracing)
- **Vector DB**: Weaviate (vector search engine)
- **API Framework**: FastAPI (modern Python API framework)
- **Database**: PostgreSQL (relational database)
- **UI**: Streamlit (rapid data app development)
- **Testing**: pytest (Python test framework)

### Additional Resources

- See `images/IMAGE_ANALYSIS.md` for detailed visual asset analysis and architectural comparisons
