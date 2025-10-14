# Review Guide: Deciding on Final PoC Structure

> **⚠️ SUPERSEDED BY SESSION 01**
>
> This document was created on 2025-10-12, before Session 01 architectural decisions (2025-10-13).
>
> **Session 01 now authoritatively defines:**
> - **PoC types** → See `/plan/sessions/_session-01-architectural-decisions.md` Q1 (Steel Thread, Pragmatic CA, Full CA)
> - **Evolution paths** → See Session 01 Q5 (progressive refinement)
> - **Decision framework** → Integrated into `README.md` Phase 5
>
> This file remains for additional decision guidance but **defer to Session 01 for current architectural decisions.**

## How to Use This Guide

This guide provides a structured decision-making process for finalizing the PoC structure recommendations in the lean-clean-methodology. **Choose your path based on available time:**

### ⚡ Quick Path (30 minutes)
1. Read **CRITICAL_INSIGHTS.md** (10 min) - Understand the controller vs mapper distinction
2. Skip to **"Quick Decision Shortcuts"** section (5 min) - Choose AI/LLM PoCs, Quick Prototyping, or Production-Grade
3. Review the pre-made decision YAML (5 min)
4. Jump to **Phase 5: Update Documentation** (10 min)

**Best for:** Solo developers who need to make a decision quickly and trust the analysis.

### 🎯 Standard Path (3-5 hours)
1. **Phase 1:** Understand the Landscape (30-45 min)
2. **Phase 2:** Deep Review by Decision Area (1-2 hours)
3. **Phase 3:** Make Key Decisions (30-45 min)
4. **Phase 4:** Create Final Recommendations (1-2 hours)
5. **Phase 5:** Update Documentation (1-2 hours)

**Best for:** Teams who need consensus and want to understand trade-offs deeply.

### 🔬 Comprehensive Path (1-2 days)
1. Complete Standard Path
2. Read all reference implementation code
3. Build a test PoC with each structure option
4. Gather team feedback
5. Iterate on recommendations

**Best for:** Organizations establishing long-term standards for multiple teams.

---

## ⚠️ CRITICAL: Read This First

**Before reviewing structures, read `plan/drafts/CRITICAL_INSIGHTS.md`** - It identifies a fundamental architectural divergence between:
- **Uncle Bob's Clean Architecture** (controllers orchestrate, use cases have business logic)
- **PoC-Optimized Architecture** (thin mappers, thin use cases, direct driver→use case calls)

This distinction affects ALL structure decisions below. **The key question is: Are you building for speed (PoC) or robustness (Production)?**

---

## Purpose

This guide helps you systematically review the proposed structures and patterns to finalize what should be recommended in the lean-clean-methodology documentation.

## Documents to Review

### Core Analysis Documents
1. **`plan/drafts/CRITICAL_INSIGHTS.md`** ⚠️ **READ FIRST** - Controller vs Mapper distinction, progressive architecture
2. **`plan/drafts/TDD_STAKEHOLDER_ACCELERATION_INSIGHTS.md`** ⚠️ **CRITICAL FOR ENTERPRISE** - How TDD reduces delivery from 2-4 weeks to 1 week
3. **`plan/STRUCTURE.md`** - Comprehensive analysis of 3 reference projects with proposed baseline structure
4. **`plan/drafts/PYTHON_PATTERNS.md`** - Python-specific idioms and patterns from the style guide
5. **`plan/drafts/TDD_INTEGRATION_PLAN.md`** - Roadmap for weaving TDD into methodology phases
6. **`plan/drafts/TDD_ENTERPRISE_POC.md`** - Original TDD framework and patterns

### Reference Implementations
- **`calibration-service`** - Production-grade CA (true controllers, rich use cases, DTOs)
- **`creative-ai-poc`** - AI-focused pragmatic CA (thin use cases, direct wiring)
- **`lean-clean-core-skeleton`** - Minimal steel thread (no controllers, inline wiring)

### External Analysis
- **`../lean-clean-code/CLEAN_ARCHITECTURE_ANALYSIS.md`** - Detailed comparison of calibration-service vs lean-clean patterns

## Review Workflow

### Phase 1: Understand the Landscape (30-45 min)

#### Step 1: Quick Scan of STRUCTURE.md
- [ ] Read Executive Summary
- [ ] Scan the three reference implementation analyses
- [ ] Review the "Recommended Baseline: Pragmatic Clean Architecture" structure
- [ ] Note which parts feel immediately right or wrong

#### Step 2: Quick Scan of PYTHON_PATTERNS.md
- [ ] Read the Summary section
- [ ] Scan the 10 key pattern updates
- [ ] Note which patterns you're already using vs. new to you

#### Step 3: Identify Your Context

Answer these questions:

- What type of PoCs will you typically build? (AI-focused, data pipelines, APIs, etc.)
- What's your typical team size? (solo, 2-3, 4+)
- What's your typical PoC timeline? (2 days, 1 week, 2 weeks, 1 month)
- What's your typical PoC to production path? (throwaway, evolve to prod, production-grade from start)

### Phase 2: Deep Review by Decision Area (1-2 hours)

For each decision area below, use the decision framework to evaluate options.

---

## Decision Area 1: Base Structure Complexity

**Question:** What should be the default recommended structure?

### Options

| Option | When to Use | Pros | Cons |
|--------|-------------|------|------|
| **Steel Thread** (lean-clean-core-skeleton) | P3-P4: Initial prototyping, 2-3 day timeboxes, solo dev | Fastest to start, minimal files, clear growth path | Lacks controller/presenter separation, no built-in observability |
| **Pragmatic CA** (creative-ai-poc style) | P5-P6: Full implementations, 1-2 week timeboxes, 2-3 devs | Balanced structure, observability built-in, ports/adapters | More upfront setup than steel thread |
| **Full CA** (calibration-service style) | P6+: Production-ready, ongoing maintenance, 3+ devs | Complete separation, comprehensive testing, DTOs | Heavy for PoCs, can feel over-engineered |

### Decision Framework

**Rate each option (1-5 scale) for your typical use case:**

```
Criteria                           | Steel Thread | Pragmatic CA | Full CA
-----------------------------------|--------------|--------------|--------
Speed to first working code        |     5        |      3       |    2
Suitable for AI/LLM PoCs          |     3        |      5       |    5
Ease of adding observability      |     2        |      5       |    5
Testability out of the box        |     3        |      4       |    5
Suitable for solo developer       |     5        |      4       |    3
Scalable to team of 3+            |     2        |      4       |    5
Can evolve to production          |     3        |      4       |    5
Learning curve for new devs       |     5        |      4       |    2
```

**Your scores:**

```
Criteria                           | Steel Thread | Pragmatic CA | Full CA
-----------------------------------|--------------|--------------|--------
Speed to first working code        |      ?       |      ?       |    ?
Suitable for AI/LLM PoCs          |      ?       |      ?       |    ?
Ease of adding observability      |      ?       |      ?       |    ?
Testability out of the box        |      ?       |      ?       |    ?
Suitable for solo developer       |      ?       |      ?       |    ?
Scalable to team of 3+            |      ?       |      ?       |    ?
Can evolve to production          |      ?       |      ?       |    ?
Learning curve for new devs       |      ?       |      ?       |    ?
```

**Decision:**

- [ ] Use Steel Thread as default, document migration to Pragmatic CA
- [ ] Use Pragmatic CA as default, document Steel Thread as faster option
- [ ] Provide decision tree based on PoC phase/complexity
- [ ] Other: _________________

---

## Decision Area 2: Layer Naming

**Question:** What should the layer names be?

### Options

| Layer Concept | Option A (calibration) | Option B (creative-ai-poc) | Option C (hybrid) |
|---------------|------------------------|----------------------------|-------------------|
| Core business logic | `entities/` | `domain/` | `domain/` |
| Use cases + ports | `application/` | `application/` | `application/` |
| Controller/presenter | `interface_adapters/` | _(no separate layer)_ | `interface_adapters/` (optional) |
| Implementations | `infrastructure/` | `infrastructure/` | `infrastructure/` |
| Entry points | `drivers/` | `drivers/` | `drivers/` |

### Considerations

**Option A (5-layer with entities/):**

- ✅ Aligns with classic Clean Architecture terminology
- ✅ Clear separation of entities from application logic
- ❌ "entities" less intuitive than "domain" for Python devs
- ❌ Extra layer may feel heavy for small PoCs

**Option B (4-layer with domain/):**

- ✅ "domain" more familiar to Python/DDD community
- ✅ Simpler structure, fewer directories
- ✅ Controllers can live in drivers/ for simple cases
- ❌ Less explicit about where controllers/presenters go

**Option C (flexible 4-5 layer):**

- ✅ Accommodates both simple and complex PoCs
- ✅ Start with 4 layers, add `interface_adapters/` when needed
- ❌ Requires clear guidance on when to add 5th layer

**Decision:**

- [ ] Use Option A: 5-layer with `entities/` (classic CA)
- [ ] Use Option B: 4-layer with `domain/` (pragmatic)
- [ ] Use Option C: Start with 4-layer, add 5th when needed
- [ ] Other: _________________

**If Option B or C, where do controllers/presenters live initially?**

- [ ] In `drivers/` (co-located with framework code)
- [ ] In `application/` (as part of use case coordination)
- [ ] Create `interface_adapters/` from the start
- [ ] Provide guidance: simple PoCs in drivers/, complex in interface_adapters/

---

## Decision Area 3: Controllers vs Mappers (NEW - Based on CRITICAL_INSIGHTS)

**Question:** What should we call the boundary conversion layer, and when should we add orchestration?

⚠️ **This is the most important decision** - Read `CRITICAL_INSIGHTS.md` first!

### The Distinction

**Uncle Bob's "Controllers"** (calibration-service pattern):

```python
# interface_adapters/controllers/add_calibration_controller.py
class AddCalibrationController:
    async def create_calibration(self, request: Request) -> Response:
        # 1. Convert DTO
        input_dto = self._to_input(request)
        # 2. Orchestrate use case(s)
        output_dto = await self._use_case(input_dto)
        # 3. Handle errors
        # 4. Present result
        return self._presenter.format(output_dto)
```
- **Orchestrates** multiple operations
- Handles cross-cutting concerns (auth, logging)
- Sits in `interface_adapters/`
- Coordinates use cases and presenters

**PoC "Mappers/Handlers"** (creative-ai-poc pattern):

```python
# drivers/cli.py
@app.command()
def run(brief_path: str):
    brief = load_brief(brief_path)  # Just converts
    assets = generate_use_case(brief, ...)  # Direct call
    print(json.dumps(assets))
```
- **No orchestration** - just converts and passes through
- Lives in `drivers/`
- Direct driver → use case wiring
- Fast for PoCs

### Decision Matrix

| Scenario | Use Orchestration Controllers? | Rationale |
|----------|------------------------------|-----------|
| 2-3 day PoC | ❌ No | Direct wiring is faster |
| Need authentication/authorization | ✅ Yes | Controllers check permissions |
| Multi-step workflows | ✅ Yes | Controllers coordinate steps |
| Simple CRUD | ❌ No | Overkill |
| Team of 3+ developers | ✅ Yes | Clear boundaries |
| Production system | ✅ Yes | Need orchestration point |
| Rapid iteration required | ❌ No | Less boilerplate |

### Options

**Option A: Always Use True Controllers**

- Start with `interface_adapters/controllers/` from day 1
- All examples show orchestration pattern
- Pros: Aligned with Uncle Bob, scales well
- Cons: Slower initial development

**Option B: Never Use Controllers (PoC Pattern)**

- Direct driver → use case wiring
- Call them "handlers" or "routers"
- Pros: Fast development, less boilerplate
- Cons: Hard to add orchestration later

**Option C: Progressive Controllers (RECOMMENDED)**

- **Phase 3-4:** No controllers, direct wiring
- **Phase 5-6:** Optional controllers for complex cases
- **Phase 7+:** Add controllers when migrating to production
- Pros: Matches methodology phases, flexible
- Cons: Requires clear guidance on when to add

**Option D: Dual Patterns**

- Document both patterns side-by-side
- "PoC Pattern" vs "Production Pattern"
- Clear criteria for choosing
- Pros: Explicit about trade-offs
- Cons: More documentation to maintain

### Terminology Decision

If NOT using true orchestration controllers, what should we call the boundary layer?

- [ ] **Handlers** (FastAPI: route handlers, CLI: command handlers)
- [ ] **Mappers** (boundary mappers that convert DTOs)
- [ ] **Adapters** (adapter functions, not controllers)
- [ ] **Routers** (framework-specific routing logic)
- [ ] **Keep "Controllers"** but clarify they're simplified

**Decision:**

- [ ] Option A: Always use true controllers
- [ ] Option B: Never use controllers (PoC pattern only)
- [ ] Option C: Progressive controllers (start without, add when needed)
- [ ] Option D: Document both patterns explicitly

**Terminology choice:** _______________

**Notes:** _______________________________________________

---

## Decision Area 4: Use Case Complexity

**Question:** Should use cases be thin (delegates to ports) or rich (contains business logic)?

### The Distinction

**Thin Use Case** (PoC pattern):

```python
class GenerateCreativesUseCase:
    def execute(self, brief: Brief) -> List[Asset]:
        """Just delegates to ports."""
        return self._image_gen.generate(brief)
```
- Minimal logic
- Quick to write
- Business logic in adapters

**Rich Use Case** (Uncle Bob pattern):

```python
class AddCalibrationUseCase:
    async def __call__(self, input: AddCalibrationInput) -> AddCalibrationOutput:
        """Contains business logic."""
        # Validate
        measurement = Measurement(input.value, CalibrationType(input.calibration_type))
        # Create entity
        calibration = Calibration(id=uuid4(), measurement=measurement, ...)
        # Persist
        saved = await self._repo.add_calibration(calibration)
        # Return DTO
        return AddCalibrationOutput(created_calibration=saved)
```
- Creates value objects and entities
- Enforces business rules
- Clear business logic location

### Decision

**For PoC phases (P3-P6):**

- [ ] Allow thin use cases (focus on speed)
- [ ] Require rich use cases (learn CA properly)
- [ ] Mixed: thin for simple operations, rich for complex

**For Production phases (P7+):**

- [ ] Require rich use cases
- [ ] Allow thin if truly simple CRUD
- [ ] Case-by-case basis

**Notes:** _______________________________________________

---

## Decision Area 5: TDD Strategy (NEW - Enterprise Acceleration)

**Question:** Should we use Test-Driven Development for this PoC?

⚠️ **CRITICAL DECISION** - This can reduce enterprise PoC delivery from 2-4 weeks to 1 week

**Read these documents:**

- **`plan/drafts/TDD_STAKEHOLDER_ACCELERATION_INSIGHTS.md`** - Complete analysis of how TDD accelerates stakeholder alignment
- **`plan/drafts/TDD_INTEGRATION_PLAN.md`** - How to weave TDD into each methodology phase
- **`plan/drafts/TDD_ENTERPRISE_POC.md`** - Original framework and patterns

### The Paradigm Shift: Tests as Stakeholder Communication

**Old Paradigm:** TDD is about catching bugs
**New Paradigm:** TDD is about **stakeholder alignment through executable specifications**

**Why this matters for enterprise PoCs:**

- Stakeholders co-author tests in 1-hour workshop → executable requirements (no misinterpretation)
- Implement with fakes first → prove concept without paying for OpenAI/S3 ($0 API costs during development)
- Get approval before expensive work → zero rework from misalignment
- Parallel development → teams work on agreed interfaces independently
- Tests = documentation → governance approval is faster

**Typical Results:**

- **Timeline:** 1 week (TDD) vs 2-4 weeks (traditional with rewrites)
- **Cost Savings:** 90% (develop with fakes, only test real adapters at end)
- **Rework:** 0 cycles (stakeholders approve tests upfront)

### The TDD Advantage

**Outside-In TDD enables:**

1. **Writing tests with stakeholders** - Executable specifications, not vague requirements
2. **Implementing with fakes first** - No expensive external dependencies until spec is locked
3. **Parallel team development** - Agree on interfaces, teams implement independently
4. **Faster alignment** - 1-week delivery vs 2-3 weeks with rewrites
5. **Delayed expensive implementation** - Build with fakes, swap to real adapters last

### Decision Matrix

| Factor | Use TDD? | TDD Approach | Why? |
|--------|----------|--------------|------|
| **Multiple stakeholders need alignment** | ✅ YES | Specification-First | Tests = executable requirements |
| **Enterprise approval process** | ✅ YES | Outside-In TDD | Tests = documentation for governance |
| **Complex business rules** | ✅ YES | Business-First TDD | Use case tests capture domain knowledge |
| **Expensive external dependencies** | ✅ YES | Fake-First TDD | Test without API costs (OpenAI, S3, etc.) |
| **Parallel team development** | ✅ YES | Interface-First TDD | Agree on contracts, implement independently |
| **Unclear requirements** | ❌ NO | Exploratory Spike | Discover requirements first |
| **Solo dev, 2-3 day spike** | ❌ NO | Direct Implementation | Too much overhead |
| **Simple CRUD, no complex logic** | ⚠️ MAYBE | Light TDD | May not need full approach |

### TDD Workflow Options

**Option A: Full Outside-In TDD**

- Write controller tests with stakeholders (1-hour workshop)
- Write use case tests with domain experts
- Implement with fakes (no external deps)
- Implement real adapters last
- **Timeline:** 1 week, no rewrites
- **Best for:** Enterprise PoCs, multiple stakeholders

**Option B: Light TDD**

- Write use case tests only
- Skip controller tests
- Implement directly with real adapters
- **Timeline:** 1.5 weeks
- **Best for:** Solo dev, clear requirements

**Option C: No TDD**

- Write code first
- Add tests after (if at all)
- **Timeline:** Variable (fast initially, but rewrites add time)
- **Best for:** Exploratory spikes, throwaway code

**Option D: TDD for Critical Paths Only**

- Identify 2-3 most complex/risky features
- Use TDD only for those
- Direct implementation for simple features
- **Timeline:** 1-2 weeks
- **Best for:** Mixed complexity PoCs

### Test Structure Requirements (If using TDD)

**Must have:**

```python
# 1. Protocol ports (enable faking)
class ImageGenPort(Protocol):
    def generate_png(self, prompt: str, size: str) -> bytes: ...

# 2. Fake implementations (not mocks for use cases)
class FakeImageGen:
    def generate_png(self, prompt: str, size: str) -> bytes:
        return f"FAKE_{prompt}_{size}".encode()

# 3. Entity factories
def create_calibration(**overrides) -> Calibration:
    defaults = {...}
    return Calibration(**(defaults | overrides))

# 4. Clear test organization

```

```text
tests/
├── unit/
│   ├── controllers/  # Mock use cases
│   └── use_cases/    # Fake adapters
├── integration/      # Real adapters
└── utils/
    └── entity_factories.py
```

### Example: Stakeholder Workshop Output

**1-hour workshop produces:**

```python
# tests/unit/controllers/test_creative_controller.py

async def test_generate_creatives_for_summer_campaign():
    """Marketing team: Generate Instagram and Facebook ads for summer sale."""
    # Stakeholders define expected behavior
    request = GenerateRequest(
        campaign_message="50% Off All Summer Gear",
        products=["sunglasses", "sandals"],
        aspects=["1:1", "9:16"]  # Instagram post, Story
    )

    response = await controller.generate(request)

    # Assertions stakeholders agreed to:
    assert len(response.assets) == 4  # 2 products × 2 aspects
    assert all(asset.image_url for asset in response.assets)
    assert all("50% Off" in asset.campaign_message for asset in response.assets)

async def test_rejects_invalid_aspect_ratio():
    """Business rule: Only support Instagram/Facebook formats."""
    request = GenerateRequest(aspects=["16:10"])  # Not supported

    with pytest.raises(ValidationError, match="Unsupported aspect ratio"):
        await controller.generate(request)
```

**Then implement to make tests pass.**

### Architectural Impact

**If using TDD (especially Outside-In):**

- ✅ **MUST use controllers** - Tests define controller API
- ✅ **MUST use rich use cases** - Business logic tested before adapters
- ✅ **MUST use protocol ports** - Enable faking
- ✅ **MUST use dependency injection** - Wire fakes, then real implementations
- ✅ **SHOULD use entity factories** - Reusable test data builders

**If NOT using TDD:**

- Can skip controllers (direct driver → use case)
- Can use thin use cases (delegate to adapters)
- Can use concrete classes (no need for protocols)

### Integration with Methodology Phases

**P1: Decomposition**

- [ ] **With TDD:** Write test scenarios in Given/When/Then format
- [ ] **Without TDD:** Write acceptance criteria as prose

**P3: Steel Thread**

- [ ] **With TDD:** Write controller tests, implement with fakes (all tests pass)
- [ ] **Without TDD:** Direct implementation, maybe smoke tests

**P5: Implementation Planning**

- [ ] **With TDD:** Test scenarios become implementation specs
- [ ] **Without TDD:** YAML specs become implementation guide

**P6: Execution**

- [ ] **With TDD:** Red → Green → Refactor cycle
- [ ] **Without TDD:** Implement → Test → Debug cycle

### Decision Questions

1. **Do you need stakeholder sign-off on behavior before expensive implementation?**
   - YES → Use Full Outside-In TDD (Option A)
   - NO → Consider Light TDD or No TDD

2. **Do you have expensive external dependencies (OpenAI, databases)?**
   - YES → Use TDD with fakes
   - NO → TDD less critical

3. **Do you have multiple teams working in parallel?**
   - YES → Use Interface-First TDD
   - NO → TDD optional

4. **Are requirements clear and stable?**
   - NO → Exploratory spike first, then TDD
   - YES → TDD can start immediately

5. **Is this throwaway code?**
   - YES → Skip TDD
   - NO → Consider TDD

### Decision

**Primary decision:**

- [ ] **Full Outside-In TDD** (Workshop → Tests → Fakes → Implementation)
- [ ] **Light TDD** (Use case tests only)
- [ ] **No TDD** (Direct implementation, maybe tests after)
- [ ] **Selective TDD** (Critical paths only)

**If using TDD, which layers get tests first?**

- [ ] Controllers (with stakeholders) → Use Cases → Adapters
- [ ] Use Cases (with domain experts) → Adapters
- [ ] All layers simultaneously (if team has TDD experience)

**Test structure:**

- [ ] Use fakes (InMemoryRepository) for use case tests
- [ ] Use mocks (AsyncMock) for controller tests
- [ ] Create entity factories in tests/utils/

**Integration with phases:**

- [ ] P1: Write test scenarios
- [ ] P3: Implement with fakes (red → green)
- [ ] P6: Implement real adapters (refactor)

**Notes:** _______________________________________________

---

## Decision Area 6: Python Patterns - Which to Include?

**Question:** Which Python patterns should be mandatory vs. optional vs. not recommended?

Review each pattern in `PYTHON_PATTERNS.md` and rate:

### Pattern: Immutable Dataclasses with `slots=True, frozen=True`

```python
@dataclass(slots=True, frozen=True)
class Brief:
    brand_id: str
    campaign_message: str
    products: tuple['Product', ...]  # Note: tuple not list
```

**Rating:**

- [ ] **Mandatory** - All examples should use this
- [ ] **Recommended** - Include in docs as best practice
- [ ] **Optional** - Mention but don't require
- [ ] **Not Recommended** - Overkill for PoCs

**Notes:** _______________________________________________

### Pattern: TypedDict for Payload Composition

```python
class InvokeParams(TypedDict, total=False):
    temperature: float
    top_p: float

def build_payload(model: str, msgs: list[dict], params: InvokeParams) -> dict:
    return {"model": model, **params, "messages": msgs}
```

**Rating:**

- [ ] **Mandatory** - All adapter payload building should use this
- [ ] **Recommended** - Include in adapter examples
- [ ] **Optional** - Mention as advanced pattern
- [ ] **Not Recommended** - Plain dicts are sufficient

**Notes:** _______________________________________________

### Pattern: Singledispatch for Response Normalization

```python
@singledispatch
def normalize_response(response: object) -> DomainResult:
    raise NotImplementedError

@normalize_response.register
def _(resp: OpenAIResponse) -> DomainResult:
    return DomainResult(...)
```

**Rating:**

- [ ] **Mandatory** - Required for multi-provider adapters
- [ ] **Recommended** - Show as pattern for normalization
- [ ] **Optional** - Mention as alternative to if/elif
- [ ] **Not Recommended** - Simple if/elif is clearer

**Notes:** _______________________________________________

### Pattern: Attribute Flattening for Telemetry

```python
def brief_attrs(brief: Brief) -> Iterator[tuple[str, str]]:
    yield ("campaign.brand_id", brief.brand_id)
    # ...

for key, value in (*span_kind_attrs(), *brief_attrs(brief)):
    span.set_attribute(key, value)
```

**Rating:**

- [ ] **Mandatory** - All telemetry should use generators
- [ ] **Recommended** - Include in observability examples
- [ ] **Optional** - Show as advanced pattern
- [ ] **Not Recommended** - Simple dicts are clearer

**Notes:** _______________________________________________

### Pattern: Error Taxonomy with Translation

```python
class AppError(Exception): pass
class ValidationError(AppError): pass
class VendorError(AppError): pass

# In adapter:
except OpenAIError as e:
    raise VendorError(f"OpenAI error: {e}", vendor_error=e)

# In driver:
except ValidationError as e:
    raise HTTPException(status_code=422, detail=str(e))
```

**Rating:**

- [ ] **Mandatory** - Required error handling pattern
- [ ] **Recommended** - Include in all examples
- [ ] **Optional** - Show for complex PoCs
- [ ] **Not Recommended** - Let exceptions bubble

**Notes:** _______________________________________________

### Pattern: LRU-Cached Settings

```python
@lru_cache(maxsize=1)
def get_settings() -> Settings:
    return Settings(
        openai_api_key=os.environ["OPENAI_API_KEY"],
        # ...
    )
```

**Rating:**

- [ ] **Mandatory** - All config loading should use this
- [ ] **Recommended** - Include in config examples
- [ ] **Optional** - Show as optimization
- [ ] **Not Recommended** - Direct env access is fine

**Notes:** _______________________________________________

### Pattern: DTOs (Pydantic) vs Domain (Dataclasses) Separation

```python
# drivers/api/schemas.py
class BriefPayload(BaseModel):  # Pydantic at edge
    brand_id: str
    # ...

# domain/models.py
@dataclass(slots=True, frozen=True)
class Brief:  # Pure dataclass in domain
    brand_id: str
    # ...

# Controller converts: payload_to_brief(payload: BriefPayload) -> Brief
```

**Rating:**

- [ ] **Mandatory** - Always separate DTOs from domain
- [ ] **Recommended** - Include in examples with FastAPI
- [ ] **Optional** - Show for complex validation scenarios
- [ ] **Not Recommended** - Use Pydantic everywhere

**Notes:** _______________________________________________

### Pattern: Protocol Ports with Rich Type Annotations

```python
class VectorStorePort(Protocol):
    def embed(self, texts: Iterable[str]) -> Sequence[Sequence[float]]:
        """Docstring explaining contract."""
        ...
```

**Rating:**

- [ ] **Mandatory** - All ports must use Protocol with rich types
- [ ] **Recommended** - Include in all port examples
- [ ] **Optional** - Mention as best practice
- [ ] **Not Recommended** - Simple ABC is sufficient

**Notes:** _______________________________________________

---

## Decision Area 4: Example Complexity

**Question:** How complex should the example code in STRUCTURE.md be?

### Options

**Option A: Minimal (conceptual)**

```python
# Just show the signature
class ImageGenPort(Protocol):
    def generate_png(self, prompt: str, size: str) -> bytes: ...
```

**Option B: Basic (with docstrings)**

```python
class ImageGenPort(Protocol):
    """Port for image generation services."""
    def generate_png(self, prompt: str, size: str) -> bytes:
        """Generate a PNG image from a text prompt."""
        ...
```

**Option C: Complete (production-ready)**

```python
class ImageGenPort(Protocol):
    """Port for image generation services.

    Use-cases depend on this protocol without importing vendor SDKs.
    Implementations live in infrastructure/adapters/.
    """
    def generate_png(self, prompt: str, size: str) -> bytes:
        """Generate a PNG image from a text prompt.

        Args:
            prompt: Text description of the image to generate.
            size: Image dimensions (e.g., "1024x1024").

        Returns:
            PNG image data as bytes.

        Raises:
            VendorError: If image generation fails.
        """
        ...
```

**Decision:**

- [ ] Option A: Keep examples minimal and conceptual
- [ ] Option B: Add basic docstrings for clarity
- [ ] Option C: Show production-ready examples
- [ ] Mix: Conceptual in STRUCTURE.md, complete in separate examples/

**Notes:** _______________________________________________

---

## Decision Area 5: Testing Strategy Detail Level

**Question:** How much testing guidance should be included?

### Current Coverage in STRUCTURE.md

- ✅ Unit test examples (mock-based)
- ✅ Integration test examples (adapter testing)
- ✅ E2E test examples (CLI workflow)

### Additional Options to Consider

- [ ] Add pytest fixtures for common test setup
- [ ] Add conftest.py pattern for shared fixtures
- [ ] Add snapshot testing examples (mentioned in style guide)
- [ ] Add test data factory patterns
- [ ] Add coverage requirements/goals
- [ ] Add testing decision tree (what to unit vs integration test)

**Decision:**

- [ ] Keep current level (examples only)
- [ ] Add moderate detail (fixtures, conftest patterns)
- [ ] Add comprehensive testing guide (separate doc)
- [ ] Minimal (remove to separate testing doc)

**Notes:** _______________________________________________

---

## Decision Area 6: Observability Patterns

**Question:** How should observability be positioned?

### Options

**Option A: Core Feature (always included)**

- Observability patterns in baseline structure
- All examples show telemetry
- Phase 6+ requires observability

**Option B: Optional Add-on (Phase 8 focus)**

- Steel thread has no observability
- Add observability in Phase 8
- Examples show with/without telemetry

**Option C: Pragmatic Middle Ground**

- Basic logging from the start
- Structured telemetry in Phase 6+
- OpenTelemetry/Phoenix in Phase 8

**Decision:**

- [ ] Option A: Always include observability
- [ ] Option B: Optional, Phase 8 focus
- [ ] Option C: Progressive observability
- [ ] Other: _________________

**Notes:** _______________________________________________

---

## Phase 3: Make Key Decisions (30-45 min)

### Decision Summary Template

Fill this out after completing Phase 2:

```yaml
decisions:
  base_structure:
    choice: [steel_thread | pragmatic_ca | full_ca | progressive]
    reasoning: |


  layer_naming:
    choice: [5_layer_entities | 4_layer_domain | flexible_4_5]
    reasoning: |


  controllers_vs_mappers:  # NEW
    choice: [always_controllers | never_controllers | progressive | dual_patterns]
    terminology: [handlers | mappers | adapters | routers | controllers_clarified]
    reasoning: |


  use_case_complexity:  # NEW
    poc_phases: [thin_allowed | rich_required | mixed]
    production_phases: [rich_required | thin_if_simple | case_by_case]
    reasoning: |


  tdd_strategy:  # NEW - Enterprise acceleration
    choice: [full_outside_in | light_tdd | no_tdd | selective_tdd]
    layers_tested_first: [controllers_then_use_cases | use_cases_only | all_layers]
    test_structure: [fakes_for_use_cases | mocks_everywhere | mixed]
    phase_integration: [p1_scenarios | p3_tests_first | p6_tests_after]
    reasoning: |


  mandatory_patterns:
    - pattern: immutable_dataclasses
      status: [mandatory | recommended | optional | not_recommended]
    - pattern: typed_dict_payloads
      status: [mandatory | recommended | optional | not_recommended]
    - pattern: singledispatch_normalization
      status: [mandatory | recommended | optional | not_recommended]
    - pattern: attribute_flattening
      status: [mandatory | recommended | optional | not_recommended]
    - pattern: error_taxonomy
      status: [mandatory | recommended | optional | not_recommended]
    - pattern: lru_cached_settings
      status: [mandatory | recommended | optional | not_recommended]
    - pattern: dto_domain_separation
      status: [mandatory | recommended | optional | not_recommended]
    - pattern: protocol_rich_typing
      status: [mandatory | recommended | optional | not_recommended]

  example_complexity:
    choice: [minimal | basic | complete | mixed]
    reasoning: |


  testing_detail:
    choice: [current | moderate | comprehensive | minimal]
    reasoning: |


  observability_approach:
    choice: [always_included | optional_phase8 | progressive]
    reasoning: |

```

---

## Phase 4: Create Final Recommendations (1-2 hours)

Based on your decisions, choose an approach:

### Approach A: Single Recommended Structure

- Create one definitive structure recommendation
- Document as "the way" to structure PoCs
- Pros: Clear, opinionated, easy to follow
- Cons: May not fit all use cases

### Approach B: Tiered Recommendations

- Provide 2-3 structures for different scenarios
- Decision tree to help choose
- Pros: Flexible, accommodates different needs
- Cons: More complex, requires judgment

### Approach C: Progressive Revelation

- Start simple, evolve through phases
- Steel thread → Pragmatic CA → Full CA
- Pros: Matches methodology phases, gradual learning
- Cons: Requires refactoring between phases

**Recommended approach:**

- [ ] Approach A: Single structure
- [ ] Approach B: Tiered (2-3 options)
- [x] Approach C: Progressive (evolve through phases)
- [ ] Hybrid: _________________

---

## Phase 5: Update Documentation (1-2 hours)

### Files to Update

1. **`CLAUDE.md`** (primary source of truth)
   - [ ] Update "Proposed App Structure" section
   - [ ] Add decided Python patterns
   - [ ] Update "Technology Stack" section
   - [ ] Update "Key Principles" if needed
   - [ ] Add examples for chosen patterns

2. **`README.md`** (if it references structure)
   - [ ] Update structure references
   - [ ] Ensure consistency with CLAUDE.md

3. **Create new files (if needed):**
   - [ ] `docs/STRUCTURE_EXAMPLES.md` - Complete working examples
   - [ ] `docs/PYTHON_PATTERNS.md` - Pattern reference guide
   - [ ] `templates/` - Project scaffolding templates

### Content to Archive

- [ ] Move `plan/STRUCTURE.md` → `docs/STRUCTURE_ANALYSIS.md` (reference)
- [ ] Move `plan/drafts/PYTHON_PATTERNS.md` → `docs/PYTHON_PATTERNS.md` (reference)
- [ ] Keep `plan/drafts/REVIEW_GUIDE.md` for next review cycle

---

## Quick Decision Shortcuts

If you're short on time, use these heuristics:

### For AI/LLM-Focused PoCs (typical case - RECOMMENDED DEFAULT)

```yaml
base_structure: progressive  # Start simple, evolve to pragmatic/full CA
layer_naming: 4_layer_domain  # domain/, application/, infrastructure/, drivers/

# NEW: Controller decisions
controllers_vs_mappers:
  choice: progressive  # Start without controllers, add in Phase 7+ if needed
  terminology: handlers  # Call them "handlers" in drivers/ layer

use_case_complexity:
  poc_phases: thin_allowed  # Focus on speed
  production_phases: rich_required  # Add business logic when productionizing

# NEW: TDD strategy
tdd_strategy:
  choice: selective_tdd  # Use TDD for critical/complex features only
  layers_tested_first: use_cases_only  # Focus on business logic
  test_structure: fakes_for_use_cases  # InMemory fakes, not mocks
  phase_integration: p3_tests_first  # Write tests in steel thread phase

mandatory_patterns:
  - immutable_dataclasses: recommended  # @dataclass(slots=True, frozen=True)
  - typed_dict_payloads: recommended    # For adapter payload building
  - singledispatch_normalization: optional  # Use if multi-provider
  - attribute_flattening: recommended   # For telemetry generators
  - error_taxonomy: recommended         # AppError hierarchy
  - lru_cached_settings: mandatory      # Always cache settings
  - dto_domain_separation: recommended  # Pydantic at edges, dataclasses in domain
  - protocol_rich_typing: recommended   # Protocol ports with Iterable/Sequence

example_complexity: basic  # Show docstrings but keep concise
testing_detail: current  # Unit, integration, e2e examples
observability_approach: progressive  # Logging → structured → OpenTelemetry
```

**Rationale:** Balances speed (thin use cases, no controllers initially) with structure (ports/adapters, immutable domain). Matches methodology phases - evolve complexity as PoC matures.

### For Quick Prototyping (2-3 days)

```yaml
base_structure: steel_thread  # Minimal - app/core, app/adapters, cli.py
layer_naming: 4_layer_domain  # Simplified naming

# NEW: No controllers, inline everything
controllers_vs_mappers:
  choice: never_controllers  # Direct wiring in CLI
  terminology: n/a  # No separate boundary layer

use_case_complexity:
  poc_phases: thin_allowed  # Very thin, almost functions
  production_phases: n/a  # This is throwaway code

# NEW: TDD strategy
tdd_strategy:
  choice: no_tdd  # Skip tests entirely for speed
  layers_tested_first: n/a
  test_structure: n/a
  phase_integration: p6_tests_after  # Maybe add smoke tests at end

mandatory_patterns:
  - immutable_dataclasses: optional  # Speed over safety
  - typed_dict_payloads: optional
  - singledispatch_normalization: optional
  - attribute_flattening: optional
  - error_taxonomy: optional
  - lru_cached_settings: recommended  # Still cache settings
  - dto_domain_separation: optional
  - protocol_rich_typing: optional

example_complexity: minimal  # Just signatures
testing_detail: minimal  # Maybe smoke tests
observability_approach: optional_phase8  # Skip unless critical
```

**Rationale:** Maximum speed. Prove concept quickly. Accept that this may be throwaway code. Good for experiments and spikes.

---

### For Production-Grade from Start

```yaml
base_structure: full_ca  # 5-layer with entities/, application/, interface_adapters/, infrastructure/, drivers/
layer_naming: 5_layer_entities  # Classic CA terminology

# NEW: True orchestration controllers from day 1
controllers_vs_mappers:
  choice: always_controllers  # Full interface_adapters/controllers/ layer
  terminology: controllers  # Use Uncle Bob's terminology

use_case_complexity:
  poc_phases: rich_required  # Always have business logic in use cases
  production_phases: rich_required  # Maintain richness

# NEW: TDD strategy


tdd_strategy:
  choice: full_outside_in  # Enterprise-grade TDD from day 1
  layers_tested_first: controllers_then_use_cases  # Complete coverage
  test_structure: fakes_for_use_cases  # Proper fakes, mocks only for controllers
  phase_integration: p1_scenarios  # Write test scenarios with stakeholders in P1

mandatory_patterns:
  - immutable_dataclasses: mandatory  # All domain objects immutable
  - typed_dict_payloads: mandatory  # Type-safe payload building
  - singledispatch_normalization: recommended  # Plan for multiple providers
  - attribute_flattening: mandatory  # Structured telemetry from day 1
  - error_taxonomy: mandatory  # Complete error hierarchy
  - lru_cached_settings: mandatory  # Cached configuration
  - dto_domain_separation: mandatory  # Strict boundary separation
  - protocol_rich_typing: mandatory  # Complete type annotations

example_complexity: complete  # Production-ready with full docstrings
testing_detail: comprehensive  # Unit, integration, e2e, plus fixtures
observability_approach: always_included  # OpenTelemetry from day 1
```

**Rationale:** No shortcuts. Build it right from the start. Appropriate when PoC will become production code or when learning Clean Architecture is a goal.

---

### For Enterprise PoCs with Multiple Stakeholders (NEW - TDD FOCUS)

```yaml
base_structure: full_ca  # Need controllers for TDD specification layer
layer_naming: 5_layer_entities  # Classic CA with interface_adapters/

# CRITICAL: Controllers required for TDD
controllers_vs_mappers:
  choice: always_controllers  # TDD needs controller API for stakeholder tests
  terminology: controllers  # Use Uncle Bob's terminology

use_case_complexity:
  poc_phases: rich_required  # Business logic in use cases (testable before adapters)
  production_phases: rich_required  # Maintain richness

# TDD IS THE KEY DIFFERENTIATOR
tdd_strategy:
  choice: full_outside_in  # GAME CHANGER for enterprise PoCs
  layers_tested_first: controllers_then_use_cases  # Stakeholder workshop → controller tests → use case tests
  test_structure: fakes_for_use_cases  # Develop with $0 API costs
  phase_integration: p1_scenarios  # Write test scenarios with stakeholders in P1

mandatory_patterns:
  - immutable_dataclasses: mandatory
  - typed_dict_payloads: recommended
  - singledispatch_normalization: optional
  - attribute_flattening: recommended
  - error_taxonomy: mandatory
  - lru_cached_settings: mandatory
  - dto_domain_separation: mandatory  # CRITICAL for TDD (Pydantic at edges, dataclasses in domain)
  - protocol_rich_typing: mandatory  # CRITICAL for TDD (enable fakes)

example_complexity: complete  # Full docstrings for clarity
testing_detail: comprehensive  # Full test suite is the deliverable
observability_approach: progressive  # Add later, focus on tests first
```

**Why TDD for Enterprise:**

- **50-75% faster delivery:** 1 week (TDD) vs 2-4 weeks (traditional with rewrites)
- **Zero rework:** Stakeholders approve tests upfront → build exactly what was specified
- **$0 development costs:** Build with fakes (InMemoryRepository, FakeImageGen), only test real adapters at end
- **Parallel development:** Teams implement adapters independently after interface agreed
- **Governance friendly:** Tests = executable documentation for approval processes

**Workflow:**

1. **Day 1:** Stakeholder workshop (3 hours) → Write controller tests together → Sign-off
2. **Day 2:** Implement controllers + use cases with fakes (tests pass!) → Demo to stakeholders
3. **Days 3-4:** Teams implement real adapters in parallel (OpenAI, S3, Weaviate)
4. **Day 5:** Integration + final demo (tests still pass)

**Rationale:** When you need stakeholder sign-off, have expensive dependencies (OpenAI, databases), or multiple teams working in parallel, TDD transforms the PoC process from "build and hope" to "specify then build."

**See:** `plan/drafts/TDD_STAKEHOLDER_ACCELERATION_INSIGHTS.md` for complete case study

---

## Next Actions Checklist

After completing this review:

- [ ] Fill out decision summary template
- [ ] Choose documentation approach (A/B/C/Hybrid)
- [ ] Update `CLAUDE.md` with final decisions
- [ ] Create code examples/templates
- [ ] Archive analysis documents
- [ ] Test proposed structure with a real PoC
- [ ] Gather feedback and iterate

---

## Questions to Ask Yourself

As you review, keep asking:

1. **Simplicity:** Is this the simplest thing that could work?
2. **Teachability:** Can I explain this to a new team member in 15 minutes?
3. **Flexibility:** Does this accommodate 80% of my use cases?
4. **Maintainability:** Will I curse myself 6 months from now?
5. **Pragmatism:** Am I over-engineering for a PoC context?
6. **Consistency:** Does this align with lean-clean principles?

---

**Last Updated:** 2025-10-12
**Status:** Review Guide (Updated with TDD Enterprise Acceleration)
**Next Review:** After implementing decisions in a real PoC
