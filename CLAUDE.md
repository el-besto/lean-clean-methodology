# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Overview

**Lean-Clean Methodology** is a pragmatic framework for designing, prototyping, and operationalizing AI-driven systems. It blends Lean Product Development (iterative learning, minimal viable slices) with Clean Architecture (decoupled layers, testable boundaries, replaceable adapters) and Agentic Design (AI-assisted orchestration, observability, human-in-the-loop workflows).

**Current Status:** Living documentation repository (no implementation code yet)

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

### Proposed App Structure (NOT FINALIZED - being worked out through PoC implementation)

Following nikolovlazar's modern Clean Architecture pattern with explicit Infrastructure layer:

```
app/
├─ core/                    # Domain + Application layers
│  ├─ entities/             # Domain models (Entities)
│  ├─ use_cases/            # Application business rules (Use Cases)
│  └─ interfaces/           # Infrastructure interfaces (defined by app, implemented by infra)
├─ adapters/                # Infrastructure layer (implements core/interfaces)
│  ├─ storage/              # Storage implementations (local, S3, etc.)
│  ├─ imagegen/             # Image generation clients (Firefly, DALL-E)
│  ├─ vector/               # Vector DB adapters (Weaviate)
│  ├─ db/                   # Database repositories (Postgres)
│  └─ compose.py            # Image composition utilities
├─ interface_adapters/      # Controllers, Presenters, CLI handlers
│  ├─ controllers/          # API/CLI request handlers
│  └─ presenters/           # Response formatters
├─ utils/                   # Cross-cutting concerns
│  ├─ observability.py      # Phoenix/Arize telemetry
│  ├─ manifest.py           # Manifest generation
│  └─ brief_loader.py       # YAML loading utilities
├─ server.py                # FastAPI (Frameworks & Drivers layer)
├─ cli.py                   # Typer CLI (Frameworks & Drivers layer)
├─ models.py                # SQLAlchemy models
└─ db.py                    # DB session utilities
tools/
├─ agent_watcher.py         # Automation watcher
├─ init_db.py               # Create tables
└─ validate.py              # Schema validation CLI
```

**Key Pattern - Infrastructure Interfaces**:
- `app/core/interfaces/` defines contracts (e.g., `IStorageAdapter`, `IImageGenerator`)
- `app/adapters/` provides concrete implementations
- Use Cases depend on interfaces, not implementations (Dependency Inversion)
- Achieved through Dependency Injection at runtime

**Note:** The actual project structure will be determined through ongoing PoC implementation work. The above is a proposed structure based on Clean Architecture principles (see `images/ca/clean-architecture-diagram-nikolovlazar.jpg`) and may evolve.

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

## Key Documentation Files

### Methodology Core
- `README.lean-clean.md` - Complete v2.2 methodology documentation with all 8 phases
- `Lean-Clean-PoC-Playbook-v2-template.md` - Template for creating project-specific playbooks
- `lean-clean-methodology-v1.csv` & `lean-clean-methodology-v2.csv` - Phase tables

### How-To Guides
- `_howto/NOTES_HowToUse.md` - Repo bootstrap and app skeleton setup
- `_howto/NOTES_HowToUse_dayOf.md` - Day-of-demo workflow
- `_howto/NOTES.md` - Tips, tricks, and bundle explanations

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

## Typical Development Workflow (PROPOSED - being validated through PoC)

When implementing a PoC using this methodology:

### 1. Setup
```bash
uv sync                          # Install dependencies (when pyproject.toml exists)
```

### 2. Validation (when schemas exist)
```bash
uv run python tools/validate.py playbook <playbook.yaml>
uv run python tools/validate.py use_case <use-case.yaml>
make validate-playbook           # If Makefile exists
make validate-use-case
```

### 3. CLI Execution (typical pattern)
```bash
uv run python -m app.cli run --brief ./campaigns/brief.yaml
uv run python -m app.cli validate --brief ./campaigns/brief.yaml
```

### 4. Testing
```bash
uv run pytest -q                 # Run test suite
```

### 5. Agentic Watcher (when implemented)
```bash
# With observability enabled
export OBS_ENABLED=1
export PHOENIX_ENDPOINT=http://localhost:6006/api/traces
export ARIZE_ENDPOINT=https://api.arize.com/v1/log
export ARIZE_API_KEY=xxx

uv run python tools/agent_watcher.py \
  --inbox ./briefs/inbox \
  --cmd "uv run python -m app.cli run --brief {brief}" \
  --out-root ./out \
  --alerts-root ./alerts
```

### 6. Docker Stack (when docker-compose.yaml exists)
```bash
make up            # docker compose up -d
make down          # tear down
make db-init       # create tables in Postgres
make watch         # run watcher locally
make api           # start FastAPI server
```

**Note:** These workflows are proposed patterns documented in the methodology. Actual implementation commands will be finalized through PoC development.

## Technology Stack (PROPOSED - subject to change based on PoC learnings)

### Core
- **Language**: Python (uv for dependency management)
- **CLI**: Typer or argparse
- **API**: FastAPI (optional)
- **Database**: PostgreSQL (approvals, runs, alerts)
- **Vector DB**: Weaviate (optional, for RAG)

### Observability
- **Phoenix**: Local LLM trace explorer (port 6006)
- **Arize**: Hosted observability/analytics
- **Observability utils**: `app/utils/observability.py`

### UI Options
- **Streamlit**: Simple dashboard for human-in-loop approvals (port 8501)
- **FastAPI**: REST API for approvals, runs, alerts
- **CLI**: Primary automation surface

### Testing
- **pytest**: Test suite framework

**Note:** Technology choices documented in the methodology are recommendations. Actual stack decisions will be made based on specific PoC requirements and learnings.

## Important Patterns (from methodology - implementation TBD)

### Clean Architecture Layers (Proposed)

Following the **nikolovlazar modern pattern** (see `images/ca/`), the methodology emphasizes:

**Layer Hierarchy** (bottom to top):
1. **Entities** (`app/core/entities/`): Domain models, business objects, errors
2. **Application** (`app/core/use_cases/` + `app/core/interfaces/`):
   - Use Cases implementing business rules
   - Infrastructure Interfaces defining contracts for external dependencies
3. **Infrastructure** (`app/adapters/`): Concrete implementations of interfaces (repositories, external service clients)
4. **Interface Adapters** (`app/interface_adapters/`): Controllers, Presenters, CLI handlers
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

### Adapter Pattern for Substitution (Proposed)

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

## Version History

- **v2.0**: Added schemas & PoC templates
- **v2.1**: Added Agentic (Phase 8) spec and experimental watcher
- **v2.2**: Added Observability (Phoenix/Arize), FastAPI, Weaviate, Postgres, Streamlit, pytest suite, and Docker stack

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
