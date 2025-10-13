# Session 02: Framework Folder Structures

**Date:** 2025-10-13
**Purpose:** Define concrete folder architectures for Steel Thread, Pragmatic CA, and Full CA PoC types
**Status:** In Progress

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
| **Steel Thread** | 1-2 days | Minimal (use_cases + adapters + fakes) | Technical feasibility spike | Stakeholders approved, need production path |
| **Pragmatic CA** | 3-5 days | Production-ready subset (+ controllers + presenters) | Multi-stakeholder workshop output | Scaling to multi-team or complex domain |
| **Full CA** | Production | Comprehensive (+ separate entities, DTOs, events, CQRS) | Production-grade system | Already in production |

---

## 1. Pragmatic CA (The Sweet Spot)

**Target:** Enterprise PoCs with multi-stakeholder workshops, 3-5 day timeline, production-ready subset

### 1.1 Folder Structure

```
campaign-generator/                    # Root project directory
├─ app/                                # Application code
│  ├─ core/                            # ═══ CORE (innermost) ═══
│  │  ├─ entities/                     # Domain models, business objects
│  │  │  ├─ campaign.py                # Campaign, CreativeSet, Creative
│  │  │  ├─ brand.py                   # Brand, BrandGuidelines
│  │  │  └─ errors.py                  # DomainError, ValidationError
│  │  ├─ use_cases/                    # Business logic (application layer)
│  │  │  ├─ generate_campaign.py       # GenerateCampaignUseCase
│  │  │  ├─ validate_brand.py          # ValidateBrandComplianceUseCase
│  │  │  └─ emit_events.py             # EmitCampaignEventsUseCase
│  │  └─ interfaces/                   # Infrastructure contracts (ports)
│  │     ├─ image_generator.py         # IImageGenerator protocol
│  │     ├─ storage.py                 # IStorageAdapter protocol
│  │     └─ event_tracker.py           # IEventTracker protocol
│  │
│  ├─ adapters/                        # ═══ INFRASTRUCTURE ═══
│  │  ├─ imagegen/                     # Image generation implementations
│  │  │  ├─ openai.py                  # OpenAIImageGenerator (real)
│  │  │  ├─ firefly.py                 # FireflyImageGenerator (real)
│  │  │  └─ fake_imagegen.py           # FakeImageGenerator (test double)
│  │  ├─ storage/                      # Storage implementations
│  │  │  ├─ local.py                   # LocalStorageAdapter (real)
│  │  │  ├─ s3.py                      # S3StorageAdapter (real)
│  │  │  └─ fake_storage.py            # FakeStorageAdapter (test double)
│  │  └─ events/                       # Event tracking implementations
│  │     ├─ amplitude.py               # AmplitudeEventTracker (real)
│  │     └─ fake_events.py             # FakeEventTracker (test double)
│  │
│  ├─ interface_adapters/              # ═══ INTERFACE ADAPTERS ═══
│  │  ├─ controllers/                  # Orchestration layer (feature coordination)
│  │  │  └─ campaign_controller.py     # LocalizedCampaignController
│  │  └─ presenters/                   # Response formatting + cross-cutting concerns
│  │     └─ campaign_presenter.py      # CampaignPresenter (to_response, attach_telemetry, emit_events)
│  │
│  └─ utils/                           # Cross-cutting utilities (not Clean Architecture layer)
│     ├─ observability.py              # Telemetry helpers (Phoenix/Arize stubs)
│     └─ manifest.py                   # Manifest generation utilities
│
├─ server.py                           # ═══ FRAMEWORKS & DRIVERS ═══
├─ cli.py                              # FastAPI, Typer CLI (entry points)
├─ models.py                           # SQLAlchemy ORM models
├─ db.py                               # Database session utilities
│
├─ tests/                              # ═══ TESTS (mirror app structure) ═══
│  ├─ acceptance/                      # Layer 1: Stakeholder contracts (ALWAYS use fakes)
│  │  └─ features/                     # Feature-level tests
│  │     └─ test_localized_campaign_feature.py
│  ├─ unit/                            # Layer 2: Isolated logic tests
│  │  ├─ use_cases/                    # Business logic tests
│  │  │  ├─ test_generate_campaign.py
│  │  │  └─ test_validate_brand.py
│  │  ├─ controllers/                  # Orchestration tests (with fake use cases)
│  │  │  └─ test_campaign_controller.py
│  │  └─ entities/                     # Domain model tests
│  │     └─ test_campaign.py
│  └─ integration/                     # Layer 3: Real service tests
│     └─ adapters/                     # Test real implementations
│        ├─ test_openai_imagegen.py
│        └─ test_s3_storage.py
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
| `core/entities/` | **Entities** (innermost) | Domain models, business rules, domain errors | Nothing |
| `core/use_cases/` | **Application** | Business logic, orchestrate entities, use interfaces | Entities, Interfaces |
| `core/interfaces/` | **Application** | Infrastructure contracts (defined by app needs) | Entities |
| `adapters/` | **Infrastructure** | Concrete implementations of interfaces | Interfaces, External services |
| `interface_adapters/controllers/` | **Interface Adapters** | Feature orchestration (multi-use-case coordination) | Use Cases |
| `interface_adapters/presenters/` | **Interface Adapters** | Response formatting, telemetry, events | Entities, Use Case outputs |
| `server.py`, `cli.py`, `models.py` | **Frameworks & Drivers** | Entry points, ORM, external tools | Controllers, Presenters |

**Dependency Rule:** All dependencies point inward (toward Entities). Outer layers never imported by inner layers.

### 1.3 Why This Structure?

**Q: Why separate `controllers/` from `use_cases/`?**
- Controllers orchestrate **multiple use cases** for a feature
- Use cases implement **single business operations**
- Example: `LocalizedCampaignController` coordinates `GenerateCampaignUseCase` + `ValidateBrandUseCase` + `EmitEventsUseCase`

**Q: Why put telemetry/events in `presenters/`?**
- Telemetry is "formatting domain data for observability systems" (presentation concern)
- Keeps use cases clean of infrastructure
- Explicit methods (`attach_telemetry()`, `emit_events()`) are testable
- See Session 01 Q2 for full rationale

**Q: Why `interfaces/` in `core/` instead of `adapters/`?**
- Follows **Dependency Inversion Principle** (DIP)
- Interfaces defined by **application needs** (inner layer), not infrastructure capabilities (outer layer)
- Example: `IImageGenerator` defines what the use case needs (`generate(prompt, size)`), not what OpenAI provides

**Q: Why separate `fakes/` in adapters?**
- Fakes are **production code** (realistic behavior, no mocks), not test code
- Enables Outside-In TDD: write acceptance tests with fakes before real adapters exist
- Example: `FakeImageGenerator` returns realistic fake images for workshop demos

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
from app.interface_adapters.controllers.campaign_controller import LocalizedCampaignController
from app.core.use_cases.generate_campaign import GenerateCampaignUseCase
from app.core.use_cases.validate_brand import ValidateBrandComplianceUseCase
from app.core.use_cases.emit_events import EmitCampaignEventsUseCase
from app.adapters.imagegen.fake_imagegen import FakeImageGenerator
from app.adapters.storage.fake_storage import FakeStorageAdapter
from app.adapters.events.fake_events import FakeEventTracker


@pytest.mark.feature("localized_campaign_generation")
class TestLocalizedCampaignFeature:
    """Maps to: LocalizedCampaignController"""

    @pytest.fixture
    def controller(self):
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

        # Interface adapter layer (controller)
        return LocalizedCampaignController(
            generate_campaign_uc=generate_uc,
            validate_brand_uc=validate_uc,
            emit_events_uc=emit_events_uc
        )

    async def test_generate_summer_sale_campaign_for_five_markets(self, controller):
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
        response = await controller.generate_campaign(request)
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

    async def test_reject_campaign_with_brand_violations(self, controller):
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
            await controller.generate_campaign(request)

        assert "cheap" in str(exc_info.value).lower(), \
            "Error should clearly indicate which banned term was violated"
```

**Key Points:**

- **Readable by technical stakeholders** - Creative Lead, Ad Ops, IT, Legal can validate requirements
- **Uses fakes** - No OpenAI API calls, runs in <1s, deterministic
- **Domain language** - `LocalizedCampaignRequest`, not `dict` or raw JSON
- **BDD mindset** - Given/When/Then structure in docstrings

### 2.2 Controller Implementation (Layer 2: Orchestration)

**File:** `app/interface_adapters/controllers/campaign_controller.py`

```python
"""
LocalizedCampaignController orchestrates the full localized campaign generation feature.

Responsibilities:
1. Coordinate multiple use cases (generate, validate, emit events)
2. Handle feature-level error handling
3. Format response via presenter

This maps to "Feature" in test language, "Controller" in implementation language.
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


class LocalizedCampaignController:
    """
    Feature orchestrator: coordinates generate → validate → emit events.

    This is the "Controller" in Clean Architecture interface adapters layer.
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
1. Implement `LocalizedCampaignController` (orchestration)
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
│  ├─ use_cases/                       # Flat business logic (no separate entities/)
│  │  └─ generate_campaign.py          # Single-file use case
│  ├─ adapters/                        # Infrastructure (mostly fakes)
│  │  ├─ fake_imagegen.py
│  │  └─ fake_storage.py
│  └─ cli.py                           # Entry point (Typer CLI)
│
├─ tests/
│  └─ test_end_to_end.py               # Single acceptance test
│
├─ pyproject.toml
└─ README.md
```

### 3.2 Key Differences from Pragmatic CA

| Aspect | Steel Thread | Pragmatic CA |
|--------|--------------|--------------|
| **Controllers** | ❌ None - CLI calls use case directly | ✅ Yes - orchestrate multiple use cases |
| **Presenters** | ❌ None - use case returns dict | ✅ Yes - format responses + telemetry |
| **Entities** | ❌ None - use dataclasses in use case | ✅ Yes - separate domain models |
| **Interfaces** | ❌ None - adapters imported directly | ✅ Yes - protocols for DIP |
| **Test Layers** | ❌ Single file | ✅ acceptance/ + unit/ + integration/ |

### 3.3 Example: Steel Thread Use Case

**File:** `app/use_cases/generate_campaign.py`

```python
"""
Steel Thread: Single-file use case with minimal ceremony.

Trade-offs:
- ✅ Fast to write (1-2 hours)
- ✅ Proves technical feasibility
- ❌ Hard to extend (no controllers, no interfaces)
- ❌ Not production-ready (no error handling, no telemetry)
"""

from dataclasses import dataclass
from app.adapters.fake_imagegen import FakeImageGenerator
from app.adapters.fake_storage import FakeStorageAdapter


@dataclass
class GenerateCampaignInput:
    message: str
    markets: list[str]


async def generate_campaign(input: GenerateCampaignInput) -> dict:
    """
    Steel Thread: Minimal implementation to prove feasibility.

    Returns dict (not domain object) for simplicity.
    """
    # Hardcoded dependencies (no DI, no interfaces)
    imagegen = FakeImageGenerator()
    storage = FakeStorageAdapter()

    results = {}
    for market in input.markets:
        image_data = await imagegen.generate(
            prompt=f"{input.message} for {market}",
            size="1024x1024"
        )
        image_url = await storage.save(
            path=f"{market}.jpg",
            content=image_data
        )
        results[market] = image_url

    return results
```

### 3.4 Example: Steel Thread Test

**File:** `tests/test_end_to_end.py`

```python
"""
Steel Thread: Single end-to-end test proving feasibility.
"""

import pytest
from app.use_cases.generate_campaign import generate_campaign, GenerateCampaignInput


@pytest.mark.asyncio
async def test_generate_campaign_for_multiple_markets():
    """
    Given: Campaign message and target markets
    When: Generating campaign
    Then: Images created for all markets
    """
    input = GenerateCampaignInput(
        message="Summer Sale",
        markets=["US", "UK", "DE"]
    )

    result = await generate_campaign(input)

    assert len(result) == 3
    assert all(market in result for market in input.markets)
```

### 3.5 When to Use Steel Thread

**Use Steel Thread when:**
- ✅ Proving technical feasibility (e.g., "Can we integrate OpenAI API?")
- ✅ Time-boxed spike (1-2 days max)
- ✅ Single developer
- ✅ Throwaway code (won't be productionized)

**Don't use Steel Thread when:**
- ❌ Multi-stakeholder workshop (need testable contracts)
- ❌ Multiple use cases to coordinate (need controllers)
- ❌ Production path required (need proper layering)

→ In these cases, start with **Pragmatic CA** instead.

---

## 4. Full CA (Production-Grade)

**Target:** Production systems, multi-team, complex domain, horizontal scalability

### 4.1 Folder Structure

```
campaign-generator-production/
├─ app/
│  ├─ core/
│  │  ├─ entities/                     # ═══ Rich domain models ═══
│  │  │  ├─ campaign/
│  │  │  │  ├─ campaign.py             # Campaign aggregate root
│  │  │  │  ├─ creative_set.py         # CreativeSet entity
│  │  │  │  ├─ creative.py             # Creative value object
│  │  │  │  └─ events.py               # CampaignCreated, CreativeGenerated domain events
│  │  │  ├─ brand/
│  │  │  │  ├─ brand.py                # Brand aggregate root
│  │  │  │  └─ guidelines.py           # BrandGuidelines value object
│  │  │  └─ shared/
│  │  │     ├─ errors.py               # Domain errors hierarchy
│  │  │     └─ value_objects.py        # Money, MarketCode, PlatformType
│  │  │
│  │  ├─ use_cases/                    # ═══ Comprehensive use cases ═══
│  │  │  ├─ generate_campaign/
│  │  │  │  ├─ command.py              # GenerateCampaignCommand
│  │  │  │  ├─ use_case.py             # GenerateCampaignUseCase
│  │  │  │  ├─ input_dto.py            # GenerateCampaignInput
│  │  │  │  ├─ output_dto.py           # GenerateCampaignOutput
│  │  │  │  └─ validators.py           # Input validation logic
│  │  │  ├─ validate_brand/
│  │  │  └─ emit_events/
│  │  │
│  │  └─ interfaces/                   # ═══ Comprehensive ports ═══
│  │     ├─ repositories/              # Domain repository interfaces
│  │     │  ├─ campaign_repository.py
│  │     │  └─ brand_repository.py
│  │     ├─ services/                  # External service interfaces
│  │     │  ├─ image_generator.py
│  │     │  ├─ translation_service.py
│  │     │  └─ content_filter.py
│  │     └─ events/                    # Event bus interface
│  │        └─ event_bus.py
│  │
│  ├─ adapters/                        # ═══ Production implementations ═══
│  │  ├─ imagegen/
│  │  │  ├─ openai.py
│  │  │  ├─ firefly.py
│  │  │  └─ fake.py
│  │  ├─ storage/
│  │  │  ├─ s3.py
│  │  │  ├─ azure_blob.py
│  │  │  └─ fake.py
│  │  ├─ db/
│  │  │  ├─ repositories/              # Repository implementations
│  │  │  │  ├─ campaign_repo.py        # SQLAlchemy implementation
│  │  │  │  └─ brand_repo.py
│  │  │  └─ models.py                  # ORM models (separate from entities!)
│  │  ├─ events/
│  │  │  ├─ kafka_event_bus.py         # Kafka event bus
│  │  │  └─ fake_event_bus.py
│  │  └─ translation/
│  │     ├─ google_translate.py
│  │     └─ fake_translate.py
│  │
│  ├─ interface_adapters/              # ═══ Comprehensive adapters ═══
│  │  ├─ controllers/
│  │  │  └─ campaign/
│  │  │     ├─ controller.py           # LocalizedCampaignController
│  │  │     └─ request_validators.py   # API-level validation
│  │  ├─ presenters/
│  │  │  └─ campaign/
│  │  │     ├─ presenter.py            # CampaignPresenter
│  │  │     └─ dto_mappers.py          # Entity → DTO mapping
│  │  └─ dtos/                         # API-level DTOs (separate from domain DTOs)
│  │     ├─ campaign_request.py
│  │     └─ campaign_response.py
│  │
│  └─ utils/
│     ├─ middleware/                   # ═══ AOP concerns ═══
│     │  ├─ telemetry.py               # Decorator for tracing
│     │  ├─ caching.py                 # Decorator for caching
│     │  └─ retry.py                   # Decorator for retries
│     ├─ events/
│     │  └─ event_dispatcher.py        # Event handling coordination
│     └─ config/
│        └─ settings.py                # Environment configuration
│
├─ server.py                           # FastAPI with OpenAPI docs
├─ cli.py                              # Typer CLI
├─ worker.py                           # Celery worker for async tasks
├─ models.py                           # SQLAlchemy ORM (production)
├─ alembic/                            # Database migrations
│
├─ tests/
│  ├─ acceptance/                      # Feature-level tests
│  │  └─ features/
│  ├─ unit/                            # Isolated logic tests
│  │  ├─ entities/
│  │  ├─ use_cases/
│  │  ├─ controllers/
│  │  └─ presenters/
│  ├─ integration/                     # Real adapter tests
│  │  └─ adapters/
│  └─ e2e/                             # Full system tests
│     └─ test_production_workflow.py
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
| **Entities** | Simple dataclasses in entities/ | Rich aggregate roots with domain events |
| **Use Cases** | Single file per use case | Folder per use case (command, DTO, validators) |
| **DTOs** | Minimal (use case input/output) | Comprehensive (domain DTOs + API DTOs) |
| **Repositories** | ❌ Direct adapter calls | ✅ Repository pattern with interfaces |
| **Events** | Emitted via presenters | Domain events + event sourcing |
| **Cross-Cutting** | Explicit presenter methods | Middleware/decorators (AOP) |
| **ORM Models** | ❌ Entities are ORM models | ✅ Separate entities from ORM (mapping layer) |
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

                 ──add──►     controllers/         ──refine──►  controllers/
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

1. **Add Controllers** (orchestration layer)
   - [ ] Create `interface_adapters/controllers/`
   - [ ] Extract orchestration logic from CLI → controller
   - [ ] Controller coordinates multiple use cases

2. **Add Presenters** (formatting + cross-cutting)
   - [ ] Create `interface_adapters/presenters/`
   - [ ] Move response formatting from use cases → presenters
   - [ ] Add telemetry methods (`attach_telemetry()`, `emit_events()`)

3. **Define Interfaces** (Dependency Inversion)
   - [ ] Create `core/interfaces/`
   - [ ] Define protocols for adapters (`IImageGenerator`, `IStorageAdapter`)
   - [ ] Update use cases to depend on interfaces, not implementations

4. **Organize Entities** (domain models)
   - [ ] Create `core/entities/`
   - [ ] Move domain models from use cases → entities
   - [ ] Add domain errors (`errors.py`)

5. **Organize Tests** (layer separation)
   - [ ] Create `tests/acceptance/features/` for feature tests
   - [ ] Create `tests/unit/use_cases/` for use case tests
   - [ ] Create `tests/integration/adapters/` for adapter tests
   - [ ] Move existing tests to appropriate layers

6. **Add Real Adapters** (parallel with fakes)
   - [ ] Implement real adapters (`OpenAIImageGenerator`, `S3StorageAdapter`)
   - [ ] Write integration tests for real adapters
   - [ ] Keep fakes for acceptance tests (fast feedback)

**Estimated Effort:** 1-2 days (depends on use case complexity)

**Key Insight:** You're **adding layers**, not rewriting. Use cases remain largely unchanged because you're just refactoring imports (interfaces instead of concrete adapters).

### 5.3 Migration Checklist: Pragmatic CA → Full CA

**Trigger:** Scaling to multi-team, production load, or complex domain

**Steps:**

1. **Separate Entities from ORM** (domain purity)
   - [ ] Create rich aggregate roots in `core/entities/`
   - [ ] Add domain events (`CampaignCreated`, `CreativeGenerated`)
   - [ ] Create ORM models in `adapters/db/models.py` (separate from entities)
   - [ ] Implement repository pattern for mapping entity ↔ ORM

2. **Comprehensive DTO Layer** (API vs domain separation)
   - [ ] Create API DTOs in `interface_adapters/dtos/`
   - [ ] Create domain DTOs in `core/use_cases/*/dtos.py`
   - [ ] Add mappers in presenters (`entity → DTO → API response`)

3. **Extract Cross-Cutting to Middleware** (AOP)
   - [ ] Create decorators in `utils/middleware/`
   - [ ] Add `@trace`, `@retry`, `@cache` decorators
   - [ ] Remove explicit telemetry calls from presenters

4. **Add Event Sourcing** (if needed)
   - [ ] Implement event bus interface in `core/interfaces/events/`
   - [ ] Add Kafka/RabbitMQ adapter in `adapters/events/`
   - [ ] Emit domain events from aggregates
   - [ ] Create event handlers in `utils/events/`

5. **Add CQRS** (if read/write patterns diverge)
   - [ ] Separate command use cases (`core/use_cases/commands/`)
   - [ ] Separate query use cases (`core/use_cases/queries/`)
   - [ ] Add read models in `adapters/db/read_models.py`

6. **Production Infrastructure** (deployment)
   - [ ] Add Kubernetes manifests (`k8s/`)
   - [ ] Add Celery worker (`worker.py`) for async tasks
   - [ ] Add Alembic migrations (`alembic/`)
   - [ ] Add monitoring (Prometheus, Grafana)
   - [ ] Add E2E tests (`tests/e2e/`)

**Estimated Effort:** 2-4 weeks (depends on domain complexity and team size)

**Key Insight:** You're **refining implementation**, not changing stakeholder contract. Acceptance tests remain unchanged because the feature behavior is the same—you're just improving internal quality.

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
| **Layers** | use_cases + adapters | + controllers + presenters | + entities + DTOs + events |
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

## 7. Next Steps

**Session 02 Remaining Work:**

1. ✅ Defined Pragmatic CA structure (complete)
2. ✅ Defined Steel Thread structure (complete)
3. ✅ Defined Full CA structure (complete)
4. ✅ Defined evolution path (complete)
5. ✅ Created complete feature example (Localized Campaign Generation)
6. ⏭️ **Next:** Update README.lean-clean.md to reference this document
7. ⏭️ **Next:** Create Python code examples (Session 03)

**Success Criteria:**
- [x] Folder structure defined for at least Pragmatic CA
- [x] Clear evolution path between PoC types
- [x] One complete example test demonstrating Outside-In approach

---

**Document Status:** ✅ Complete

**Last Updated:** 2025-10-13
