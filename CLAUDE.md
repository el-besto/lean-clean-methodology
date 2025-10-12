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
```
app/
├─ core/          # Domain models & use cases
├─ adapters/      # Infrastructure: storage, imagegen, vector, db
├─ utils/         # Helpers: observability, manifest, brief loader
├─ server.py      # FastAPI (optional micro-API)
├─ models.py      # SQLAlchemy models (Run, Approval, Alert)
└─ db.py          # DB session utilities (Postgres)
tools/
├─ agent_watcher.py   # Automation watcher
├─ init_db.py         # Create tables
└─ validate.py        # Schema validation CLI
```

**Note:** The actual project structure will be determined through ongoing PoC implementation work. The above is a proposed structure based on Clean Architecture principles and may evolve.

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
- `_howto/cursor/suggested_artifacts.md` - Cursor IDE setup guide with `.cursorrules` and task prompts

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
- **Domain** (`app/core/`): Business logic, models, use cases
- **Adapters** (`app/adapters/`): External integrations (storage, image generation, vector search)
- **Utils** (`app/utils/`): Cross-cutting concerns (observability, validation, manifest generation)

### YAML-Driven Development
- Use-case specs are YAML files defining inputs, steps, and acceptance criteria
- Playbooks are YAML/Markdown capturing the full PoC journey through all 8 phases
- Agent tasks in Phase 8 are defined in `agent_task.P8.yaml`

### Adapter Pattern for Substitution (Proposed)
All external dependencies should be behind adapters:
```python
# Example pattern (not yet implemented)
app/adapters/
├─ storage_local.py      # LocalFS implementation
├─ imagegen_firefly.py   # Adobe Firefly client
└─ compose.py            # Image composition (Pillow/OpenCV)
```

## Working with Cursor IDE

When using Cursor for development:

1. Create `.cursorrules` at repo root with constraints and scope
2. Use task prompt templates from `_howto/cursor/suggested_artifacts.md`
3. Implement one epic/task at a time in small slices
4. Keep MCP policy constraints for filesystem, shell, and HTTP access

## Validation and CI

- Schemas validate playbooks, use-cases, and agent tasks
- GitHub Actions workflow (`.github/workflows/validate.yml`) runs validation on CI
- Always validate before committing changes to YAML specs

## Version History

- **v2.0**: Added schemas & PoC templates
- **v2.1**: Added Agentic (Phase 8) spec and experimental watcher
- **v2.2**: Added Observability (Phoenix/Arize), FastAPI, Weaviate, Postgres, Streamlit, pytest suite, and Docker stack

## References

- _Lean Product Playbook_ – Dan Olsen
- _Clean Architecture_ – Robert C. Martin
- Arize / Phoenix – ML Observability
- Weaviate – Vector Search Engine
- Streamlit – Rapid UI for data apps
