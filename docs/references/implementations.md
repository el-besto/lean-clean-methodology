# Reference Implementation Analysis

**Purpose:** Analysis of three reference implementations showing different Clean Architecture patterns for the Lean-Clean Methodology

**Last Updated:** 2025-10-13 (Session 02 reconciliation)

**For authoritative folder structures:** See `/docs/framework-folder-structures.md`

---

## Executive Summary

This document analyzes three reference implementations that demonstrate different approaches to Clean Architecture for PoC development:

1. **calibration-service** - Production-grade Full CA with comprehensive testing
2. **creative-ai-poc** - Pragmatic CA with AI/observability focus
3. **lean-clean-core-skeleton** - Minimal Steel Thread for rapid prototyping

**Key Takeaway:** Each implementation represents a different point on the evolution spectrum (Steel Thread → Pragmatic CA → Full CA). See `/docs/framework-folder-structures.md` for complete progressive evolution patterns.

---

## Clean Architecture Foundation

### Grounding & Inspiration

This project uses Clean Architecture patterns. Hereafter "CA" will be used for "Clean Architecture".

- Background article by Bob Martin https://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html
- Inspiration from existing CA implementations
    - [CA with Python](https://medium.com/@shaliamekh/clean-architecture-with-python-d62712fd8d4f) - by R. Shaliamekh.
        - [clean-architecture-fastapi - by shaliamekh](https://github.com/shaliamekh/clean-architecture-fastapi/tree/main) - associated repo for blog post
    - [CA in Next.js](https://www.youtube.com/watch?v=jJVAla0dWJo) - video describing CA
        - [nextjs-clean-architecture - by nikolovlazar](https://github.com/nikolovlazar/nextjs-clean-architecture/tree/main) - associated repo for video
- Other similar architectures to Clean Architecture
    - [Hexagonal Architecture](https://alistair.cockburn.us/hexagonal-architecture/) - (a.k.a. Ports and Adapters) by Alistair Cockburn
    - [Onion Architecture](https://jeffreypalermo.com/2008/07/the-onion-architecture-part-1/) - by Jeffrey Palermo
    - [Screaming Architecture](https://blog.cleancoder.com/uncle-bob/2011/09/30/Screaming-Architecture.html) - by Uncle Bob

### Diagrams

- [The CA Diagram (sphere) - by Uncle Bob](https://blog.cleancoder.com/uncle-bob/images/2012-08-13-the-clean-architecture/CleanArchitecture.jpg)
- [The CA Diagram (simplified) - by nikolovlazar](https://github.com/nikolovlazar/nextjs-clean-architecture/blob/main/assets/clean-architecture-diagram.jpg?raw=true)

#### Original

<img src="https://blog.cleancoder.com/uncle-bob/images/2012-08-13-the-clean-architecture/CleanArchitecture.jpg" alt="Clean Architecture Diagram" width="500">

#### "Simplified"

<img src="https://github.com/nikolovlazar/nextjs-clean-architecture/blob/main/assets/clean-architecture-diagram.jpg?raw=true" alt="Simplified Clean Architecture" width="500">

### Clean Architecture in Brief

> Great summary of how to approach these layers in-practice with the project.
> _Note: it's **heavily** borrowed from nikolovlazar... thank you!_

Clean Architecture is a _set of rules_ that help us structure our applications in such way that they're easier to maintain and test, and their codebases are predictable. It's like a common language that developers understand, regardless of their technical backgrounds and programming language preferences.

Clean Architecture, and similar/derived architectures, all have the same goal - _separation of concerns_. They introduce **layers** that bundle similar code together. The "layering" helps us achieve important aspects in our codebase:

- **Independent of UI** - the business logic is not coupled with the UI framework that's being used. The same system can be used in a CLI application, without having to change the business logic or rules.
- **Independent of Database** - the database implementation/operations are isolated in their own layer, so the rest of the app does not care about which database is being used, but communicates using _Models_.
- **Independent of Frameworks** - the business rules and logic simply don't know anything about the outside world. They receive data defined with plain objects/dictionaries, _Services_ and _Repositories_ to define their own logic and functionality. This allows us to use frameworks as tools, instead of having to "mold" our system into their implementations and limitations.
- **Testable** - the business logic and rules can easily be tested because it does not depend on the UI framework, or the database, or the web server, or any other external element that builds up our system.

Clean Architecture achieves this through defining a _dependency hierarchy_ - layers depend only on layers **below them**, but not above.

---

## Analysis of Reference Implementations

### 1. calibration-service (Production-Grade Full CA)

**Represents:** Full CA pattern (production-ready)

**Strengths:**
- Complete 5-layer Clean Architecture implementation
- Comprehensive test coverage (unit, integration, e2e)
- Multiple repository implementations (in-memory, MongoDB, Postgres)
- Well-documented architecture with clear boundaries
- DTOs for cross-layer communication
- Strong exception handling strategy

**Structure:**

```text
src/
├── config/                  # Singleton configuration
│   ├── database.py             # SQLAlchemy SessionLocal/Engine
│   └── logger.py               # Logging configuration
├── entities/                # Domain layer
│   ├── exceptions.py           # Core custom exceptions
│   ├── models/                 # Domain models (Calibration, Tag)
│   └── value_objects/          # Value objects (CalibrationType, ISO8601Timestamp)
├── application/             # Application layer
│   ├── dtos/                   # Data Transfer Objects
│   ├── repositories/           # Repository interfaces only
│   ├── services/               # Service interfaces only
│   └── use_cases/              # Business logic implementation
│       ├── calibrations/
│       ├── tags/
│       └── exceptions.py
├── interface_adapters/      # Interface adapters layer
│   ├── orchestrators/          # Orchestrators coordinate use cases (NOTE: uses "controllers" terminology in actual code)
│   │   ├── calibrations/
│   │   └── tags/
│   └── presenters/             # Convert domain models to output format
├── drivers/                 # Frameworks & Drivers layer
│   └── rest/                   # FastAPI framework
│       ├── routers/            # FastAPI request handlers
│       ├── schemas/            # Pydantic input/output schemas
│       ├── dependencies.py     # FastAPI middleware
│       ├── exception_handlers.py
│       └── main.py             # FastAPI entrypoint
└── infrastructure/          # Infrastructure layer
    ├── orm_models/             # SQLAlchemy ORM models
    └── repositories/           # Repository implementations
        ├── calibration_repository/
        │   ├── in_memory_repository.py
        │   ├── mongodb_repository.py
        │   └── postgres_repository.py
        └── tag_repository/
```

**Key Patterns:**
- DTOs separate internal domain models from external interfaces
- Orchestrators perform authentication and validation, then coordinate use cases
- Presenters convert domain entities to presentation format
- Multiple repository implementations enable testing and flexibility
- Exception handling at each layer with custom exceptions

**Testing Strategy:**

```text
tests/
├── unit/                    # Pure logic testing
│   ├── application/use_cases/
│   ├── infrastructure/repositories/
│   └── interface_adapters/orchestrators/
├── integration/             # Cross-layer testing
│   ├── drivers/rest/
│   └── repositories/
└── e2e/                     # Full system testing
```

**Framework Alignment:** This matches the **Full CA** structure in `/docs/framework-folder-structures.md`

---

### 2. creative-ai-poc (AI-Focused Pragmatic CA)

**Represents:** Pragmatic CA pattern (enterprise PoC)

**Strengths:**
- Simpler, more pragmatic layer naming (entities, application, infrastructure, drivers)
- Protocol-based ports for clean abstraction
- Built-in observability (OpenTelemetry, Phoenix)
- Multiple driver implementations (CLI, API, Streamlit UI)
- Crash reporting and telemetry integration
- Vector store and blob storage adapters

**Structure:**

```text
src/
├── entities/                # Core business models (NOTE: uses "domain" in actual code)
│   └── models.py               # Brief, Product, CreativeAsset, Aspect
├── application/             # Business logic & ports
│   ├── ports/                  # Protocol-based interfaces (ports & adapters pattern)
│   │   ├── image_gen.py
│   │   ├── vector_store.py
│   │   ├── blob_store.py
│   │   ├── compositor.py
│   │   ├── checks.py
│   │   └── telemetry.py
│   └── use_cases/
│       └── generate_creatives.py
├── infrastructure/          # External integrations (NOTE: this is NOT split into adapters/ + infrastructure/)
│   ├── config.py
│   ├── telemetry.py            # OpenTelemetry setup
│   ├── adapters/               # Port implementations
│   │   ├── openai_image_gen.py
│   │   ├── weaviate_store.py
│   │   ├── minio_blob.py
│   │   ├── pillow_compositor.py
│   │   └── simple_checks.py
│   └── services/
│       ├── instrumentation.py  # OTel wrappers
│       └── crash_reporter.py
└── drivers/                 # Entry points
    ├── cli.py                  # Typer CLI with adapter wiring
    ├── api.py                  # FastAPI REST endpoints
    └── ui/
        └── streamlit/          # Streamlit pages
```

**Key Patterns:**
- Python `Protocol` for ports (structural subtyping, no inheritance needed)
- Dependency injection at driver level (explicitly wired in CLI/API)
- Observability as first-class concern (telemetry, transactions, crash reporting)
- Multiple drivers share same use cases
- Use cases accept port interfaces, not concrete implementations

**Observability Integration:**
```python
# From cli.py:
otel = OTelTelemetry("cli")
tx = OTelTransactionManager(otel)
crash = LoggingCrashReporter()

generate_creatives_for_brief(
    brief=b,
    image_gen=OpenAIImagesAdapter(),
    vector_store=WeaviateAdapter(),
    # ... other adapters
    telemetry=otel,
    tx=tx,
    crash=crash,
)
```

**Framework Alignment:** This is closest to **Pragmatic CA** in `/docs/framework-folder-structures.md`, but predates the adapters/infrastructure split decision (Decision 10).

**Key Difference from Framework:** The framework now recommends splitting `adapters/` (external services) and `infrastructure/` (persistence) as separate top-level folders, not nested under `infrastructure/`.

---

### 3. lean-clean-core-skeleton (Minimal Steel Thread)

**Represents:** Steel Thread pattern (rapid prototyping)

**Strengths:**
- Absolute minimal viable structure
- Flat, easy to understand
- Fast to bootstrap
- Clear "grow from here" approach
- Focuses on essential elements only

**Structure:**

```text
app/
├── cli.py                   # Entry point (argparse)
├── core/                    # NOTE: would be "entities/" in Session 02 terminology
│   ├── models.py               # Brief, Brand, Product dataclasses
│   └── use_cases.py            # generate_creatives_for_brief (placeholder impl)
├── adapters/
│   ├── storage_local.py        # File system operations
│   ├── imagegen_firefly.py     # Stub for image generation
│   └── compose.py              # Stub for composition
└── utils/
    ├── brief_loader.py         # YAML/JSON -> Brief
    └── manifest.py             # build_manifest()
```

**Key Patterns:**
- No layers for orchestrators, presenters, or repositories initially
- Direct use case invocation from CLI
- Stubs/placeholders for not-yet-implemented features
- Utility functions rather than full services
- Flat structure that can grow into layered architecture

**Framework Alignment:** This matches the **Steel Thread** structure in `/docs/framework-folder-structures.md`, except it's missing the `drivers/` folder. The framework now recommends `drivers/` from day 1 for CLI + UI (Enterprise PoC Reality - Decision 9).

**Key Difference from Framework:** Session 02 established that even Steel Thread needs `drivers/cli/` and `drivers/ui/` from the start because enterprise PoCs need visual stakeholder feedback (Streamlit UI) alongside automation (CLI).

---

## Technology Stack Recommendations

### Core (Always)
- **Python 3.11+** - Modern Python features
- **uv** - Fast dependency management
- **Typer** - CLI framework
- **Pydantic** - Data validation and settings
- **pytest** - Testing framework

### API (When needed)
- **FastAPI** - High-performance async API
- **Uvicorn** - ASGI server

### Storage (Pick based on needs)
- **Local filesystem** - Development, simple PoCs
- **MinIO** - S3-compatible object storage (local dev)
- **AWS S3** - Production blob storage
- **PostgreSQL** - Relational data (approvals, runs, alerts)

### Vector/Search (AI PoCs)
- **Weaviate** - Vector database for semantic search
- **ChromaDB** - Alternative, lighter weight

### Observability (Phase 8+)
- **OpenTelemetry** - Tracing, metrics, logs
- **Phoenix** - Local LLM trace explorer
- **Arize** - Hosted ML observability

### UI (Human-in-loop)
- **Streamlit** - Rapid prototyping dashboards (ALWAYS for enterprise PoCs - Decision 9)
- **Gradio** - ML model interfaces

### CI/CD
- **GitHub Actions** - Validation, testing, deployment
- **pre-commit** - Git hooks for linting/formatting

---

## Common Pitfalls & Solutions

### Pitfall 1: Over-engineering Early
**Problem:** Starting with full 5-layer CA for a 2-day PoC.

**Solution:** Use Steel Thread structure initially. Refactor to Pragmatic/Full CA only when:
- Multiple entry points needed (CLI + API + UI)
- Complex authorization/validation logic
- Team grows beyond 2-3 developers
- Preparing for production

See `/docs/framework-folder-structures.md` for complete evolution patterns.

### Pitfall 2: Leaky Abstractions
**Problem:** Domain layer importing FastAPI or SQLAlchemy.

**Solution:**
- Use linting rules to enforce boundaries
- Code review checklist
- Keep domain pure (only stdlib imports)

### Pitfall 3: Anemic Domain Models
**Problem:** Domain models are just data bags, all logic in use cases.

**Solution:**
- Put validation in domain models (`brief.validate()`)
- Add domain methods (`product.generate_prompt()`)
- Use value objects for complex types

### Pitfall 4: Missing Observability
**Problem:** No way to debug or monitor AI workflows in production.

**Solution:**
- Add telemetry ports early (even if stub implementation)
- Instrument use cases with events/spans
- Test with Phoenix locally before adding Arize

### Pitfall 5: Hardcoded Dependencies
**Problem:** Use cases directly instantiate adapters.

**Solution:**
- Always use dependency injection
- Pass ports/adapters from driver layer
- Use factory functions for complex wiring

### Pitfall 6: Skipping Drivers Layer
**Problem:** Keeping CLI/UI as root-level files instead of organized drivers.

**Solution (Enterprise PoC Reality - Decision 9):**
- Always use `drivers/` folder from Steel Thread onwards
- Include `drivers/cli/` for automation (ALWAYS)
- Include `drivers/ui/streamlit/` for stakeholder demos (ALWAYS for enterprise PoCs)
- Add `drivers/rest/` when integrating with enterprise systems (WHEN NEEDED)

This enables multi-stakeholder workshops where Visual feedback is critical from day 1.

---

## Key Architectural Patterns

### 1. Ports & Adapters (Hexagonal Architecture)

**Use Protocols for Ports:**

```python
# application/ports/storage.py
from typing import Protocol

class BlobStorePort(Protocol):
    def put_bytes(self, key: str, data: bytes, content_type: str) -> str: ...
    def get_bytes(self, key: str) -> bytes: ...
```

**Implement Adapters:**

```python
# adapters/storage/s3_storage.py (Session 02: top-level adapters/)
import boto3

class S3BlobStore:
    def __init__(self, bucket: str):
        self.s3 = boto3.client('s3')
        self.bucket = bucket

    def put_bytes(self, key: str, data: bytes, content_type: str) -> str:
        self.s3.put_object(Bucket=self.bucket, Key=key, Body=data)
        return f"s3://{self.bucket}/{key}"
```

**Session 02 Note:** Ports follow two-step evolution:
- Early (Steel Thread/Pragmatic CA): Co-located `protocol.py` with implementations
- Full CA: Extracted to `application/ports/` split into `services/` and `repositories/`

See `/docs/framework-folder-structures.md` Decision 12 for complete details.

### 2. Dependency Injection at Entry Point

**Wire at driver level:**

```python
# drivers/cli/__main__.py
def build_dependencies():
    config = load_config()

    # Adapters
    image_gen = OpenAIImageGen(api_key=config.openai_api_key)
    vector_store = WeaviateAdapter(url=config.weaviate_url)
    storage = S3BlobStore(bucket=config.s3_bucket)

    # Telemetry
    telemetry = OTelTelemetry("cli")

    return {
        "image_gen": image_gen,
        "vector_store": vector_store,
        "storage": storage,
        "telemetry": telemetry,
    }

@app.command()
def run(brief: str):
    deps = build_dependencies()
    result = generate_creatives(brief, **deps)
```

### 3. Exception Translation

**Convert library exceptions to domain exceptions:**

```python
# adapters/generation/openai_image_gen.py (Session 02: top-level adapters/)
from openai import OpenAIError
from entities.exceptions import ImageGenerationError  # Session 02: entities/ not domain/

class OpenAIImageGen:
    def generate_png(self, prompt: str, size: str) -> bytes:
        try:
            response = self.client.images.generate(...)
            return response.data[0].b64_json
        except OpenAIError as e:
            # Translate to domain exception
            raise ImageGenerationError(f"Image generation failed: {e}")
```

### 4. Configuration Management

**Centralize configuration:**

```python
# infrastructure/config/settings.py
from pydantic_settings import BaseSettings

class Settings(BaseSettings):
    # API Keys
    openai_api_key: str
    arize_api_key: str | None = None

    # Infrastructure
    weaviate_url: str = "http://localhost:8080"
    postgres_url: str = "postgresql://localhost/pocdb"
    s3_bucket: str = "poc-assets"

    # Observability
    otel_endpoint: str = "http://localhost:4317"
    phoenix_endpoint: str = "http://localhost:6006"

    class Config:
        env_file = ".env"

_settings = None

def get_settings() -> Settings:
    global _settings
    if _settings is None:
        _settings = Settings()
    return _settings
```

### 5. Observability Integration

**Instrument use cases with telemetry:**

```python
# application/use_cases/generate_creatives.py
from application.ports.telemetry import TelemetryPort

def generate_creatives(
    brief: Brief,
    image_gen: ImageGenPort,
    telemetry: TelemetryPort | None = None,
) -> List[Asset]:

    if telemetry:
        telemetry.add_event("generation_started", {"brand": brief.brand_id})

    try:
        # Business logic
        assets = []
        for product in brief.products:
            # ...
            assets.append(asset)

        if telemetry:
            telemetry.add_event("generation_completed", {"count": len(assets)})

        return assets
    except Exception as e:
        if telemetry:
            telemetry.capture_exception(e)
        raise
```

---

## Testing Strategy Examples

### Unit Tests (Fast, Isolated)

**Test domain logic:**

```python
# tests/unit/entities/test_models.py (Session 02: entities/ not domain/)
from entities.models import Brief  # Session 02: entities/
from entities.exceptions import InvalidBriefError

def test_brief_validation_requires_products():
    brief = Brief(brand_id="test", campaign_message="msg", products=[])

    with pytest.raises(InvalidBriefError):
        brief.validate()
```

**Test use cases with mocks:**

```python
# tests/unit/application/test_generate_creatives.py
from unittest.mock import Mock
from application.use_cases.generate_creatives import generate_creatives

def test_generate_creatives_calls_image_gen():
    # Mock dependencies
    image_gen = Mock()
    image_gen.generate_png.return_value = b"fake_png_data"

    # Execute
    result = generate_creatives(brief, image_gen=image_gen, ...)

    # Assert
    assert image_gen.generate_png.called
    assert len(result) > 0
```

**Session 02 Note:** The framework now uses:
- `acceptance/` tests (stakeholder contracts with fakes - ALWAYS)
- `unit/` tests (isolated logic)
- `integration/` tests (real adapters)

See `/docs/framework-folder-structures.md` Decision 7 for complete test structure.

### Integration Tests (Cross-Layer)

**Test adapter implementations:**

```python
# tests/integration/adapters/test_openai_adapter.py (Session 02: adapters/ top-level)
import pytest
from adapters.generation.openai_image_gen import OpenAIImageGen

@pytest.mark.integration
def test_openai_generates_image():
    adapter = OpenAIImageGen()

    image_bytes = adapter.generate_png("A sunset over mountains", "1024x1024")

    assert len(image_bytes) > 0
    assert image_bytes.startswith(b'\x89PNG')  # PNG header
```

### Acceptance Tests (Stakeholder Contracts)

**Session 02 Addition - Outside-In TDD:**

```python
# tests/acceptance/features/test_localized_campaign_feature.py
@pytest.mark.feature("localized_campaign_generation")
class TestLocalizedCampaignFeature:
    """
    Feature: Generate compliant, localized creative variants

    Stakeholders: Creative Lead, Ad Ops, IT, Legal

    This test uses FAKES for all external dependencies to enable:
    1. Workshop demos without expensive API calls
    2. Fast feedback loops (<1s test execution)
    3. Deterministic outputs for validation
    """

    async def test_generate_summer_sale_campaign(self, orchestrator):
        # Given: Campaign request
        request = LocalizedCampaignRequest(...)

        # When: Generating localized variants
        response = await orchestrator.generate_campaign(request)

        # Then: All stakeholder requirements met
        assert all(cs.brand_compliant for cs in response.creative_sets.values())
```

See `/docs/framework-folder-structures.md` for complete Outside-In TDD workflow examples.

---

## Summary & Recommendations

### Choosing the Right Pattern

**Reference these implementations:**
- **calibration-service** → When building production-grade services (Full CA)
- **creative-ai-poc** → When building enterprise PoCs with AI/observability (Pragmatic CA)
- **lean-clean-core-skeleton** → When proving technical feasibility (Steel Thread)

**For complete progressive evolution:** See `/docs/framework-folder-structures.md`

### Key Learnings from Reference Implementations

1. **calibration-service shows Full CA benefits:**
   - Multiple repository implementations
   - Comprehensive DTOs
   - Separated entities from ORM
   - Production-ready testing

2. **creative-ai-poc shows Pragmatic CA speed:**
   - Protocol-based ports (no inheritance)
   - Flat structure (faster navigation)
   - Observability from day 1
   - Multiple drivers (CLI/API/UI)

3. **lean-clean-core-skeleton shows Steel Thread principles:**
   - Minimal layers (prove concept fast)
   - Direct wiring (no indirection)
   - Stubs over real implementations
   - Grow when needed

### Session 02 Framework Improvements

The reference implementations informed Session 02 architectural decisions:

- **Decision 1:** Terminology clarification (orchestrators not controllers)
- **Decision 8:** Two types of fakes (adapters vs repositories)
- **Decision 9:** Enterprise PoC Reality (CLI + UI always, FastAPI when needed)
- **Decision 10:** Infrastructure split (adapters/ + infrastructure/)
- **Decision 12:** Two-step ports evolution (co-located → extracted)

See `/docs/framework-folder-structures.md` for all 12 architectural decisions and their rationale.

---

## Cross-References

**For framework folder structures:** `/docs/framework-folder-structures.md`
**For core principles:** `/docs/lean-clean-axioms.md`
**For methodology phases:** `/README.md`

---

**Last Updated:** 2025-10-13
**Status:** Final (Session 02 reconciliation complete)
**Purpose:** Reference implementation analysis only - defer to framework document for authoritative structures
