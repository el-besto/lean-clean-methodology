# PoC Project Structure Analysis & Recommendations

> **⚠️ SUPERSEDED BY SESSION 01**
>
> This document was created on 2025-10-11, before Session 01 architectural decisions (2025-10-13).
>
> **Session 01 now authoritatively defines:**
> - **PoC types** (Steel Thread, Pragmatic CA, Full CA) → See `/plan/_session-01-architectural-decisions.md` Q1
> - **Evolution paths** between PoC types → See Session 01 Q5
> - **Cross-cutting concerns placement** → See Session 01 Q2
>
> This file remains for detailed reference implementation analysis (calibration-service, creative-ai-poc, lean-clean-core-skeleton) but **defer to Session 01, and 02 for architectural decisions.**

## Executive Summary

This document synthesizes findings from three reference implementations to define a baseline PoC skeleton for the Lean-Clean Methodology. The analysis examines:

1. **calibration-service** - A mature, production-ready Clean Architecture implementation with comprehensive testing
2. **creative-ai-poc** - An AI-focused PoC with observability, vector stores, and blob storage
3. **lean-clean-core-skeleton** - A minimal steel thread implementation for rapid prototyping

## Clean Architecture Foundation

### Grounding & Inspiration

This project uses Clean Architecture patterns. Hereafter "CA" will be used for "Clean Architecture".

- Background article by Bob Martin https://blog.cleancoder.com/uncle-bob/2012/08/13/the-clean-architecture.html
- Inspiration from existing CA implementations
    - [CA with Python](https://medium.com/@shaliamekh/clean-architecture-with-python-d62712fd8d4f) - by R. Shaliamekh.
        - [clean-architecture-fastapi - by shaliamekh](https://github.com/shaliamekh/clean-architecture-fastapi/tree/main) -
          associated repo for blog post
    - [CA in Next.js](https://img.youtube.com/vi/jJVAla0dWJo/0.jpg)](https://www.youtube.com/watch?v=jJVAla0dWJo) -
      video describing CA
        - [nextjs-clean-architecture - by nikolovlazar](https://github.com/nikolovlazar/nextjs-clean-architecture/tree/main) -
          associated repo for video
- Other similar architectures to Clean Architecture
    - [Hexagonal Architecture](https://alistair.cockburn.us/hexagonal-architecture/) - (a.k.a. Ports and Adapters) by
      Alistair Cockburn
    - [Onion Architecture](https://jeffreypalermo.com/2008/07/the-onion-architecture-part-1/) - by Jeffrey Palermo
    - [Screaming Architecture](https://blog.cleancoder.com/uncle-bob/2011/09/30/Screaming-Architecture.html) - by Uncle
      Bob (the same guy behind Clean Architecture)

### Diagrams

- [The CA Diagram (sphere) - by Uncle Bob](https://blog.cleancoder.com/uncle-bob/images/2012-08-13-the-clean-architecture/CleanArchitecture.jpg) -
  by Uncle Bob
- [The CA Diagram (simplified) - by nikolovlazar](https://github.com/nikolovlazar/nextjs-clean-architecture/blob/main/assets/clean-architecture-diagram.jpg?raw=true)

#### Original

<img src="https://blog.cleancoder.com/uncle-bob/images/2012-08-13-the-clean-architecture/CleanArchitecture.jpg" alt="ERD" width="500">

#### "Simplified"

<img src="https://github.com/nikolovlazar/nextjs-clean-architecture/blob/main/assets/clean-architecture-diagram.jpg?raw=true" alt="ERD" width="500">

### Clean Architecture in Brief

> Great summary of how to approach these layers in-practice with the project.
> _Note: it's **heavily** borrowed from nikolovlazar... thank you!_

Clean Architecture is a _set of rules_ that help us structure our applications
in such way that they're easier to maintain and test, and their codebases are
predictable. It's like a common language that developers understand, regardless
of their technical backgrounds and programming language preferences.

Clean Architecture, and similar/derived architectures, all have the same goal -
_separation of concerns_. They introduce **layers** that bundle similar code
together. The "layering" helps us achieve important aspects in our codebase:

- **Independent of UI** - the business logic is not coupled with the UI
  framework that's being used (in this case Next.js). The same system can be
  used in a CLI application, without having to change the business logic or
  rules.
- **Independent of Database** - the database implementation/operations are
  isolated in their own layer, so the rest of the app does not care about which
  database is being used, but communicates using _Models_.
- **Independent of Frameworks** - the business rules and logic simply don't know
  anything about the outside world. They receive data defined with plain
  objects/dictionaries, _Services_ and _Repositories_ to define
  their own logic and functionality. This allows us to use frameworks as tools,
  instead of having to "mold" our system into their implementations and
  limitations. If we use Route Handlers in our app's _Drivers_, and want to refactor some of
  them to use a different framework implementation all we need to do is just invoke the specific
  _controllers_ in the new framework's manner, but the _core_
  business logic remains unchanged.
- **Testable** - the business logic and rules can easily be tested because it
  does not depend on the UI framework, or the database, or the web server, or
  any other external element that builds up our system.

Clean Architecture achieves this through defining a _dependency hierarchy_ -
layers depend only on layers **below them**, but not above.

## Analysis of Reference Implementations

### 1. calibration-service (Production-Grade CA)

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
├── entities/                # "Entities" or "core" layer
│   ├── exceptions.py           # Core custom exceptions
│   ├── models/                 # Domain models (Calibration, Tag)
│   └── value_objects/          # Value objects (CalbrationType, ISO8601Timestamp)
├── application/             # "Application" layer
│   ├── dtos/                   # Data Transfer Objects
│   ├── repositories/           # Repository interfaces only
│   ├── services/               # Service interfaces only
│   └── use_cases/              # Business logic implementation
│       ├── calibrations/
│       ├── tags/
│       └── exceptions.py
├── interface_adapters/      # "Interface" adapters layer from CA
│   ├── controllers/            # Controllers orchestrate use cases
│   │   ├── calibrations/
│   │   └── tags/
│   └── presenters/             # Convert domain models to output format
├── drivers/                 # "Frameworks & Drivers" layer
│   └── rest/                   # FastAPI framework
│       ├── routers/            # FastAPI request handlers
│       ├── schemas/            # Pydantic input/output schemas
│       ├── dependencies.py     # FastAPI middleware
│       ├── exception_handlers.py
│       └── main.py             # FastAPI entrypoint
└── infrastructure/          # "Infrastructure" layer
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
- Controllers perform authentication and validation, then orchestrate use cases
- Presenters convert domain entities to presentation format
- Multiple repository implementations enable testing and flexibility
- Exception handling at each layer with custom exceptions

**Testing Strategy:**

```text
tests/
├── unit/                    # Pure logic testing
│   ├── application/use_cases/
│   ├── infrastructure/repositories/
│   └── interface_adapters/controllers/
├── integration/             # Cross-layer testing
│   ├── drivers/rest/
│   └── repositories/
└── e2e/                     # Full system testing
```

### 2. creative-ai-poc (AI-Focused PoC)

**Strengths:**
- Simpler, more pragmatic layer naming (domain, application, infrastructure, drivers)
- Protocol-based ports for clean abstraction
- Built-in observability (OpenTelemetry, Phoenix)
- Multiple driver implementations (CLI, API, Streamlit UI)
- Crash reporting and telemetry integration
- Vector store and blob storage adapters

**Structure:**

```text
src/
├── domain/                  # Core business models
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
├── infrastructure/          # External integrations
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

### 3. lean-clean-core-skeleton (Minimal Steel Thread)

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
├── core/
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
- No layers for controllers, presenters, or repositories initially
- Direct use case invocation from CLI
- Stubs/placeholders for not-yet-implemented features
- Utility functions rather than full services
- Flat structure that can grow into layered architecture

## Recommended PoC Structure

### Decision Framework

**Choose structure based on PoC complexity:**

1. **Steel Thread (lean-clean-core-skeleton style)** → Use for Phase 3-4 initial prototyping
2. **Pragmatic CA (creative-ai-poc style)** → Use for Phase 5-6 implementations with AI/observability
3. **Full CA (calibration-service style)** → Use for Phase 6+ when building production-ready services

### Recommended Baseline: Pragmatic Clean Architecture

A balanced structure that's neither too minimal nor too complex:

```text
project-root/
├── README.md
├── pyproject.toml           # uv/pip dependencies
├── .env.example             # Environment template
├── Makefile                 # Common tasks (optional)
├── docker-compose.yaml      # Optional: Postgres, Weaviate, MinIO, Phoenix
│
├── docs/                    # Living documentation
│   ├── ARCHITECTURE.md
│   ├── DEVELOPER.md
│   └── DEPLOYMENT.md
│
├── schemas/                 # YAML schemas for validation
│   ├── playbook.schema.yaml
│   ├── use_case.schema.yaml
│   └── agent_task.schema.yaml
│
├── examples/                # Sample briefs and playbooks
│   ├── briefs/
│   │   └── sample_brief.yaml
│   └── playbooks/
│       └── sample_playbook.yaml
│
├── tools/                   # Automation utilities
│   ├── validate.py             # Schema validation CLI
│   ├── agent_watcher.py        # Optional: Phase 8 automation
│   └── init_db.py              # Optional: DB initialization
│
├── src/                     # Main application code
│   ├── domain/                 # INNER: Core business models
│   │   ├── models.py              # Domain entities (Brief, Product, Asset, etc.)
│   │   ├── value_objects.py       # Value objects (Aspect, Status, etc.)
│   │   └── exceptions.py          # Domain-specific exceptions
│   │
│   ├── application/            # MIDDLE: Business logic & ports
│   │   ├── ports/                 # Interfaces for external dependencies
│   │   │   ├── storage.py            # BlobStorePort, FileStorePort
│   │   │   ├── generation.py         # ImageGenPort, PromptEnhancerPort
│   │   │   ├── vector_store.py       # VectorStorePort
│   │   │   ├── composition.py        # CompositorPort
│   │   │   ├── validation.py         # CompliancePort, QualityCheckPort
│   │   │   └── telemetry.py          # TelemetryPort, TransactionPort
│   │   │
│   │   ├── use_cases/             # Business operations
│   │   │   ├── generate_creatives.py # Main creative generation flow
│   │   │   ├── validate_brief.py     # Brief validation logic
│   │   │   └── query_assets.py       # Asset retrieval/search
│   │   │
│   │   └── dtos.py                # Data Transfer Objects (optional)
│   │
│   ├── infrastructure/         # OUTER: Implementations
│   │   ├── config/                # Configuration management
│   │   │   ├── settings.py           # Environment variables, app config
│   │   │   └── database.py           # DB session factory (if needed)
│   │   │
│   │   ├── adapters/              # Port implementations
│   │   │   ├── storage/
│   │   │   │   ├── local_storage.py  # LocalFS implementation
│   │   │   │   └── s3_storage.py     # S3/MinIO implementation
│   │   │   ├── generation/
│   │   │   │   ├── openai_image_gen.py
│   │   │   │   ├── firefly_image_gen.py
│   │   │   │   └── mock_image_gen.py
│   │   │   ├── vector/
│   │   │   │   ├── weaviate_store.py
│   │   │   │   └── mock_vector_store.py
│   │   │   ├── composition/
│   │   │   │   └── pillow_compositor.py
│   │   │   └── validation/
│   │   │       └── simple_checks.py
│   │   │
│   │   ├── telemetry/             # Observability setup
│   │   │   ├── otel_setup.py         # OpenTelemetry initialization
│   │   │   ├── instrumentation.py    # Wrappers for spans/metrics
│   │   │   └── crash_reporter.py
│   │   │
│   │   └── persistence/           # Optional: DB models & repos
│   │       ├── orm_models.py         # SQLAlchemy models
│   │       └── repositories/
│   │
│   ├── interface_adapters/     # OUTER: Controllers & Presenters (optional for simple PoCs)
│   │   ├── controllers/
│   │   │   └── creative_controller.py
│   │   └── presenters/
│   │       └── creative_presenter.py
│   │
│   └── drivers/                # OUTERMOST: Entry points & frameworks
│       ├── cli/
│       │   ├── __main__.py           # CLI entry point (python -m src.drivers.cli)
│       │   └── commands.py           # Typer commands
│       ├── api/
│       │   ├── main.py               # FastAPI app
│       │   ├── routers/
│       │   ├── schemas.py            # Pydantic schemas
│       │   └── dependencies.py       # DI/middleware
│       └── ui/                    # Optional: Streamlit or web UI
│           └── streamlit/
│               └── app.py
│
└── tests/                   # Test suite
    ├── unit/                   # Pure logic tests
    │   ├── domain/
    │   ├── application/
    │   └── infrastructure/
    ├── integration/            # Cross-layer tests
    │   └── use_cases/
    └── e2e/                    # Full system tests
        └── test_cli_workflow.py
```

## Layer Responsibilities

### 1. Domain Layer (Innermost - Pure Business Logic)

**Purpose:** Define core business entities and rules that are independent of any technical implementation.

**Contents:**
- `models.py` - Domain entities as dataclasses or simple classes
- `value_objects.py` - Immutable values with validation (e.g., Aspect, Status)
- `exceptions.py` - Domain-specific exceptions (e.g., `InvalidBriefError`)

**Rules:**
- NO external dependencies (no FastAPI, no SQLAlchemy, no OpenAI)
- NO I/O operations
- Only imports from standard library or within domain layer
- Pure Python data structures

**Example:**
```python
# domain/models.py
from dataclasses import dataclass, field
from typing import List

@dataclass
class Brief:
    brand_id: str
    campaign_message: str
    products: List['Product']
    aspects: List[str] = field(default_factory=lambda: ["1:1", "9:16", "16:9"])

    def validate(self) -> None:
        if not self.products:
            raise InvalidBriefError("Brief must contain at least one product")
```

### 2. Application Layer (Use Cases & Ports)

**Purpose:** Define business operations and interfaces for external dependencies.

**Contents:**
- `use_cases/` - Orchestrate domain logic, use ports for I/O
- `ports/` - Protocol/ABC interfaces for infrastructure adapters
- `dtos.py` - Optional data transfer objects for cross-layer communication

**Rules:**
- MAY import from domain layer
- MUST define ports (interfaces) for external dependencies
- NO concrete infrastructure implementations (use dependency injection)
- NO web framework specifics (FastAPI, Flask)

**Example:**
```python
# application/ports/generation.py
from typing import Protocol

class ImageGenPort(Protocol):
    def generate_png(self, prompt: str, size: str) -> bytes: ...

# application/use_cases/generate_creatives.py
from domain.models import Brief
from application.ports.generation import ImageGenPort

def generate_creatives(
    brief: Brief,
    image_gen: ImageGenPort,  # Injected dependency
    output_path: str
) -> List[str]:
    # Business logic here
    image_bytes = image_gen.generate_png(prompt, size)
    # ...
```

### 3. Infrastructure Layer (Adapters & Services)

**Purpose:** Implement ports with concrete technologies, handle external system integration.

**Contents:**
- `adapters/` - Implementations of application ports
- `config/` - Configuration management, environment variables
- `telemetry/` - Observability setup (OpenTelemetry, Phoenix)
- `persistence/` - Optional: Database models and repositories

**Rules:**
- MUST implement application ports
- MAY import from domain and application layers
- Contains all external library dependencies (boto3, weaviate-client, openai, etc.)
- Handles error translation (convert library exceptions to domain exceptions)

**Example:**
```python
# infrastructure/adapters/generation/openai_image_gen.py
from openai import OpenAI
from application.ports.generation import ImageGenPort
from domain.exceptions import ImageGenerationError

class OpenAIImageGen:
    def __init__(self):
        self.client = OpenAI()

    def generate_png(self, prompt: str, size: str) -> bytes:
        try:
            response = self.client.images.generate(
                model="dall-e-3",
                prompt=prompt,
                size=size
            )
            # ... fetch and return bytes
        except OpenAIError as e:
            raise ImageGenerationError(f"Failed to generate image: {e}")
```

### 4. Interface Adapters Layer (Optional - Controllers & Presenters)

**Purpose:** Convert between external format (HTTP, CLI args) and internal format (domain models).

**When to use:**
- Complex validation/transformation logic
- Multiple entry points sharing logic
- Need to separate authorization from business logic

**When to skip:**
- Simple PoCs with only CLI driver
- Direct driver-to-use-case flow is sufficient

**Contents:**
- `controllers/` - Validate input, authorize, invoke use cases
- `presenters/` - Format domain data for output (JSON, tables, etc.)

**Example:**
```python
# interface_adapters/controllers/creative_controller.py
from application.use_cases.generate_creatives import generate_creatives
from domain.models import Brief

class CreativeController:
    def __init__(self, image_gen, storage, ...):
        self.image_gen = image_gen
        self.storage = storage

    def create_campaign(self, brief_data: dict) -> dict:
        # Validate input
        brief = Brief(**brief_data)
        brief.validate()

        # Invoke use case
        assets = generate_creatives(brief, self.image_gen, ...)

        # Present output
        return {"status": "success", "assets": [a.to_dict() for a in assets]}
```

### 5. Drivers Layer (Outermost - Entry Points)

**Purpose:** Framework-specific code, CLI/API/UI entry points, dependency wiring.

**Contents:**
- `cli/` - Typer/argparse CLI commands
- `api/` - FastAPI routes, schemas, middleware
- `ui/` - Streamlit pages or web UI components

**Rules:**
- MUST wire together all dependencies (adapters, use cases)
- Contains framework-specific code
- Thin layer - delegates to controllers or use cases

**Example:**
```python
# drivers/cli/__main__.py
import typer
from application.use_cases.generate_creatives import generate_creatives
from infrastructure.adapters.generation.openai_image_gen import OpenAIImageGen
from infrastructure.adapters.storage.local_storage import LocalStorage

app = typer.Typer()

@app.command()
def run(brief_path: str, output: str = "./out"):
    # Wire dependencies
    image_gen = OpenAIImageGen()
    storage = LocalStorage()

    # Load brief
    brief = load_brief(brief_path)

    # Execute use case
    assets = generate_creatives(brief, image_gen, output)

    typer.echo(f"Generated {len(assets)} assets")

if __name__ == "__main__":
    app()
```

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
# infrastructure/adapters/storage/s3_storage.py
import boto3

class S3BlobStore:
    def __init__(self, bucket: str):
        self.s3 = boto3.client('s3')
        self.bucket = bucket

    def put_bytes(self, key: str, data: bytes, content_type: str) -> str:
        self.s3.put_object(Bucket=self.bucket, Key=key, Body=data)
        return f"s3://{self.bucket}/{key}"
```

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
# infrastructure/adapters/generation/openai_image_gen.py
from openai import OpenAIError
from domain.exceptions import ImageGenerationError

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

## Testing Strategy

### Unit Tests (Fast, Isolated)

**Test domain logic:**

```python
# tests/unit/domain/test_models.py
from domain.models import Brief
from domain.exceptions import InvalidBriefError

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

### Integration Tests (Cross-Layer)

**Test adapter implementations:**

```python
# tests/integration/infrastructure/test_openai_adapter.py
import pytest
from infrastructure.adapters.generation.openai_image_gen import OpenAIImageGen

@pytest.mark.integration
def test_openai_generates_image():
    adapter = OpenAIImageGen()

    image_bytes = adapter.generate_png("A sunset over mountains", "1024x1024")

    assert len(image_bytes) > 0
    assert image_bytes.startswith(b'\x89PNG')  # PNG header
```

### E2E Tests (Full System)

**Test CLI workflows:**

```python
# tests/e2e/test_cli_workflow.py
import subprocess
import json

def test_cli_run_command_generates_assets(tmp_path):
    brief_path = tmp_path / "brief.yaml"
    brief_path.write_text("""
    brand_id: test-brand
    campaign_message: "Summer Sale"
    products:
      - id: prod-1
        name: "Widget"
    """)

    result = subprocess.run(
        ["python", "-m", "src.drivers.cli", "run", "--brief", str(brief_path)],
        capture_output=True
    )

    assert result.returncode == 0
    output = json.loads(result.stdout)
    assert len(output["assets"]) > 0
```

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
- **Streamlit** - Rapid prototyping dashboards
- **Gradio** - ML model interfaces

### CI/CD
- **GitHub Actions** - Validation, testing, deployment
- **pre-commit** - Git hooks for linting/formatting

## Migration Path: From Steel Thread to Full CA

### Phase 1: Minimal Steel Thread (P3-P4)

```text
app/
├── cli.py
├── core/
│   ├── models.py
│   └── use_cases.py
├── adapters/
│   └── storage_local.py
└── utils/
    └── brief_loader.py
```

### Phase 2: Add Ports & Infrastructure (P5)

```text
app/
├── cli.py
├── domain/              # Renamed from core
│   └── models.py
├── application/         # New layer
│   ├── ports/
│   └── use_cases/
├── infrastructure/      # Renamed from adapters
│   ├── config/
│   └── adapters/
└── utils/
```

### Phase 3: Add Multiple Drivers (P6)

```text
app/
├── domain/
├── application/
├── infrastructure/
└── drivers/            # New layer
    ├── cli/
    ├── api/
    └── ui/
```

### Phase 4: Add Interface Adapters (P6+)

```text
app/
├── domain/
├── application/
├── interface_adapters/  # New layer
│   ├── controllers/
│   └── presenters/
├── infrastructure/
└── drivers/
```

## Common Pitfalls & Solutions

### Pitfall 1: Over-engineering Early
**Problem:** Starting with full 5-layer CA for a 2-day PoC.

**Solution:** Use steel thread structure initially. Refactor to full CA only when:
- Multiple entry points needed (CLI + API + UI)
- Complex authorization/validation logic
- Team grows beyond 2-3 developers
- Preparing for production

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

## Summary & Recommendations

### For Initial PoC (P3-P4: Steel Thread)
**Use:** `lean-clean-core-skeleton` structure
- Minimal layers: `domain/`, `application/`, `infrastructure/`, `drivers/`
- Direct CLI → use case wiring
- Local filesystem only
- No observability initially

### For AI PoC (P5-P6: Implementation)
**Use:** `creative-ai-poc` structure (Pragmatic CA)
- Four layers: `domain/`, `application/`, `infrastructure/`, `drivers/`
- Ports & Adapters pattern
- Add observability (Phoenix + OpenTelemetry)
- Multiple storage options (local, S3, vector DB)

### For Production Service (P7+: Hardening)
**Use:** `calibration-service` structure (Full CA)
- Five layers: add `interface_adapters/`
- Comprehensive testing (unit, integration, e2e)
- Multiple repository implementations
- DTOs for cross-layer communication
- Exception handling at each layer

## Next Steps

1. **Review & Refine** - Discuss this structure with team
2. **Choose Starting Point** - Pick steel thread vs pragmatic CA based on PoC complexity
3. **Create Template** - Build project scaffolding script
4. **Update CLAUDE.md** - Finalize structure in main documentation
5. **Build First PoC** - Validate structure with real implementation

---

**Last Updated:** 2025-10-11
**Status:** Draft for Review
**Author:** Analysis synthesized from calibration-service, creative-ai-poc, lean-clean-core-skeleton
