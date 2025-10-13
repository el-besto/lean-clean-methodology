# Session 02: Framework Folder Structures

**Date:** 2025-10-13
**Purpose:** Define concrete folder architectures for Steel Thread, Pragmatic CA, and Full CA PoC types
**Status:** ✅ Complete - All 12 Architectural Decisions Resolved

---

## Overview

This document defines **Component B (Framework)** of the Lean-Clean Methodology: the folder structures that implement Clean Architecture principles for each PoC type.

**Key Design Goals:**

1. **Self-evident architecture** - Folder structure reveals layer boundaries
2. **Progressive refinement** - Steel Thread → Pragmatic CA → Full CA adds layers, doesn't rewrite
3. **Test-driven** - Test structure mirrors app structure
4. **Outside-In flow** - Acceptance tests with stakeholders → implementation with fakes → real adapters

**PoC Type Summary:**

| PoC Type | Timeline | Layers | Use Case | Evolution Trigger |
|----------|----------|--------|----------|-------------------|
| **Steel Thread** | 1-2 days | Minimal (use_cases + adapters + fakes + drivers) | Technical feasibility spike | Stakeholders approved, need production path |
| **Pragmatic CA** | 3-5 days | Production-ready subset (+ orchestrators + presenters + infrastructure) | Multi-stakeholder workshop output | Scaling to multi-team or complex domain |
| **Full CA** | Production | Comprehensive (+ separate entities, DTOs, events, CQRS, extracted ports) | Production-grade system | Already in production |

---

## Architectural Patterns Established

These patterns were resolved through collaborative decision-making (see `_session-02-all-decisions-resolved.md` for complete context):

### Pattern 1: Two-Step Evolution
**Applied to:** Ports, DTOs, Folder Organization

1. **Co-locate/Simplify** (Steel Thread/Pragmatic CA) - Keep related code together for speed
2. **Extract/Formalize** (Full CA) - Separate concerns when complexity justifies

**Examples:**
- **Ports:** `protocol.py` co-located with implementations → Extracted to `application/ports/`
- **DTOs:** None → Driver boundaries (`drivers/rest/schemas/`) → Use case boundaries (`use_cases/*/dtos.py`)
- **Drivers:** Flat → Organized subfolders → Production-ready with middleware

### Pattern 2: Enterprise PoC Reality
**Critical Discovery from Decision 9:**

Traditional PoCs: "just a CLI"
Enterprise PoCs: **CLI + UI from day 1**

**Why:** Multi-stakeholder workshops require:
- ✅ **CLI** (ALWAYS) - Automation, reproducibility, CI/CD
- ✅ **Streamlit UI** (ALWAYS) - Visual feedback for Creative Lead, Legal, Ad Ops, IT
- ⚠️ **FastAPI** (WHEN NEEDED) - Enterprise integrations, webhooks, external access

**Impact:** Even Steel Thread needs `drivers/` folder because enterprise PoCs require BOTH automation (CLI) AND visual feedback (Streamlit UI) from day 1. This is non-negotiable for multi-stakeholder workshops.

### Pattern 3: Fakes Have Two Forms
**From Decision 8 - Different Stakeholder Audiences:**

1. **Fake Adapters** → `app/adapters/`
   - External services (OpenAI, DAM, CRM, analytics)
   - **Audience:** Business stakeholders (Creative Lead, Ad Ops, Legal)

2. **In-Memory Repositories** → `app/infrastructure/repositories/`
   - Persistence (Postgres, MongoDB, in-memory)
   - **Audience:** DevOps/IT stakeholders

**Why separate:** Different mental models, different stakeholder concerns. Business stakeholders think "external services," IT thinks "databases and persistence."

### Pattern 4: Progressive = Additive (Not Transformative)
**Good evolution:**
```
Steel Thread:    app/adapters/fake_imagegen.py
Pragmatic CA:    app/adapters/imagegen/fake.py      # Added nesting
Full CA:         app/adapters/imagegen/fake.py      # Same location
```

**Bad evolution (avoided):**
```
Steel Thread:    tests/fakes/fake_imagegen.py       # Wrong layer
Pragmatic CA:    app/adapters/imagegen/fake.py      # REWORK: moved layers
Full CA:         app/infrastructure/external/...    # REWORK: moved again
```

**Key:** Files stay in same conceptual location; imports extend (add depth), don't transform (change root).

---

## 1. Pragmatic CA (The Sweet Spot)

**Target:** Enterprise PoCs with multi-stakeholder workshops, 3-5 day timeline, production-ready subset

### 1.1 Folder Structure

```
campaign-generator/                    # Root project directory
├─ app/                                # Application code
│  ├─ entities/                        # ═══ DOMAIN (innermost) ═══
│  │  ├─ campaign.py                   # Campaign, CreativeSet, Creative
│  │  ├─ brand_guidelines.py           # BrandGuidelines value object
│  │  └─ errors.py                     # DomainError, ValidationError
│  │
│  ├─ use_cases/                       # ═══ APPLICATION ═══
│  │  ├─ generate_campaign_uc.py       # GenerateCampaignUseCase (no DTOs - uses domain objects)
│  │  ├─ validate_brand_uc.py          # ValidateBrandComplianceUseCase
│  │  └─ emit_events_uc.py             # EmitCampaignEventsUseCase
│  │
│  ├─ interface_adapters/              # ═══ INTERFACE ADAPTERS ═══
│  │  ├─ orchestrators/
│  │  │  └─ campaign_orchestrator.py   # LocalizedCampaignOrchestrator
│  │  │                                # Converts: API DTO → domain objects
│  │  └─ presenters/
│  │     └─ campaign_presenter.py      # CampaignPresenter (to_response, attach_telemetry)
│  │
│  ├─ adapters/                        # ═══ INFRASTRUCTURE (External Services) ═══
│  │  ├─ imagegen/
│  │  │  ├─ protocol.py                # IImageGenerator (co-located port - Decision 12)
│  │  │  ├─ fake.py                    # FakeImageGenerator (Decision 8)
│  │  │  ├─ openai.py                  # OpenAIImageGenerator (real)
│  │  │  └─ firefly.py                 # FireflyImageGenerator (real)
│  │  ├─ storage/
│  │  │  ├─ protocol.py                # IStorageAdapter (co-located)
│  │  │  ├─ fake.py                    # FakeStorageAdapter
│  │  │  └─ s3.py                      # S3StorageAdapter (real)
│  │  └─ events/
│  │     ├─ protocol.py                # IEventTracker (co-located)
│  │     ├─ fake.py                    # FakeEventTracker
│  │     └─ amplitude.py               # AmplitudeEventTracker (real)
│  │
│  └─ infrastructure/                  # ═══ INFRASTRUCTURE (Persistence) ═══
│     ├─ orm_models/                   # SQLAlchemy models (if needed)
│     │  └─ campaign_orm.py            # CampaignModel (ORM, separate from entity)
│     └─ repositories/
│        └─ campaign/
│           ├─ protocol.py             # ICampaignRepository (co-located - Decision 12)
│           ├─ in_memory.py            # InMemoryCampaignRepository (Decision 8)
│           └─ postgres.py             # PostgresCampaignRepository (real)
│
├─ drivers/                            # ═══ DRIVERS (Entry Points) - Decision 9 ═══
│  ├─ cli/
│  │  ├─ __main__.py                   # python -m drivers.cli
│  │  ├─ commands.py                   # Typer commands
│  │  └─ dependencies.py               # DI module (manual wiring)
│  │
│  ├─ rest/                            # Decision 9: Added when enterprise integration
│  │  ├─ main.py                       # FastAPI app
│  │  ├─ routers/
│  │  │  └─ campaigns.py
│  │  ├─ schemas/                      # Decision 11: API DTOs here (Pydantic)
│  │  │  ├─ campaign_request.py       # CampaignRequest(BaseModel)
│  │  │  └─ campaign_response.py      # CampaignResponse(BaseModel)
│  │  └─ dependencies.py               # DI module
│  │
│  └─ ui/                              # Decision 9: UI ALWAYS for stakeholders
│     └─ streamlit/
│        ├─ app.py                     # Main Streamlit app
│        ├─ pages/
│        │  ├─ 1_generate.py          # Creative Lead workflow
│        │  └─ 2_approve.py           # Legal/Compliance workflow
│        └─ dependencies.py            # DI module
│
├─ tests/                              # ═══ TESTS (mirror app structure) ═══
│  ├─ acceptance/                      # Layer 1: Stakeholder contracts (ALWAYS use fakes)
│  │  └─ features/
│  │     └─ test_localized_campaign_feature.py
│  ├─ unit/                            # Layer 2: Isolated logic tests
│  │  ├─ use_cases/
│  │  │  ├─ test_generate_campaign.py
│  │  │  └─ test_validate_brand.py
│  │  ├─ orchestrators/
│  │  │  └─ test_campaign_orchestrator.py
│  │  └─ entities/
│  │     └─ test_campaign.py
│  └─ integration/                     # Layer 3: Real service tests
│     ├─ adapters/
│     │  ├─ test_openai_imagegen.py
│     │  └─ test_s3_storage.py
│     └─ repositories/
│        └─ test_postgres_campaign_repo.py
│
├─ .streamlit/                         # Streamlit configuration
│  └─ config.toml
│
├─ tools/                              # Development utilities
│  ├─ validate.py                      # Schema validation CLI
│  └─ init_db.py                       # Database initialization
│
├─ pyproject.toml                      # uv dependencies
├─ Makefile                            # Developer commands
└─ docker-compose.yaml                 # Local stack (optional)
```

### 1.2 Layer Responsibilities

**Mapping to nikolovlazar's Clean Architecture Pattern:**

| Folder | CA Layer | Responsibility | Depends On |
|--------|----------|----------------|------------|
| `entities/` | **Entities** (innermost) | Domain models, business rules, domain errors | Nothing |
| `use_cases/` | **Application** | Business logic, orchestrate entities, use protocols | Entities, Protocols (co-located) |
| `adapters/*/protocol.py` | **Application** (ports) | External service contracts (co-located with implementations) | Entities |
| `infrastructure/repositories/*/protocol.py` | **Application** (ports) | Persistence contracts (co-located with implementations) | Entities |
| `adapters/` | **Infrastructure** | External service implementations (OpenAI, CRM, fakes) | Protocols, External services |
| `infrastructure/` | **Infrastructure** | Persistence implementations (Postgres, in-memory) | Protocols, Databases |
| `interface_adapters/orchestrators/` | **Interface Adapters** | Feature orchestration (multi-use-case coordination) | Use Cases |
| `interface_adapters/presenters/` | **Interface Adapters** | Response formatting, telemetry, events | Entities, Use Case outputs |
| `drivers/` | **Frameworks & Drivers** | Entry points (CLI, REST, UI) | Orchestrators, Presenters |

**Dependency Rule:** All dependencies point inward (toward Entities). Outer layers never imported by inner layers.

**Ports Co-location (Decision 12):** In Pragmatic CA, ports (`protocol.py` files) are co-located with their implementations for simplicity. This reduces folder depth while maintaining Dependency Inversion Principle. In Full CA, ports are extracted to `application/ports/` when multiple implementations justify the separation.

### 1.3 Why This Structure?

**Q: Why separate `orchestrators/` from `use_cases/`?** (Decision 1)
- Orchestrators orchestrate **multiple use cases** for a feature
- Use cases implement **single business operations**
- Example: `LocalizedCampaignOrchestrator` coordinates `GenerateCampaignUseCase` + `ValidateBrandUseCase` + `EmitEventsUseCase`
- Uses terminology stakeholders understand: "orchestration" not "control"

**Q: Why split `adapters/` and `infrastructure/`?** (Decision 10)
- **Different stakeholder mental models:**
  - `adapters/` - External services (OpenAI, CRM, analytics) - Business stakeholders
  - `infrastructure/` - Persistence (databases, repositories) - IT/DevOps stakeholders
- Mirrors real-world organizational boundaries
- Fakes serve different purposes in each layer (see Decision 8)

**Q: Why co-locate ports (`protocol.py`) with implementations?** (Decision 12)
- **Pragmatic CA sweet spot:** Simplicity over formality
- Reduces folder depth (easier navigation)
- Still maintains Dependency Inversion Principle (use cases depend on protocols)
- Evolution path: Extract to `application/ports/` in Full CA when complexity justifies

**Q: Why put fakes in production code (`adapters/`, `infrastructure/`)?** (Decision 8)
- Fakes are **production code** (realistic behavior, no mocks), not test code (Axiom 3)
- Enables Outside-In TDD: write acceptance tests with fakes before real adapters exist
- Two types of fakes:
  1. **Fake Adapters** (`adapters/`) - External service doubles for workshops
  2. **In-Memory Repositories** (`infrastructure/repositories/`) - Database doubles for tests
- Different stakeholder audiences understand different fake types

**Q: Why `drivers/` folder from the start?** (Decision 9 - Enterprise PoC Reality)
- **Traditional PoCs:** Start with "just a CLI"
- **Enterprise PoCs:** Need CLI + UI from day 1
- **Why:** Multi-stakeholder workshops require:
  - CLI for automation, reproducibility, CI/CD
  - Streamlit UI for visual feedback (Creative Lead, Legal, Ad Ops, IT)
  - FastAPI when integrating with enterprise systems
- Even Steel Thread needs visual stakeholder feedback

**Q: Why no DTOs in use cases?** (Decision 11)
- **Pragmatic CA:** Use domain objects directly (speed over formality)
- DTOs only at driver boundaries (`drivers/rest/schemas/` for FastAPI validation)
- Evolution path: Add use case DTOs in Full CA when domain protection justifies

**Q: Why put telemetry/events in `presenters/`?**
- Telemetry is "formatting domain data for observability systems" (presentation concern)
- Keeps use cases clean of infrastructure
- Explicit methods (`attach_telemetry()`, `emit_events()`) are testable

---

## 2. Complete Feature Example: Localized Campaign Generation

This example demonstrates **Outside-In TDD** with stakeholders: acceptance test → implementation with fakes → real adapters.

### 2.1 Acceptance Test (Layer 1: Stakeholder Contract)

**File:** `tests/acceptance/features/test_localized_campaign_feature.py`

```python
"""
Feature: Generate compliant, localized creative variants at scale

Stakeholders:
- Creative Lead: Brand compliance, visual quality
- Ad Operations: Performance tracking, A/B testing
- IT: <3s latency, <$2 cost per campaign
- Legal: Content filtering, regulatory compliance

This test uses FAKES for all external dependencies to enable:
1. Workshop demos without expensive API calls
2. Fast feedback loops (<1s test execution)
3. Deterministic outputs for validation
"""

import pytest
from app.interface_adapters.orchestrators.campaign_orchestrator import LocalizedCampaignOrchestrator
from app.core.use_cases.generate_campaign import GenerateCampaignUseCase
from app.core.use_cases.validate_brand import ValidateBrandComplianceUseCase
from app.core.use_cases.emit_events import EmitCampaignEventsUseCase
from app.adapters.imagegen.fake_imagegen import FakeImageGenerator
from app.adapters.storage.fake_storage import FakeStorageAdapter
from app.adapters.events.fake_events import FakeEventTracker


@pytest.mark.feature("localized_campaign_generation")
class TestLocalizedCampaignFeature:
    """Maps to: LocalizedCampaignOrchestrator"""

    @pytest.fixture
    def orchestrator(self):
        """
        Wire up the full feature with FAKES.

        This is the dependency injection setup that would normally
        be done by a DI container in production.
        """
        # Infrastructure layer (fakes)
        image_generator = FakeImageGenerator()
        storage = FakeStorageAdapter()
        event_tracker = FakeEventTracker()

        # Application layer (use cases)
        generate_uc = GenerateCampaignUseCase(
            image_generator=image_generator,
            storage=storage
        )
        validate_uc = ValidateBrandComplianceUseCase()
        emit_events_uc = EmitCampaignEventsUseCase(
            event_tracker=event_tracker
        )

        # Interface adapter layer (orchestrator)
        return LocalizedCampaignOrchestrator(
            generate_campaign_uc=generate_uc,
            validate_brand_uc=validate_uc,
            emit_events_uc=emit_events_uc
        )

    async def test_generate_summer_sale_campaign_for_five_markets(self, orchestrator):
        """
        Given: Global summer sale campaign targeting EU and LATAM
        When: Generating localized variants for Instagram and Facebook
        Then: All stakeholder requirements met (brand compliant, <3s, filtered)
        """
        # ═══ ARRANGE ═══
        # Use domain language (not technical jargon)
        request = LocalizedCampaignRequest(
            global_message="Summer Sale: Up to 50% Off",
            markets=["UK", "DE", "FR", "MX", "BR"],
            platforms=["instagram", "facebook"],
            brand_guidelines={
                "primary_color": "#FF6B35",
                "logo_url": "https://brand.example.com/logo.png",
                "banned_terms": ["cheap", "discount"]
            },
            target_audience="18-35 year olds interested in fashion",
            budget_cap_usd=2.00  # IT requirement
        )

        # ═══ ACT ═══
        import time
        start = time.time()
        response = await orchestrator.generate_campaign(request)
        duration = time.time() - start

        # ═══ ASSERT: Stakeholder Success Criteria ═══

        # Creative Lead: Brand compliance
        assert all(
            creative_set.brand_compliant
            for creative_set in response.creative_sets.values()
        ), "All creative sets must pass brand compliance"

        # Creative Lead: Visual quality
        assert all(
            creative.image_url.endswith(".jpg")
            for creative_set in response.creative_sets.values()
            for creative in creative_set.creatives
        ), "All creatives must have valid image URLs"

        # Ad Operations: A/B testing variants
        assert len(response.creative_sets) == 5, "One creative set per market"
        for market in request.markets:
            assert market in response.creative_sets, f"Missing creative set for {market}"
            creative_set = response.creative_sets[market]
            assert len(creative_set.creatives) >= 2, "At least 2 variants per market for A/B testing"

        # IT: Latency
        assert duration < 3.0, f"Campaign generation took {duration:.2f}s (requirement: <3s)"

        # IT: Cost
        assert response.total_cost_usd < request.budget_cap_usd, \
            f"Cost ${response.total_cost_usd} exceeds budget ${request.budget_cap_usd}"

        # Legal: Content filtering
        banned_terms = request.brand_guidelines["banned_terms"]
        for creative_set in response.creative_sets.values():
            for creative in creative_set.creatives:
                assert not any(
                    term.lower() in creative.text.lower()
                    for term in banned_terms
                ), f"Banned term found in creative text: {creative.text}"

    async def test_reject_campaign_with_brand_violations(self, orchestrator):
        """
        Given: Campaign request with content violating brand guidelines
        When: Attempting to generate campaign
        Then: System rejects with clear brand violation errors
        """
        # ═══ ARRANGE ═══
        request = LocalizedCampaignRequest(
            global_message="Cheap deals on everything!",  # Contains banned term "cheap"
            markets=["US"],
            platforms=["instagram"],
            brand_guidelines={
                "banned_terms": ["cheap", "discount"]
            }
        )

        # ═══ ACT & ASSERT ═══
        with pytest.raises(BrandViolationError) as exc_info:
            await orchestrator.generate_campaign(request)

        assert "cheap" in str(exc_info.value).lower(), \
            "Error should clearly indicate which banned term was violated"
```

**Key Points:**

- **Readable by technical stakeholders** - Creative Lead, Ad Ops, IT, Legal can validate requirements
- **Uses fakes** - No OpenAI API calls, runs in <1s, deterministic
- **Domain language** - `LocalizedCampaignRequest`, not `dict` or raw JSON
- **BDD mindset** - Given/When/Then structure in docstrings

### 2.2 Orchestrator Implementation (Layer 2: Orchestration)

**File:** `app/interface_adapters/orchestrators/campaign_orchestrator.py`

```python
"""
LocalizedCampaignOrchestrator orchestrates the full localized campaign generation feature.

Responsibilities:
1. Coordinate multiple use cases (generate, validate, emit events)
2. Handle feature-level error handling
3. Format response via presenter

This maps to "Feature" in test language, "Orchestrator" in implementation language.
"""

from dataclasses import dataclass
from app.core.use_cases.generate_campaign import GenerateCampaignUseCase, GenerateCampaignCommand
from app.core.use_cases.validate_brand import ValidateBrandComplianceUseCase
from app.core.use_cases.emit_events import EmitCampaignEventsUseCase
from app.interface_adapters.presenters.campaign_presenter import CampaignPresenter


@dataclass
class LocalizedCampaignRequest:
    """API/CLI input (from Frameworks & Drivers layer)"""
    global_message: str
    markets: list[str]
    platforms: list[str]
    brand_guidelines: dict
    target_audience: str | None = None
    budget_cap_usd: float = 10.0


@dataclass
class LocalizedCampaignResponse:
    """API/CLI output (to Frameworks & Drivers layer)"""
    campaign_id: str
    creative_sets: dict[str, CreativeSetDTO]  # market -> CreativeSetDTO
    total_cost_usd: float
    duration_seconds: float


class LocalizedCampaignOrchestrator:
    """
    Feature orchestrator: coordinates generate → validate → emit events.

    This is the "Orchestrator" in Clean Architecture interface adapters layer.
    """

    def __init__(
        self,
        generate_campaign_uc: GenerateCampaignUseCase,
        validate_brand_uc: ValidateBrandComplianceUseCase,
        emit_events_uc: EmitCampaignEventsUseCase
    ):
        self.generate_campaign_uc = generate_campaign_uc
        self.validate_brand_uc = validate_brand_uc
        self.emit_events_uc = emit_events_uc

    async def generate_campaign(
        self,
        request: LocalizedCampaignRequest
    ) -> LocalizedCampaignResponse:
        """
        Orchestrate full campaign generation workflow.

        Flow:
        1. Validate brand guidelines upfront (fail fast)
        2. Generate creative variants for each market
        3. Emit analytics events
        4. Format response via presenter
        """
        import time
        start = time.time()

        # Step 1: Validate brand guidelines (fail fast)
        self.validate_brand_uc.validate_message(
            message=request.global_message,
            banned_terms=request.brand_guidelines.get("banned_terms", [])
        )

        # Step 2: Generate creative variants
        command = GenerateCampaignCommand(
            message=request.global_message,
            markets=request.markets,
            platforms=request.platforms,
            brand_guidelines=request.brand_guidelines,
            target_audience=request.target_audience,
            budget_cap_usd=request.budget_cap_usd
        )
        output = await self.generate_campaign_uc.execute(command)

        # Step 3: Emit analytics events
        await self.emit_events_uc.emit_campaign_generated(
            campaign_id=output.campaign_id,
            market_count=len(output.creative_sets),
            total_cost=output.total_cost_usd
        )

        # Step 4: Format response via presenter
        duration = time.time() - start
        return CampaignPresenter.to_response(output, duration)
```

**Key Points:**

- **Thin orchestration** - Delegates business logic to use cases
- **Fail fast** - Validates brand guidelines before expensive generation
- **Presenter for formatting** - Not responsible for response structure

### 2.3 Use Case Implementation (Layer 3: Business Logic)

**File:** `app/core/use_cases/generate_campaign.py`

```python
"""
GenerateCampaignUseCase implements core business logic for campaign generation.

Business rules:
- Generate 2-3 creative variants per market for A/B testing
- Localize message per market
- Stay within budget cap
- Ensure brand compliance post-generation

This is the "Application" layer in Clean Architecture.
"""

from dataclasses import dataclass
from app.core.interfaces.image_generator import IImageGenerator
from app.core.interfaces.storage import IStorageAdapter
from app.core.entities.campaign import Campaign, CreativeSet, Creative
from app.core.entities.errors import BudgetExceededError


@dataclass
class GenerateCampaignCommand:
    """Use case input (domain language)"""
    message: str
    markets: list[str]
    platforms: list[str]
    brand_guidelines: dict
    target_audience: str | None = None
    budget_cap_usd: float = 10.0


@dataclass
class GenerateCampaignOutput:
    """Use case output (domain language)"""
    campaign_id: str
    creative_sets: dict[str, CreativeSet]
    total_cost_usd: float


class GenerateCampaignUseCase:
    """
    Core business logic for campaign generation.

    Depends on interfaces (ports), not implementations (adapters).
    """

    def __init__(
        self,
        image_generator: IImageGenerator,
        storage: IStorageAdapter
    ):
        self.image_generator = image_generator
        self.storage = storage

    async def execute(self, command: GenerateCampaignCommand) -> GenerateCampaignOutput:
        """
        Execute campaign generation workflow.

        Business rules enforced:
        1. Generate 2-3 variants per market (A/B testing requirement)
        2. Localize message per market
        3. Respect budget cap (fail if exceeded)
        """
        campaign = Campaign.create(
            global_message=command.message,
            brand_guidelines=command.brand_guidelines
        )

        total_cost = 0.0
        creative_sets = {}

        for market in command.markets:
            # Business rule: Localize message
            localized_message = self._localize_message(command.message, market)

            # Business rule: Generate 2-3 variants per market
            creatives = []
            for variant_num in range(2):  # Simplified: always generate 2 variants
                prompt = self._build_prompt(
                    message=localized_message,
                    brand_guidelines=command.brand_guidelines,
                    target_audience=command.target_audience
                )

                # Generate image via port (interface)
                image_data = await self.image_generator.generate(
                    prompt=prompt,
                    size="1024x1024"
                )

                # Store image via port (interface)
                image_url = await self.storage.save(
                    path=f"campaigns/{campaign.campaign_id}/{market}/variant_{variant_num}.jpg",
                    content=image_data
                )

                creative = Creative(
                    text=localized_message,
                    image_url=image_url,
                    market=market,
                    variant_num=variant_num
                )
                creatives.append(creative)
                total_cost += self.image_generator.cost_per_image

            # Business rule: Respect budget cap
            if total_cost > command.budget_cap_usd:
                raise BudgetExceededError(
                    f"Cost ${total_cost:.2f} exceeds budget ${command.budget_cap_usd}"
                )

            creative_set = CreativeSet(
                market=market,
                creatives=creatives,
                brand_compliant=True  # Validated upstream
            )
            creative_sets[market] = creative_set

        return GenerateCampaignOutput(
            campaign_id=campaign.campaign_id,
            creative_sets=creative_sets,
            total_cost_usd=total_cost
        )

    def _localize_message(self, message: str, market: str) -> str:
        """
        Business logic: Localize message per market.

        Simplified: Real implementation would use translation service.
        """
        # Placeholder: In real system, this would call translation adapter
        return f"{message} ({market})"

    def _build_prompt(self, message: str, brand_guidelines: dict, target_audience: str | None) -> str:
        """Build image generation prompt from business requirements"""
        prompt = f"Create marketing image: {message}. "
        prompt += f"Brand colors: {brand_guidelines.get('primary_color', 'blue')}. "
        if target_audience:
            prompt += f"Target audience: {target_audience}."
        return prompt
```

**Key Points:**

- **Depends on interfaces** - `IImageGenerator`, `IStorageAdapter` (not concrete implementations)
- **Business rules enforced** - Budget cap, A/B variant count, localization
- **No infrastructure concerns** - No HTTP, no file paths, no vendor-specific APIs

### 2.4 Interfaces (Layer 4: Infrastructure Contracts)

**File:** `app/core/interfaces/image_generator.py`

```python
"""
IImageGenerator defines what the application NEEDS from image generation.

This is a "port" in Ports & Adapters (Hexagonal Architecture).
Defined in Application layer, implemented in Infrastructure layer.
"""

from abc import ABC, abstractmethod
from typing import Protocol


class IImageGenerator(Protocol):
    """
    Protocol for image generation adapters.

    Design note: Using Protocol instead of ABC for structural subtyping.
    Adapters don't need to explicitly inherit, just implement the interface.
    """

    cost_per_image: float  # Business requirement: track costs

    async def generate(self, prompt: str, size: str) -> bytes:
        """
        Generate image from prompt.

        Args:
            prompt: Text description of desired image
            size: Image dimensions (e.g., "1024x1024")

        Returns:
            Raw image bytes (JPEG format)

        Raises:
            ImageGenerationError: If generation fails
        """
        ...
```

**Key Points:**

- **Defined by application needs** - Not by what OpenAI/Firefly provide
- **Protocol not ABC** - Structural subtyping (more Pythonic)
- **Business concerns** - `cost_per_image` for budget tracking

### 2.5 Fake Adapter (Layer 5: Test Double)

**File:** `app/adapters/imagegen/fake_imagegen.py`

```python
"""
FakeImageGenerator provides realistic fake behavior for testing and workshops.

This is NOT a mock. It's a simplified implementation with realistic behavior:
- Returns deterministic fake images
- Simulates cost tracking
- Fast (<10ms) for rapid feedback

Used in:
1. Acceptance tests (workshop demos)
2. Local development (no API keys needed)
3. CI/CD (no external dependencies)
"""

import asyncio
from app.core.interfaces.image_generator import IImageGenerator


class FakeImageGenerator:
    """
    Fake implementation of IImageGenerator.

    Design note: This is production code (in adapters/), not test code (in tests/).
    It's a first-class adapter that implements the same interface as real adapters.
    """

    cost_per_image: float = 0.02  # Match OpenAI pricing

    def __init__(self, latency_ms: int = 100):
        """
        Args:
            latency_ms: Simulate network latency for realistic timing tests
        """
        self.latency_ms = latency_ms
        self.generation_count = 0  # Track usage for testing

    async def generate(self, prompt: str, size: str) -> bytes:
        """
        Generate deterministic fake image.

        Returns a simple colored rectangle as JPEG bytes.
        The color is deterministic based on prompt hash (stable across runs).
        """
        # Simulate network latency
        await asyncio.sleep(self.latency_ms / 1000.0)

        self.generation_count += 1

        # Generate deterministic fake image
        # Real implementation would use PIL to create colored rectangle
        fake_image = self._create_fake_image(prompt, size)
        return fake_image

    def _create_fake_image(self, prompt: str, size: str) -> bytes:
        """
        Create deterministic fake JPEG.

        Simplified: Returns placeholder bytes.
        Real implementation would use PIL to create actual image.
        """
        # Deterministic "hash" from prompt (for stable tests)
        color_code = sum(ord(c) for c in prompt) % 256

        # Placeholder: Real implementation would use PIL
        # from PIL import Image
        # img = Image.new('RGB', parse_size(size), color=(color_code, 100, 150))
        # return img.tobytes('jpeg')

        return f"FAKE_IMAGE_{color_code}_{size}".encode()
```

**Key Points:**

- **Production code** - In `adapters/`, not `tests/`
- **Realistic behavior** - Simulates latency, costs, deterministic outputs
- **No mocking framework** - Simple class implementing interface
- **Enables Outside-In TDD** - Write acceptance tests before real adapters exist

### 2.6 Outside-In TDD Workflow

This example demonstrates the **Outside-In workflow** for multi-stakeholder PoCs:

**Workshop Day 1: Write Acceptance Test with Stakeholders**
1. Gather Creative Lead, Ad Ops, IT, Legal in room
2. Walk through `test_generate_summer_sale_campaign_for_five_markets()`
3. Stakeholders validate requirements in code:
   - Creative Lead: "Yes, all creatives must pass brand compliance"
   - IT: "Yes, <3s and <$2 are our SLAs"
   - Legal: "Yes, banned terms must be filtered"
4. Run test → RED (nothing implemented yet)
5. **Stakeholders approve the test as specification**

**Workshop Day 2: Implement with Fakes**
1. Implement `LocalizedCampaignOrchestrator` (orchestration)
2. Implement `GenerateCampaignUseCase` (business logic)
3. Implement `FakeImageGenerator`, `FakeStorageAdapter` (test doubles)
4. Run test → GREEN (all fakes)
5. **Demo to stakeholders** - Full feature works in <1s, no API keys

**Post-Workshop: Implement Real Adapters**
1. Implement `OpenAIImageGenerator` (real)
2. Implement `S3StorageAdapter` (real)
3. **Acceptance test still GREEN** (contract unchanged)
4. Add integration tests for real adapters

**Key Insight:** The acceptance test is the **stakeholder contract**. It doesn't change when you swap fakes for real adapters. This is the power of Dependency Inversion Principle.

---

## 3. Steel Thread (Minimal for Spikes)

**Target:** Technical feasibility spikes, 1-2 day timeline, single happy path

### 3.1 Folder Structure

```
campaign-generator-spike/
├─ app/
│  ├─ entities/                        # Domain models (simple dataclasses)
│  │  └─ campaign.py
│  ├─ use_cases/                       # Flat business logic (no DTOs)
│  │  └─ generate_campaign_uc.py       # Single-file use case
│  └─ adapters/                        # Infrastructure (Decision 8: fakes + real)
│     ├─ imagegen/
│     │  ├─ protocol.py                # IImageGenerator (co-located - Decision 12)
│     │  ├─ fake.py                    # FakeImageGenerator (Decision 8)
│     │  └─ openai.py                  # Real implementation
│     └─ storage/
│        ├─ protocol.py                # IStorageAdapter (co-located)
│        ├─ fake.py                    # FakeStorageAdapter
│        └─ local.py                   # LocalStorageAdapter
│
├─ drivers/                            # Decision 9: YES, even for Steel Thread
│  ├─ cli.py                           # Automation, reproducibility (ALWAYS)
│  └─ ui/
│     └─ streamlit_app.py              # Stakeholder visual demos (ALWAYS)
│
├─ tests/
│  ├─ acceptance/                      # Decision 7: Three-layer structure
│  │  └─ features/
│  │     └─ test_campaign_feature.py  # Always fakes
│  ├─ unit/
│  │  └─ use_cases/
│  └─ integration/
│     └─ adapters/
│
├─ .streamlit/                         # Streamlit configuration
│  └─ config.toml
│
├─ pyproject.toml
└─ README.md
```

**Key Insight (Decision 9 - Enterprise PoC Reality):**

Unlike traditional "CLI-only" spikes, **enterprise PoCs need visual feedback from day 1**. Even Steel Thread includes:
- **CLI** - For automation and reproducibility
- **Streamlit UI** - For stakeholder visual feedback in workshops

This isn't over-engineering—it's recognizing that enterprise AI PoCs involve stakeholders who need to SEE the output, not just read logs.

### 3.2 Key Differences from Pragmatic CA

| Aspect | Steel Thread | Pragmatic CA |
|--------|--------------|--------------|
| **Orchestrators** | ❌ None - drivers call use case directly | ✅ Yes - orchestrate multiple use cases |
| **Presenters** | ❌ None - use case returns domain objects | ✅ Yes - format responses + telemetry |
| **Entities** | ✅ Minimal - simple dataclasses | ✅ Yes - richer domain models |
| **Infrastructure Split** | ❌ No - only `adapters/` | ✅ Yes - `adapters/` + `infrastructure/` |
| **Ports** | ✅ Co-located `protocol.py` | ✅ Co-located `protocol.py` (same) |
| **DTOs** | ❌ None - domain objects everywhere | ⚠️ Minimal - only at `drivers/rest/schemas/` |
| **Drivers** | ✅ Flat - `cli.py` + `ui/streamlit_app.py` | ✅ Organized - `drivers/{cli,rest,ui}/` |
| **Test Layers** | ✅ Three layers (flat) | ✅ Three layers (organized) |

### 3.3 Example: Steel Thread Use Case

**File:** `app/use_cases/generate_campaign_uc.py`

```python
"""
Steel Thread: Single-file use case with minimal ceremony.

Trade-offs:
- ✅ Fast to write (1-2 hours)
- ✅ Proves technical feasibility
- ✅ Uses protocols (Dependency Inversion from day 1)
- ❌ No orchestrators (drivers call use case directly)
- ❌ No presenters (use case returns domain objects)
- ❌ No DTOs (domain objects everywhere)
"""

from dataclasses import dataclass
from app.entities.campaign import Campaign
from app.adapters.imagegen.protocol import IImageGenerator
from app.adapters.storage.protocol import IStorageAdapter


@dataclass
class GenerateCampaignInput:
    """Use case input (domain object, not DTO)"""
    message: str
    markets: list[str]


class GenerateCampaignUseCase:
    """
    Steel Thread use case: Depends on protocols (DIP), not concrete implementations.
    """

    def __init__(
        self,
        image_gen: IImageGenerator,  # Protocol (Decision 12: co-located)
        storage: IStorageAdapter     # Protocol (Decision 12: co-located)
    ):
        self.image_gen = image_gen
        self.storage = storage

    async def execute(self, input: GenerateCampaignInput) -> Campaign:
        """
        Steel Thread: Minimal implementation to prove feasibility.

        Returns domain object (not DTO, Decision 11).
        """
        campaign = Campaign.create(message=input.message)

        for market in input.markets:
            image_data = await self.image_gen.generate(
                prompt=f"{input.message} for {market}",
                size="1024x1024"
            )
            image_url = await self.storage.save(
                path=f"campaigns/{campaign.id}/{market}.jpg",
                content=image_data
            )
            campaign.add_creative(market=market, image_url=image_url)

        return campaign
```

**Key Differences from "Pure" Steel Thread:**
- ✅ Uses protocols (`IImageGenerator`) - Maintains DIP even in spikes
- ✅ Returns domain object (`Campaign`) - Not raw dict
- ✅ Constructor injection - Enables testing with fakes
- ❌ No orchestrator - Drivers call use case directly
- ❌ No DTOs - Domain objects cross all boundaries

### 3.4 Example: Steel Thread Test

**File:** `tests/acceptance/features/test_campaign_feature.py`

```python
"""
Steel Thread: Acceptance test with fakes (Outside-In TDD).
"""

import pytest
from app.use_cases.generate_campaign_uc import GenerateCampaignUseCase, GenerateCampaignInput
from app.adapters.imagegen.fake import FakeImageGenerator
from app.adapters.storage.fake import FakeStorageAdapter


@pytest.fixture
def use_case():
    """Wire up use case with fakes (manual DI)"""
    return GenerateCampaignUseCase(
        image_gen=FakeImageGenerator(),
        storage=FakeStorageAdapter()
    )


@pytest.mark.asyncio
async def test_generate_campaign_for_multiple_markets(use_case):
    """
    Given: Campaign message and target markets
    When: Generating campaign with fakes (fast, deterministic)
    Then: Campaign created with creatives for all markets
    """
    # ARRANGE
    input = GenerateCampaignInput(
        message="Summer Sale: Up to 50% Off",
        markets=["US", "UK", "DE"]
    )

    # ACT
    campaign = await use_case.execute(input)

    # ASSERT
    assert campaign.message == "Summer Sale: Up to 50% Off"
    assert len(campaign.creatives) == 3
    assert all(market in [c.market for c in campaign.creatives] for market in input.markets)
    # Verify image URLs generated
    assert all(c.image_url.startswith("campaigns/") for c in campaign.creatives)
```

**Key Points:**
- Uses fakes (Decision 8) - Fast, deterministic, no API calls
- Manual DI in fixture - No DI container needed for spikes
- Domain-driven assertions - Validates Campaign entity
- Given/When/Then structure - Readable by stakeholders

### 3.5 When to Use Steel Thread

**Use Steel Thread when:**
- ✅ Proving technical feasibility (e.g., "Can we integrate OpenAI API?")
- ✅ Time-boxed spike (1-2 days max)
- ✅ Single developer or small team
- ✅ Initial stakeholder validation (show visual results quickly)

**Don't use Steel Thread when:**
- ❌ Multi-stakeholder workshop (need orchestrators to coordinate complex workflows)
- ❌ Multiple use cases to coordinate (need orchestrators)
- ❌ Production path required (need infrastructure/ split, DTOs, presenters)
- ❌ Enterprise integrations needed (need organized drivers with FastAPI)

→ In these cases, start with **Pragmatic CA** instead.

**Steel Thread Evolution Strategy:**
If stakeholders approve the spike → Evolve to Pragmatic CA (see Migration Checklist in Section 5.2)

---

## 4. Full CA (Production-Grade)

**Target:** Production systems, multi-team, complex domain, horizontal scalability

### 4.1 Folder Structure

```
campaign-generator-production/
├─ app/
│  ├─ entities/                        # ═══ DOMAIN (Rich aggregate roots) ═══
│  │  ├─ campaign/
│  │  │  ├─ campaign.py                # Campaign aggregate root
│  │  │  ├─ creative_set.py            # CreativeSet entity
│  │  │  ├─ creative.py                # Creative value object
│  │  │  └─ events.py                  # CampaignCreated, CreativeGenerated domain events
│  │  ├─ brand/
│  │  │  ├─ brand.py                   # Brand aggregate root
│  │  │  └─ guidelines.py              # BrandGuidelines value object
│  │  └─ shared/
│  │     ├─ errors.py                  # Domain errors hierarchy
│  │     └─ value_objects.py           # Money, MarketCode, PlatformType
│  │
│  ├─ application/                     # ═══ APPLICATION (Decision 2: Organized under application/) ═══
│  │  ├─ use_cases/                    # Use cases with DTOs (Decision 11)
│  │  │  ├─ generate_campaign/
│  │  │  │  ├─ use_case.py            # GenerateCampaignUseCase
│  │  │  │  └─ dtos.py                # Decision 11: Use case DTOs
│  │  │  │                             # GenerateCampaignInput, GenerateCampaignOutput
│  │  │  ├─ validate_brand/
│  │  │  │  ├─ use_case.py
│  │  │  │  └─ dtos.py
│  │  │  └─ emit_events/
│  │  │     ├─ use_case.py
│  │  │     └─ dtos.py
│  │  │
│  │  └─ ports/                        # Decision 12: Ports extracted here (from co-located)
│  │     ├─ services/                  # External service contracts
│  │     │  ├─ image_generator.py     # IImageGenerator protocol
│  │     │  ├─ storage_adapter.py     # IStorageAdapter protocol
│  │     │  ├─ crm_client.py          # ICrmClient protocol
│  │     │  └─ analytics_client.py    # IAnalyticsClient protocol
│  │     └─ repositories/              # Persistence contracts
│  │        ├─ campaign_repository.py  # ICampaignRepository protocol
│  │        └─ creative_repository.py  # ICreativeRepository protocol
│  │
│  ├─ interface_adapters/              # ═══ INTERFACE ADAPTERS ═══
│  │  ├─ orchestrators/
│  │  │  └─ campaign_orchestrator.py   # LocalizedCampaignOrchestrator
│  │  │                                # Converts: API DTO → Use Case DTO → domain
│  │  └─ presenters/
│  │     └─ campaign_presenter.py      # CampaignPresenter (telemetry, formatting)
│  │
│  ├─ adapters/                        # ═══ INFRASTRUCTURE (External Services) - Implements ports/services/* ═══
│  │  ├─ imagegen/
│  │  │  ├─ fake.py                    # No protocol.py (moved to application/ports/)
│  │  │  ├─ openai.py
│  │  │  └─ firefly.py
│  │  ├─ storage/
│  │  │  ├─ fake.py
│  │  │  ├─ s3.py
│  │  │  └─ azure_blob.py
│  │  ├─ crm/
│  │  │  ├─ fake_salesforce.py
│  │  │  └─ salesforce_client.py
│  │  └─ analytics/
│  │     ├─ fake_warehouse.py
│  │     └─ snowflake_client.py
│  │
│  └─ infrastructure/                  # ═══ INFRASTRUCTURE (Persistence) - Implements ports/repositories/* ═══
│     ├─ orm_models/                   # SQLAlchemy models (separate from domain entities)
│     │  ├─ campaign_orm.py
│     │  └─ creative_orm.py
│     ├─ repositories/
│     │  ├─ campaign/
│     │  │  ├─ in_memory.py           # No protocol.py (moved to application/ports/)
│     │  │  ├─ postgres.py
│     │  │  └─ mongodb.py
│     │  └─ creative/
│     │     ├─ in_memory.py
│     │     └─ postgres.py
│     └─ events/
│        ├─ fake_event_bus.py
│        └─ kafka_event_bus.py
│
├─ drivers/                            # ═══ DRIVERS (Entry Points) - Decision 9: Production-ready ═══
│  ├─ cli/
│  │  ├─ __main__.py
│  │  ├─ commands/                     # Organized commands
│  │  │  ├─ generate.py
│  │  │  ├─ validate.py
│  │  │  └─ approve.py
│  │  ├─ formatters/                   # Output formatters
│  │  │  ├─ table.py
│  │  │  └─ json.py
│  │  └─ dependencies.py               # DI container
│  │
│  ├─ rest/                            # FastAPI with production middleware
│  │  ├─ main.py
│  │  ├─ routers/
│  │  │  ├─ campaigns.py
│  │  │  ├─ approvals.py
│  │  │  └─ webhooks.py
│  │  ├─ schemas/                      # Decision 11: API DTOs here (Pydantic)
│  │  │  ├─ campaign_request.py       # CampaignRequest(BaseModel)
│  │  │  ├─ campaign_response.py      # CampaignResponse(BaseModel)
│  │  │  └─ approval_request.py
│  │  ├─ middleware/
│  │  │  ├─ auth.py                    # Authentication
│  │  │  ├─ logging.py                 # Request logging
│  │  │  └─ telemetry.py               # OpenTelemetry
│  │  ├─ exception_handlers.py
│  │  └─ dependencies.py               # DI container
│  │
│  └─ ui/                              # Streamlit with production features
│     └─ streamlit/
│        ├─ app.py
│        ├─ pages/
│        │  ├─ 1_generate.py          # Creative Lead
│        │  ├─ 2_approve.py           # Legal/Compliance
│        │  ├─ 3_monitor.py           # IT
│        │  └─ 4_compliance.py        # Legal
│        ├─ components/
│        │  ├─ campaign_card.py
│        │  └─ approval_widget.py
│        └─ dependencies.py            # DI container
│
├─ tests/
│  ├─ acceptance/                      # Feature-level tests (always fakes)
│  │  └─ features/
│  ├─ unit/                            # Isolated logic tests
│  │  ├─ entities/
│  │  ├─ use_cases/
│  │  ├─ orchestrators/
│  │  └─ presenters/
│  ├─ integration/                     # Real service tests
│  │  ├─ adapters/
│  │  └─ repositories/
│  └─ e2e/                             # Full system tests
│     └─ test_production_workflow.py
│
├─ .streamlit/
│  └─ config.toml
│
├─ alembic/                            # Database migrations
│
├─ pyproject.toml
├─ docker-compose.yaml
├─ docker-compose.prod.yaml
├─ Makefile
└─ k8s/                                # Kubernetes manifests
   ├─ deployment.yaml
   └─ service.yaml
```

### 4.2 Key Differences from Pragmatic CA

| Aspect | Pragmatic CA | Full CA |
|--------|--------------|---------|
| **Entities** | Simple dataclasses in `entities/` | Rich aggregate roots with domain events |
| **Use Cases** | Single file per use case (no DTOs) | Folder per use case (`use_case.py` + `dtos.py`) |
| **Ports Location** | Co-located `protocol.py` with implementations | Extracted to `application/ports/` (Decision 12) |
| **DTOs** | Minimal (only `drivers/rest/schemas/`) | Comprehensive (`drivers/rest/schemas/` + `use_cases/*/dtos.py`) |
| **Application Layer** | Flat `entities/` + `use_cases/` | Organized under `application/` (Decision 2) |
| **Infrastructure** | Split (`adapters/` + `infrastructure/`) | Same split (maintained) |
| **Repositories** | In `infrastructure/repositories/` with protocol | Same, but protocol extracted to `application/ports/repositories/` |
| **ORM Models** | ⚠️ Can be same as entities (pragmatic) | ✅ Separate entities from ORM (mapping layer) |
| **Drivers** | Organized (`cli/`, `rest/`, `ui/`) | Same + production middleware |
| **Events** | Emitted via presenters | Domain events + event sourcing |
| **Deployment** | Docker Compose | Kubernetes + horizontal scaling |

### 4.3 Example: Separate Domain Entity from ORM Model

**Problem:** Pragmatic CA often uses ORM models as domain entities. This couples domain logic to infrastructure.

**Full CA Solution:** Separate entities from ORM, use repository for mapping.

**File:** `app/core/entities/campaign/campaign.py` (Domain Entity)

```python
"""
Campaign domain entity (aggregate root).

This is pure business logic, no ORM annotations, no infrastructure concerns.
"""

from dataclasses import dataclass, field
from datetime import datetime
from uuid import UUID, uuid4
from app.core.entities.campaign.creative_set import CreativeSet
from app.core.entities.campaign.events import CampaignCreated, CreativeGenerated
from app.core.entities.shared.errors import BrandViolationError


@dataclass
class Campaign:
    """
    Campaign aggregate root.

    Responsibilities:
    - Enforce brand compliance invariants
    - Coordinate creative set creation
    - Emit domain events for state changes
    """
    campaign_id: UUID = field(default_factory=uuid4)
    global_message: str = ""
    brand_guidelines: dict = field(default_factory=dict)
    creative_sets: dict[str, CreativeSet] = field(default_factory=dict)
    created_at: datetime = field(default_factory=datetime.utcnow)
    domain_events: list = field(default_factory=list, repr=False)  # Event sourcing

    @classmethod
    def create(cls, global_message: str, brand_guidelines: dict) -> "Campaign":
        """
        Factory method for creating campaigns.

        Enforces invariants and emits CampaignCreated event.
        """
        # Invariant: Message must not be empty
        if not global_message.strip():
            raise BrandViolationError("Campaign message cannot be empty")

        # Invariant: Must have brand guidelines
        if not brand_guidelines:
            raise BrandViolationError("Brand guidelines required")

        campaign = cls(
            global_message=global_message,
            brand_guidelines=brand_guidelines
        )

        # Emit domain event
        campaign.domain_events.append(
            CampaignCreated(
                campaign_id=campaign.campaign_id,
                message=global_message,
                timestamp=campaign.created_at
            )
        )

        return campaign

    def add_creative_set(self, creative_set: CreativeSet):
        """
        Add creative set to campaign.

        Enforces invariants and emits CreativeGenerated events.
        """
        # Invariant: No duplicate markets
        if creative_set.market in self.creative_sets:
            raise BrandViolationError(
                f"Creative set for {creative_set.market} already exists"
            )

        self.creative_sets[creative_set.market] = creative_set

        # Emit domain events
        for creative in creative_set.creatives:
            self.domain_events.append(
                CreativeGenerated(
                    campaign_id=self.campaign_id,
                    market=creative_set.market,
                    creative_id=creative.creative_id,
                    timestamp=datetime.utcnow()
                )
            )
```

**File:** `app/adapters/db/models.py` (ORM Model)

```python
"""
SQLAlchemy ORM models.

These are SEPARATE from domain entities to avoid coupling domain to infrastructure.
"""

from sqlalchemy import Column, String, DateTime, ForeignKey
from sqlalchemy.dialects.postgresql import UUID, JSONB
from sqlalchemy.orm import relationship
from datetime import datetime
import uuid


class CampaignModel(Base):
    """
    ORM model for campaigns table.

    Design note: This is an infrastructure concern, NOT a domain entity.
    Repository handles mapping between CampaignModel ↔ Campaign entity.
    """
    __tablename__ = "campaigns"

    id = Column(UUID(as_uuid=True), primary_key=True, default=uuid.uuid4)
    global_message = Column(String, nullable=False)
    brand_guidelines = Column(JSONB, nullable=False)
    created_at = Column(DateTime, default=datetime.utcnow)

    # Relationships
    creative_sets = relationship("CreativeSetModel", back_populates="campaign")
```

**File:** `app/adapters/db/repositories/campaign_repo.py` (Repository)

```python
"""
Campaign repository: maps between domain entities and ORM models.
"""

from app.core.interfaces.repositories.campaign_repository import ICampaignRepository
from app.core.entities.campaign.campaign import Campaign
from app.adapters.db.models import CampaignModel, CreativeSetModel


class CampaignRepository(ICampaignRepository):
    """
    Maps Campaign entity ↔ CampaignModel ORM.

    This is the "mapping layer" that keeps domain clean of ORM concerns.
    """

    def __init__(self, session):
        self.session = session

    async def save(self, campaign: Campaign):
        """
        Map domain entity → ORM model, persist to database.
        """
        # Check if exists
        model = self.session.query(CampaignModel).filter_by(
            id=campaign.campaign_id
        ).first()

        if model is None:
            # Create new ORM model
            model = CampaignModel(
                id=campaign.campaign_id,
                global_message=campaign.global_message,
                brand_guidelines=campaign.brand_guidelines,
                created_at=campaign.created_at
            )
            self.session.add(model)
        else:
            # Update existing model
            model.global_message = campaign.global_message
            model.brand_guidelines = campaign.brand_guidelines

        await self.session.commit()

    async def get_by_id(self, campaign_id: UUID) -> Campaign | None:
        """
        Map ORM model → domain entity, load from database.
        """
        model = await self.session.query(CampaignModel).filter_by(
            id=campaign_id
        ).first()

        if model is None:
            return None

        # Reconstruct domain entity from ORM model
        campaign = Campaign(
            campaign_id=model.id,
            global_message=model.global_message,
            brand_guidelines=model.brand_guidelines,
            created_at=model.created_at
        )

        # Load creative sets (simplified)
        for cs_model in model.creative_sets:
            creative_set = self._map_creative_set(cs_model)
            campaign.creative_sets[creative_set.market] = creative_set

        return campaign
```

**Key Benefits:**

- **Domain stays pure** - No SQLAlchemy imports in entities
- **Easier testing** - Domain entities don't need database
- **Swap databases** - Can switch from Postgres → MongoDB without changing domain
- **Performance tuning** - Can optimize ORM queries without changing domain logic

### 4.4 When to Use Full CA

**Use Full CA when:**

- ✅ Multi-team coordination (need stable interfaces)
- ✅ Complex domain logic (rich aggregates, domain events)
- ✅ Production deployment (horizontal scaling, event sourcing)
- ✅ Long-term maintenance (multiple years)

**Don't use Full CA when:**

- ❌ PoC or spike (over-engineering)
- ❌ Single team, simple domain
- ❌ Time-boxed project (<3 months)

→ In these cases, use **Pragmatic CA** and evolve to Full CA only if needed.

---

## 5. Evolution Path: How PoC Types Relate

### 5.1 Progressive Refinement (Not Rewrites)

**Key Principle:** All three PoC types use the **same testing philosophy** (Outside-In with fakes). Tests written in Steel Thread still pass in Full CA because the **stakeholder contract doesn't change**.

```text
Steel Thread                  Pragmatic CA                  Full CA
(Prove feasibility)          (Production-ready subset)     (Production-grade)
──────────────────────────────────────────────────────────────────────────

use_cases/       ──add──►    use_cases/           ──refine──►  use_cases/
  generate.py                  generate.py                       generate/
                                                                   command.py
                                                                   use_case.py
                                                                   dtos.py

adapters/        ──same──►    adapters/            ──add──►     adapters/
  fake_imagegen.py             fake_imagegen.py                  fake_imagegen.py
                               openai.py                         openai.py
                                                                 firefly.py

                 ──add──►     orchestrators/         ──refine──►  orchestrators/
                                campaign.py                       campaign/

                 ──add──►     presenters/          ──refine──►  presenters/
                                campaign.py                       campaign/

tests/           ──organize──► tests/              ──add──►     tests/
  test_e2e.py                   acceptance/                      acceptance/
                                unit/                            unit/
                                integration/                     integration/
                                                                 e2e/

                                                   ──add──►     alembic/
                                                                k8s/
                                                                worker.py
```

### 5.2 Migration Checklist: Steel Thread → Pragmatic CA

**Trigger:** Stakeholders approved Steel Thread, need production readiness

**Steps:**

1. **Add Infrastructure Layer** (Decision 10: split adapters/ + infrastructure/)
   - [ ] Create `app/infrastructure/repositories/` for persistence
   - [ ] Move repository-related code from adapters if needed
   - [ ] Keep `app/adapters/` for external services

2. **Add Orchestrators** (orchestration layer - Decision 1)
   - [ ] Create `app/interface_adapters/orchestrators/`
   - [ ] Extract orchestration logic from drivers → orchestrator
   - [ ] Orchestrator coordinates multiple use cases

3. **Add Presenters** (formatting + cross-cutting)
   - [ ] Create `app/interface_adapters/presenters/`
   - [ ] Add response formatting methods
   - [ ] Add telemetry methods (`attach_telemetry()`, `emit_events()`)

4. **Organize Drivers** (Decision 9: CLI/REST/UI split)
   ```bash
   mkdir -p drivers/{cli,rest/schemas,ui/streamlit/pages}
   mv drivers/cli.py drivers/cli/__main__.py
   mv drivers/ui/streamlit_app.py drivers/ui/streamlit/app.py
   ```
   - [ ] Add `drivers/rest/` when enterprise integration needed
   - [ ] Add `drivers/rest/schemas/` for API DTOs (Decision 11)
   - [ ] Add `dependencies.py` to each driver for DI

5. **Enhance Entities** (richer domain models)
   - [ ] Add domain methods to entities
   - [ ] Add domain errors (`errors.py`)
   - [ ] Separate `brand_guidelines.py` if needed

6. **Organize Tests** (already three-layer in Steel Thread)
   - [ ] Organize test folders with subfolders
   - [ ] Add integration tests for repositories

7. **Add Real Adapters** (parallel with fakes)
   - [ ] Implement real adapters (e.g., `OpenAIImageGenerator`, `S3StorageAdapter`)
   - [ ] Write integration tests for real adapters
   - [ ] Keep fakes for acceptance tests (fast feedback - Axiom 3)

**Estimated Effort:** 1-2 days (depends on use case complexity)

**Key Insight:** You're **adding layers**, not rewriting. Use cases remain largely unchanged. Ports stay co-located (`protocol.py` with implementations) - only organizational nesting changes.

### 5.3 Migration Checklist: Pragmatic CA → Full CA

**Trigger:** Scaling to multi-team, production load, or complex domain

**Steps:**

1. **Extract Ports** (Decision 12: Two-step evolution)
   ```bash
   # Create ports structure
   mkdir -p app/application/ports/{services,repositories}

   # Move service ports
   mv app/adapters/imagegen/protocol.py app/application/ports/services/image_generator.py
   mv app/adapters/storage/protocol.py app/application/ports/services/storage_adapter.py
   mv app/adapters/events/protocol.py app/application/ports/services/event_tracker.py

   # Move repository ports
   mv app/infrastructure/repositories/campaign/protocol.py app/application/ports/repositories/campaign_repository.py

   # Update imports (find-replace)
   # From: from app.adapters.imagegen.protocol import IImageGenerator
   # To:   from app.application.ports.services.image_generator import IImageGenerator
   ```
   - [ ] Extract all co-located `protocol.py` files to `application/ports/`
   - [ ] Update all imports in use cases, orchestrators, and tests
   - [ ] Verify acceptance tests still pass (no behavior change)

2. **Add Use Case DTOs** (Decision 11: Two-step evolution)
   ```bash
   # Organize use cases into folders
   mkdir -p app/application/use_cases/generate_campaign
   mv app/use_cases/generate_campaign_uc.py app/application/use_cases/generate_campaign/use_case.py

   # Create DTOs file
   touch app/application/use_cases/generate_campaign/dtos.py
   ```
   - [ ] Create `dtos.py` for each use case
   - [ ] Define `*Input` and `*Output` DTOs
   - [ ] Update use cases to use DTOs instead of domain objects directly
   - [ ] Update orchestrators to convert: API DTO → Use Case DTO → domain

3. **Organize Application Layer** (Decision 2: Deep nesting)
   ```bash
   mkdir -p app/application
   mv app/use_cases app/application/use_cases
   ```
   - [ ] Move use cases under `application/`
   - [ ] Ports already in `application/ports/` (from step 1)

4. **Separate Entities from ORM** (domain purity)
   - [ ] Ensure entities are pure (no SQLAlchemy annotations)
   - [ ] Keep ORM models in `infrastructure/orm_models/`
   - [ ] Implement repository mapping layer (entity ↔ ORM)
   - [ ] Add domain events to aggregates (`CampaignCreated`, `CreativeGenerated`)

5. **Production Drivers** (Decision 9: Add middleware)
   ```bash
   mkdir -p drivers/rest/middleware
   ```
   - [ ] Add authentication middleware
   - [ ] Add logging middleware
   - [ ] Add telemetry middleware (OpenTelemetry)
   - [ ] Add error handling middleware

6. **Production Infrastructure** (deployment)
   - [ ] Add Kubernetes manifests (`k8s/`)
   - [ ] Add Alembic migrations (`alembic/`)
   - [ ] Add monitoring (Prometheus, Grafana)
   - [ ] Add E2E tests (`tests/e2e/`)
   - [ ] Add Celery worker (`worker.py`) if async tasks needed

**Estimated Effort:** 2-4 weeks (depends on domain complexity and team size)

**Key Insight:** You're **refining implementation**, not changing stakeholder contract. Acceptance tests remain unchanged because the feature behavior is the same—you're just improving internal quality and preparing for scale.

### 5.4 Decision Matrix: When to Evolve?

| Question | Stay | Evolve to Next Level |
|----------|------|---------------------|
| **Steel Thread → Pragmatic CA** | | |
| Did stakeholders approve the PoC? | ❌ No → Stay, iterate | ✅ Yes → Evolve |
| Is this going to production? | ❌ No → Stay | ✅ Yes → Evolve |
| Do you have multiple use cases? | ❌ No → Stay | ✅ Yes → Evolve |
| | | |
| **Pragmatic CA → Full CA** | | |
| Do you have complex domain logic? | ❌ No → Stay | ✅ Yes (aggregates, events) → Evolve |
| Is this a multi-team codebase? | ❌ No → Stay | ✅ Yes → Evolve |
| Do you need horizontal scaling? | ❌ No → Stay | ✅ Yes (Kubernetes) → Evolve |
| Do you need event sourcing/CQRS? | ❌ No → Stay | ✅ Yes → Evolve |

**Default Recommendation:** Start with **Pragmatic CA** for enterprise PoCs with multi-stakeholder workshops. Only drop to Steel Thread if you're doing a 1-2 day technical feasibility spike. Only evolve to Full CA when scaling demands it.

---

## 6. Summary: Three PoC Types at a Glance

### 6.1 Quick Reference Table

| Aspect | Steel Thread | Pragmatic CA | Full CA |
|--------|--------------|--------------|---------|
| **Timeline** | 1-2 days | 3-5 days | Weeks-months |
| **Team Size** | 1 developer | 2-4 developers | 5+ developers |
| **Use Cases** | 1 (happy path) | 2-5 | 10+ |
| **Layers** | use_cases + adapters | + orchestrators + presenters | + entities + DTOs + events |
| **Tests** | 1 end-to-end | acceptance + unit + integration | + e2e |
| **DI** | ❌ Hardcoded | ✅ Manual | ✅ DI container |
| **Deployment** | Local only | Docker Compose | Kubernetes |
| **When to Use** | Feasibility spike | Multi-stakeholder PoC | Production system |

### 6.2 Core Insight: Outside-In with All Three Types

**The Secret Sauce works at every level:**

1. **Write acceptance tests with stakeholders** → Executable specifications (all types)
2. **Write use case tests with domain experts** → Business rules documented (all types)
3. **Implement with fakes** → Test without expensive dependencies (all types)
4. **Implement real adapters** → Parallel development possible (all types)

**Key Principle:** The **stakeholder contract** (acceptance test) doesn't change when you evolve from Steel Thread → Pragmatic CA → Full CA. You're refining **implementation**, not changing **behavior**.

This is the power of Clean Architecture + Outside-In TDD: **evolution without rewrites**.

---

## 7. Summary: All 12 Architectural Decisions Applied

This framework document now incorporates all 12 resolved architectural decisions:

| Decision | Impact on Framework | Section |
|----------|---------------------|---------|
| **1. Terminology** | "Orchestrator" not "Controller" | All sections, 1.3 |
| **2. Folder Depth** | Progressive (flat → moderate → deep) | Steel Thread, Pragmatic CA, Full CA structures |
| **3. Document Scope** | All three PoC types in one doc | Entire document |
| **4. Example Scenario** | Localized Campaign Generation | Section 2 |
| **5. Code Detail** | Detailed but incomplete | All code examples |
| **6. Interface Style** | Protocol (not ABC) | All interface examples |
| **7. Test Structure** | acceptance/unit/integration | All test structures |
| **8. Fakes Location** | Two types: `adapters/` + `infrastructure/repositories/` | 1.3, Pattern 3 |
| **9. Drivers Layer** | CLI + UI always, FastAPI when needed | Steel Thread 3.1, Pragmatic CA 1.1, Pattern 2 |
| **10. Gateways Split** | `adapters/` + `infrastructure/` | Pragmatic CA 1.1, Full CA 4.1 |
| **11. DTO Strategy** | Minimal with progressive evolution | 1.3, Pragmatic CA DTOs, Full CA DTOs |
| **12. Ports Location** | Co-located → extracted | 1.3, Full CA 4.1, Migration 5.3 |

**Cross-References:**
- See `_session-02-all-decisions-resolved.md` for complete decision context
- See `_session-02-decision-9-resolution.md` for drivers layer detailed analysis
- See `/docs/lean-clean-axioms.md` for foundational principles (updated with Decisions 8 & 11)
- See `/lean-clean-blog/drafts/_wip-lean-clean-the-secret-sauce.md` for stakeholder communication

---

## 8. Next Steps

**Session 02 Work:**

1. ✅ Defined Pragmatic CA structure (complete)
2. ✅ Defined Steel Thread structure (complete)
3. ✅ Defined Full CA structure (complete)
4. ✅ Defined evolution path (complete)
5. ✅ Created complete feature example (Localized Campaign Generation)
6. ✅ Incorporated all 12 architectural decisions (complete)
7. ✅ Added migration paths with bash commands (complete)

**Future Work (Session 03 candidates):**

1. ⏭️ Update README.lean-clean.md to reference this document
2. ⏭️ Create Python code examples for each PoC type
3. ⏭️ Document DI evolution patterns (manual → dependencies module → container)
4. ⏭️ Document test patterns and fixtures
5. ⏭️ Create Streamlit UI examples for stakeholder workshops

**Success Criteria:**
- [x] Folder structure defined for all three PoC types
- [x] Clear evolution path between PoC types
- [x] Complete feature example demonstrating Outside-In approach
- [x] All 12 architectural decisions integrated
- [x] Migration paths with executable commands

---

**Document Status:** ✅ Complete - All 12 Decisions Resolved and Integrated

**Last Updated:** 2025-10-13
**Session:** 02 - Framework Folder Structures
**Related Documents:** `_session-02-all-decisions-resolved.md`, `_session-02-decision-9-resolution.md`
