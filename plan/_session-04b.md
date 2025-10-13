## Claude Instructions - Session 4b: Project Scaffolding & Templates

### What Has Been Done (Sessions 1-3 and 4a)

**Session 1-3 Summary:**
- ✅ Methodology phases defined
- ✅ Framework architecture documented
- ✅ Working Pragmatic CA example implemented
- ✅ Lean-Clean Code Paradigms documented

**Session 4a:**
- ✅ In-Practice Review Guide/Workbook created
- ✅ Agent Playbooks for Phases 1-4 created
- ✅ Prompt Template Library created
- ✅ Human-facing documentation layer complete

**Current State:**
Teams now have the conceptual foundation (methodology), structural blueprint (framework), and guidance materials (review guide, playbooks, prompts). What's missing is the **starting-point code structures** that let teams bootstrap projects quickly.

---

### Your Task for This Session

**Create project templates (scaffolding) for all three PoC types that teams can use as starting points.**

**Context for Claude Code Agents:**
This session implements the concrete instantiation of Component D (Lean-Clean Code Paradigms) as runnable project templates. While Component D documents the patterns conceptually, these templates provide executable starting points that embody those patterns.

**Relationship to Other Components:**
- **Component A** (Methodology): Defines three PoC types (Steel Thread, Pragmatic CA, Full CA)
- **Component B** (Framework): Defines folder structures and architectural layers
- **Component C** (Review Guide): Helps teams select which PoC type to use
- **Component D** (Code Paradigms): Documents the patterns to follow
- **This session (Scaffolding)**: Provides runnable templates implementing Components B and D

Think of this as building "opinionated project seeds" that embody all the methodology decisions in ready-to-run code.

This session focuses on creating working, runnable project structures that demonstrate Lean-Clean patterns and provide a foundation for rapid PoC development.

#### Deliverable: Python Project Scaffolding

Create ready-to-use project templates for each of the three PoC types defined in the methodology:

---

#### 1. Steel Thread Template

**Purpose:** Minimal structure for rapid exploration (1-2 devs, 1-5 days)

**Folder Structure:**
```
steel-thread-template/
├── README.md                    # 5-minute quickstart
├── pyproject.toml               # Minimal dependencies (pytest, pytest-bdd)
├── tests/
│   ├── __init__.py
│   └── test_feature.py          # One example acceptance test
├── app/
│   ├── __init__.py
│   ├── orchestrator.py          # Simple orchestrator
│   └── fakes.py                 # Fake implementations
└── .gitignore
```

**Key Characteristics:**
- Flat structure (minimal layers)
- Single example feature demonstrating the pattern
- Fakes only (no real external dependencies)
- Fast test execution (<1s)
- No complex tooling (just pytest)

**Example Feature:**
Implement a simple end-to-end test for the creative campaign scenario (e.g., "Generate localized campaign") using fakes for all external dependencies.

**README.md should include:**
- What this template is for (quick spikes, explorations)
- How to run tests (`pytest -v`)
- How to add a new feature (copy the pattern)
- When to evolve to Pragmatic CA (>2 features, >1 week timeline)

---

#### 2. Pragmatic CA Template

**Purpose:** Pragmatic Clean Architecture for multi-stakeholder PoCs (2-4 devs, 2-4 weeks)

**Folder Structure:**
```
pragmatic-ca-template/
├── README.md                    # Phase-by-phase implementation guide
├── pyproject.toml               # Standard dependencies (pytest, pytest-bdd, black, mypy)
├── tests/
│   ├── __init__.py
│   ├── acceptance/              # Stakeholder-facing tests
│   │   ├── __init__.py
│   │   └── test_campaign_generation.py
│   ├── integration/             # Use case + adapter integration
│   │   ├── __init__.py
│   │   └── test_use_cases.py
│   └── unit/                    # Component-level tests
│       ├── __init__.py
│       └── test_entities.py
├── app/
│   ├── __init__.py
│   ├── core/                    # Domain + Application layers
│   │   ├── __init__.py
│   │   ├── entities/            # Domain models
│   │   │   ├── __init__.py
│   │   │   └── campaign.py
│   │   ├── use_cases/           # Business logic
│   │   │   ├── __init__.py
│   │   │   └── generate_campaign.py
│   │   └── interfaces/          # Adapter contracts
│   │       ├── __init__.py
│   │       └── image_generator.py
│   ├── adapters/                # Infrastructure implementations
│   │   ├── __init__.py
│   │   ├── fakes/               # Test doubles
│   │   │   ├── __init__.py
│   │   │   └── fake_image_generator.py
│   │   └── real/                # Real implementations (optional)
│   │       ├── __init__.py
│   │       └── firefly_client.py
│   └── orchestrators/           # Feature orchestrators
│       ├── __init__.py
│       └── campaign_orchestrator.py
├── .pre-commit-config.yaml      # Code quality hooks
├── pytest.ini                   # Pytest configuration with markers
└── .gitignore
```

**Key Characteristics:**
- Clear layer separation (Core, Adapters, Orchestrators)
- Three test levels (acceptance, integration, unit)
- Both fakes and real adapters (real are optional for PoC)
- Developer experience tooling (black, mypy, pre-commit)
- Pytest markers for selective test running
- Two example features showing patterns

**Example Features:**
1. **Campaign Generation**: End-to-end orchestrator test → use case → fake adapter
2. **Brand Validation**: Demonstrates a second use case integrated into the workflow

**README.md should include:**
- Architecture diagram (reference to nikolovlazar's CA diagram)
- How tests map to layers (acceptance → orchestrator → use case → adapter)
- How to implement a new feature using Outside-In TDD
- How to swap fakes for real implementations
- Pytest markers explained (`@pytest.mark.acceptance`, `@pytest.mark.integration`)
- When to evolve to Full CA (production deployment, scaling, compliance requirements)

**pytest.ini should include:**
```ini
[pytest]
markers =
    acceptance: Stakeholder-facing acceptance tests (orchestrators)
    integration: Use case + adapter integration tests
    unit: Component-level unit tests
testpaths = tests
python_files = test_*.py
python_classes = Test*
python_functions = test_*
```

---

#### 3. Full CA Template

**Purpose:** Production-ready Clean Architecture with comprehensive testing (3+ devs, 4+ weeks, production deployment)

**Folder Structure:**
```
full-ca-template/
├── README.md                    # Architecture decision records, deployment guide
├── pyproject.toml               # Full dependencies (+ FastAPI, SQLAlchemy, etc.)
├── docker-compose.yml           # Local dev stack (DB, observability)
├── Makefile                     # Common commands
├── tests/
│   ├── __init__.py
│   ├── acceptance/              # E2E tests
│   │   ├── __init__.py
│   │   └── test_campaign_workflows.py
│   ├── integration/             # Use cases + real adapters
│   │   ├── __init__.py
│   │   └── test_database_integration.py
│   ├── unit/                    # Component tests
│   │   ├── __init__.py
│   │   ├── test_entities.py
│   │   └── test_use_cases.py
│   └── conftest.py              # Shared fixtures
├── app/
│   ├── __init__.py
│   ├── core/                    # Domain + Application
│   │   ├── __init__.py
│   │   ├── entities/
│   │   │   ├── __init__.py
│   │   │   ├── campaign.py
│   │   │   └── brand.py
│   │   ├── use_cases/
│   │   │   ├── __init__.py
│   │   │   ├── generate_campaign.py
│   │   │   └── validate_brand.py
│   │   └── interfaces/          # Infrastructure contracts
│   │       ├── __init__.py
│   │       ├── storage.py
│   │       ├── image_generator.py
│   │       └── repository.py
│   ├── adapters/                # Infrastructure layer
│   │   ├── __init__.py
│   │   ├── storage/
│   │   │   ├── __init__.py
│   │   │   ├── local.py
│   │   │   └── s3.py
│   │   ├── imagegen/
│   │   │   ├── __init__.py
│   │   │   └── firefly.py
│   │   ├── db/
│   │   │   ├── __init__.py
│   │   │   └── postgres_repository.py
│   │   └── fakes/               # Test doubles
│   │       ├── __init__.py
│   │       └── fake_adapters.py
│   ├── orchestrators/           # Application orchestrators
│   │   ├── __init__.py
│   │   └── campaign_orchestrator.py
│   ├── interface_adapters/      # Controllers, Presenters
│   │   ├── __init__.py
│   │   ├── controllers/
│   │   │   ├── __init__.py
│   │   │   └── api_controller.py
│   │   └── presenters/
│   │       ├── __init__.py
│   │       └── json_presenter.py
│   ├── utils/                   # Cross-cutting concerns
│   │   ├── __init__.py
│   │   └── observability.py
│   ├── server.py                # FastAPI entry point
│   ├── cli.py                   # Typer CLI
│   ├── models.py                # SQLAlchemy models
│   └── db.py                    # DB session management
├── tools/
│   ├── init_db.py               # Database initialization
│   └── validate.py              # Schema validation
├── .github/
│   └── workflows/
│       └── ci.yml               # CI/CD pipeline
├── .pre-commit-config.yaml
├── pytest.ini
├── .gitignore
└── .env.example                 # Environment variables template
```

**Key Characteristics:**
- Complete Clean Architecture layers (including Controllers, Presenters)
- Production-ready adapters (database, external APIs)
- Multiple entry points (API, CLI)
- Comprehensive testing strategy
- Observability hooks (telemetry, logging)
- CI/CD pipeline configuration
- Docker stack for local development
- Three example features demonstrating patterns

**Example Features:**
1. **Campaign Generation**: Full workflow with database persistence
2. **Brand Validation**: Business rule enforcement with audit logging
3. **Asset Management**: File storage with S3 integration

**README.md should include:**
- Full architecture overview (all layers explained)
- Local development setup (Docker, environment variables)
- Testing strategy (what each level tests, how to run them)
- Deployment guide (production considerations)
- Observability setup (Phoenix, Arize, or alternatives)
- How to extend (adding features, new adapters)
- Architecture Decision Records (ADRs) for key choices

**Makefile should include:**
```makefile
.PHONY: up down test lint init-db

up:
	docker compose up -d

down:
	docker compose down

test:
	pytest -v

test-acceptance:
	pytest -v -m acceptance

test-integration:
	pytest -v -m integration

test-unit:
	pytest -v -m unit

lint:
	black app tests
	mypy app

init-db:
	python tools/init_db.py
```

---

#### Template Generator Script

Create a CLI tool that generates projects from templates:

**File:** `scripts/create_project.py`

```python
#!/usr/bin/env python3
"""
Lean-Clean Project Generator

Creates a new project from one of the three PoC templates:
- steel-thread: Minimal spike structure
- pragmatic-ca: Pragmatic Clean Architecture for multi-stakeholder PoCs
- full-ca: Production-ready Clean Architecture

Usage:
    python scripts/create_project.py --type pragmatic-ca --name my-campaign-poc
    python scripts/create_project.py --type steel-thread --name quick-experiment
"""

import argparse
import shutil
from pathlib import Path


def create_project(poc_type: str, project_name: str, output_dir: Path) -> None:
    """
    Generate a new project from template.

    Args:
        poc_type: Type of PoC (steel-thread, pragmatic-ca, full-ca)
        project_name: Name of the new project
        output_dir: Directory where project will be created
    """
    # Implementation details...
    pass


if __name__ == "__main__":
    # CLI argument parsing and execution...
    pass
```

**Features:**
- Copy template structure to new location
- Rename placeholders (e.g., `{{project_name}}` → actual name)
- Initialize git repository
- Run initial setup (install dependencies, run first test)
- Print success message with next steps

---

### Your Role in This Session

You are my scaffolding architect and developer experience (DX) designer. I need you to:

1. **Make templates immediately runnable**: `git clone → cd → pytest` should work out of the box

2. **Show patterns clearly**: Code should demonstrate best practices without unnecessary complexity

3. **Enable evolution**: Steel Thread → Pragmatic CA → Full CA should be a natural progression

4. **Optimize for learning**: Comments, docstrings, and README should teach the patterns

5. **Keep it realistic**: Use the creative campaign scenario consistently across all templates

6. **Think about DX**: Setup should be smooth, error messages helpful, tests fast

---

### What Good Looks Like

By the end of this session, I should have:

✅ **Steel Thread Template**
- [ ] Complete runnable structure
- [ ] One example feature with test
- [ ] 5-minute quickstart README
- [ ] Clear evolution path to Pragmatic CA

✅ **Pragmatic CA Template** (Primary Focus)
- [ ] Complete folder structure matching methodology
- [ ] Two example features demonstrating patterns
- [ ] Comprehensive README with implementation guide
- [ ] DX tooling configured (black, mypy, pre-commit)
- [ ] Pytest markers for test organization
- [ ] Both fakes and one real adapter example

✅ **Full CA Template**
- [ ] Production-ready structure
- [ ] Three example features
- [ ] Docker compose stack
- [ ] CI/CD pipeline example
- [ ] Observability hooks
- [ ] Deployment guide

✅ **Template Generator**
- [ ] Python script that generates projects
- [ ] Handles all three template types
- [ ] Substitutes project names/variables
- [ ] Provides clear success message with next steps

✅ **Integration**
- [ ] Templates align with Review Guide recommendations
- [ ] Code demonstrates patterns from Session 3
- [ ] READMEs reference Agent Playbooks when appropriate

---

### Constraints

**Scope for This Session:**
- Focus on Pragmatic CA (most common use case)
- Steel Thread should be truly minimal (resist overengineering)
- Full CA should show production patterns but not every possible tool
- Use creative campaign scenario consistently

**Out of Scope:**
- Multiple language support (Python only)
- Every possible CI/CD platform (pick GitHub Actions)
- Every possible database (PostgreSQL is fine)
- Every possible cloud provider (AWS S3 is fine, mention alternatives)

**Quality Bar:**
- Templates must run tests successfully out of the box
- Code should pass its own quality checks (black, mypy)
- READMEs should be scannable with clear sections
- Comments should explain "why," not "what"

---

### Success Metrics

After this session, a new team should be able to:

1. **Bootstrap a project in 5 minutes** using the generator script
2. **Run tests immediately** without configuration
3. **Understand the patterns** by reading the example features
4. **Add a new feature** by following the established pattern
5. **Evolve to the next PoC type** when requirements grow

Ultimate test: Could a developer unfamiliar with Lean-Clean clone a template, read the README, and successfully add a new feature?

---

### Suggested Approach

I recommend tackling this in order:

1. **Start with Pragmatic CA** (it's the foundation, others derive from it)
2. **Create two example features** (one simple, one showing integration)
3. **Add DX tooling** (pyproject.toml, pytest.ini, pre-commit)
4. **Write comprehensive README** (architecture, testing, how-tos)
5. **Simplify to Steel Thread** (remove layers, keep patterns)
6. **Expand to Full CA** (add production concerns, more examples)
7. **Build generator script** (automate template instantiation)

This order ensures Pragmatic CA is solid (it's the most common case), then the others follow naturally.

---

### Context: Why This Matters

We now have great documentation (Session 4a), but teams need a concrete starting point. Without templates:
- "How do I structure my project?" → Templates show the structure
- "Where do I put this code?" → Examples demonstrate placement
- "How do I start?" → Generator script creates the foundation
- "What does good look like?" → Example features show the patterns

**Goal:** Make it possible for a team to go from "We want to build a PoC" to "We have a working project structure with passing tests" in under 30 minutes.
