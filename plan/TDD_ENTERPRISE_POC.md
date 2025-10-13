# TDD for Enterprise-Level PoCs: Specification-First Development

> **⚠️ SUPERSEDED BY SESSION 01**
>
> This document was created on 2025-10-12, before Session 01 architectural decisions (2025-10-13).
>
> **Session 01 now authoritatively defines:**
> - **pytest vs Gherkin** → See `/plan/_session-01-architectural-decisions.md` Q3 (pytest with BDD docstrings)
> - **Feature/Controller concept** → See Session 01 Q4 (Feature maps to Controller)
> - **TDD approach integration** → Deferred to future session
>
> This file remains for TDD workflow patterns and stakeholder workshop examples but **defer to Session 01 for architectural decisions.**

## Executive Summary

**Key Insight:** The calibration-service controller pattern enables **Outside-In TDD**, where you can write specifications as tests with stakeholders BEFORE expensive implementation. This dramatically accelerates enterprise PoC delivery by:

1. **Getting stakeholder buy-in early** - Tests become executable specifications
2. **Delaying expensive implementation** - Write interfaces/tests first, implement adapters later
3. **Catching misunderstandings early** - Failed tests reveal requirement gaps
4. **Enabling parallel work** - Different teams can implement adapters once interfaces are agreed

## The TDD Pattern in calibration-service

### Test Structure
```
tests/
├── unit/
│   ├── application/use_cases/       # Test business logic with fakes
│   ├── interface_adapters/controllers/  # Test orchestration with mocks
│   └── infrastructure/repositories/     # Test adapter implementations
├── integration/                     # Test cross-layer interactions
├── e2e/                            # Test full system workflows
└── utils/
    └── entity_factories.py         # Reusable test data builders
```

### Layer-by-Layer TDD Strategy

#### 1. Controller Tests (Specification Layer)
**File:** `test_add_calibration_controller.py`

**What it tests:**
- API contract: Given valid request → returns ID
- Error handling: Given invalid input → returns 422
- Orchestration: Converts Request → Input DTO → calls use case → presents response

**Key characteristics:**
```python
@pytest.fixture
def mock_add_calibration_use_case() -> AsyncMock:
    return AsyncMock(spec=AddCalibrationUseCase)  # MOCKED - doesn't exist yet!

async def test_create_calibration_success(
    add_calibration_controller: AddCalibrationController,
    mock_add_calibration_use_case: AsyncMock,
    valid_request: CalibrationCreateRequest,  # Pydantic DTO
):
    # ARRANGE: Define expected behavior
    mock_output = AddCalibrationOutput(created_calibration=sample_calibration)
    mock_add_calibration_use_case.return_value = mock_output

    # ACT: Call controller
    response = await add_calibration_controller.create_calibration(valid_request)

    # ASSERT: Verify contract
    mock_add_calibration_use_case.assert_awaited_once()
    assert isinstance(call_args, AddCalibrationInput)  # Correct DTO conversion
    assert response == expected_response
```

**Can be written with stakeholders:** ✅ YES!
- "Given a valid calibration request..."
- "When I create a calibration..."
- "Then I get back the calibration ID"
- Non-technical stakeholders can participate in defining these scenarios

#### 2. Use Case Tests (Business Logic Layer)
**File:** `add_calibration_use_case_test.py`

**What it tests:**
- Domain rules: Valid input creates entity
- Validation: Invalid type raises InputParseError
- Business constraints: Duplicate ID raises DatabaseOperationError

**Key characteristics:**
```python
@pytest.fixture
def calibration_repository() -> CalibrationRepository:
    repo = InMemoryCalibrationRepository()  # FAKE - no database needed!
    repo.clear()
    return repo

async def test_calibration_successfully_created_with_valid_input(
    add_calibration_use_case: AddCalibrationUseCase,
):
    # ARRANGE
    input_data = AddCalibrationInput(
        calibration_type="gain",
        value=1.0,
        timestamp_str="2023-01-01T12:00:00Z",
        username="test_user",
    )

    # ACT
    result_output = await add_calibration_use_case(input_data)

    # ASSERT: Verify entity creation
    assert result_calibration.measurement.value == 1.0
    assert result_calibration.measurement.type == CalibrationType.gain
    assert len(result_calibration.tags) == 0  # Business rule: new calibrations have no tags
```

**Can be written with domain experts:** ✅ YES!
- "A gain calibration has a measurement value and type"
- "New calibrations start with zero tags"
- "Invalid calibration types are rejected"

#### 3. Entity Factories (Test Data Builders)
**File:** `entity_factories.py`

```python
def create_calibration(
    calibration_id: UUID | None = None,
    measurement: Measurement | None = None,
    timestamp: Iso8601Timestamp | None = None,
    username: str = "test_user",
    tags: list[Tag] | None = None,
) -> Calibration:
    """Create test calibration with sensible defaults."""
    if calibration_id is None:
        calibration_id = UUID("11111111-1111-1111-1111-111111111111")
    # ...
    return Calibration(...)
```

**Purpose:** Reusable builders that express intent, not data
- `create_calibration()` - default valid calibration
- `create_calibration(measurement=Measurement(type=CalibrationType.offset))` - specific scenario

---

## The TDD Workflow for Enterprise PoCs

### Phase 0: Requirements Gathering (Before P0-P1)

**Traditional approach:**
1. Write requirements doc
2. Start coding
3. Build wrong thing
4. Rewrite after demo failure

**TDD approach:**
1. Write requirements doc
2. **Write controller tests AS specifications**
3. Get stakeholder sign-off on tests
4. Then start coding

### Phase Mapping: TDD Integration

#### P0-P1: Context & Decomposition (NEW: Add Test Scenarios)
```markdown
# P1: Decomposition Output

## Acceptance Criteria (Executable)

```python
# test_creative_generation_controller.py
async def test_generate_creatives_for_valid_brief():
    """AC1: Given valid brief with 2 products, generates 6 assets (3 aspects each)."""
    # ARRANGE
    brief = BriefPayload(
        brand_id="test-brand",
        campaign_message="Summer Sale",
        products=[
            ProductPayload(id="prod-1", name="Widget"),
            ProductPayload(id="prod-2", name="Gadget"),
        ],
        aspects=["1:1", "9:16", "16:9"]
    )

    # ACT
    response = await controller.generate(brief)

    # ASSERT
    assert len(response.assets) == 6  # 2 products × 3 aspects
    assert all(asset.image_url for asset in response.assets)
```

**Stakeholder session output:** Test suite that defines success
```

#### P2-P3: Ideation & Steel Thread (Write Interface Contracts)

**Step 1: Define interfaces**
```python
# application/ports/image_gen.py
class ImageGenPort(Protocol):
    def generate_png(self, prompt: str, size: str) -> bytes: ...

# application/use_cases/generate_creatives.py
class GenerateCreativesUseCase:
    def __init__(self, image_gen: ImageGenPort): ...
    async def execute(self, brief: Brief) -> List[Asset]: ...
```

**Step 2: Write controller tests (with mocks)**
```python
@pytest.fixture
def mock_image_gen() -> Mock:
    mock = Mock(spec=ImageGenPort)
    mock.generate_png.return_value = b"fake_png_data"
    return mock

async def test_controller_orchestrates_generation(mock_image_gen):
    # Test BEFORE implementing the use case or adapters
    ...
```

**Step 3: Write use case tests (with fakes)**
```python
class FakeImageGen:
    """In-memory fake for testing."""
    def generate_png(self, prompt: str, size: str) -> bytes:
        return f"fake_{prompt}_{size}".encode()

async def test_use_case_generates_for_each_product(fake_image_gen):
    # Test business logic BEFORE implementing real adapter
    ...
```

**Deliverable:** Comprehensive test suite that documents behavior

#### P4-P5: Implementation Planning → Implementation Specification

**Traditional:**
```yaml
# use_case.yaml
name: generate_creatives
inputs: [brief]
outputs: [assets]
```

**TDD-Enhanced:**
```yaml
# use_case.yaml
name: generate_creatives
inputs: [brief]
outputs: [assets]
test_scenarios:
  - name: valid_brief_generates_assets
    given: "brief with 2 products, 3 aspects"
    when: "execute use case"
    then: "returns 6 assets with image URLs"
    test_file: "tests/unit/use_cases/test_generate_creatives.py::test_valid_brief"

  - name: invalid_aspect_raises_error
    given: "brief with invalid aspect '4:3'"
    when: "execute use case"
    then: "raises ValidationError"
    test_file: "tests/unit/use_cases/test_generate_creatives.py::test_invalid_aspect"
```

#### P6: Execution (Red-Green-Refactor)

**Workflow:**
1. **Red:** Run existing controller/use case tests (they fail - no implementation)
2. **Green:** Implement use cases with fake adapters (tests pass)
3. **Refactor:** Implement real adapters (integration tests)
4. **Green:** Run full test suite (all pass)

**Parallelization opportunity:**
- Team A: Implements use cases with fakes
- Team B: Implements real adapters (OpenAI, S3, etc.)
- Integration happens when both done

#### P7: Reflection (Test Coverage Analysis)

Add test metrics to reflection:
```markdown
## Test Coverage
- Controller tests: 15 scenarios, 100% coverage
- Use case tests: 23 scenarios, 95% coverage
- Integration tests: 8 scenarios
- E2E tests: 3 happy paths

## Gaps Identified
- Missing error scenario: Rate limit handling
- Missing edge case: Empty product list
```

---

## When to Use TDD in PoCs

### Decision Matrix

| Scenario | Use TDD? | Rationale |
|----------|---------|-----------|
| **Multiple stakeholders need alignment** | ✅ YES | Tests become executable specs, reduce miscommunication |
| **Complex business rules** | ✅ YES | Use case tests document rules, prevent regression |
| **Expensive external dependencies** | ✅ YES | Fake adapters enable testing without API costs |
| **Parallel development (multiple teams)** | ✅ YES | Interfaces defined upfront, teams work independently |
| **PoC will become production** | ✅ YES | Test suite provides safety net for future changes |
| **Enterprise approval process** | ✅ YES | Tests as documentation for governance |
| **2-3 day spike** | ❌ NO | Too much overhead for throwaway code |
| **Solo developer, simple CRUD** | ⚠️ MAYBE | Depends on whether spec clarity is needed |
| **Unclear requirements** | ⚠️ MAYBE | May need exploratory spike first |

### The "Stakeholder Workshop" Pattern

**Scenario:** You have 3 hours with stakeholders next Tuesday.

**Traditional approach:**
- Show wireframes
- Get vague feedback
- Build wrong thing

**TDD approach:**
1. **Before meeting:** Create controller test skeletons
   ```python
   async def test_user_can_generate_creative():
       # TODO: Define with stakeholders
       pass
   ```

2. **During meeting:** Fill in test scenarios together
   ```python
   async def test_user_can_generate_creative():
       """Stakeholder: 'Marketing user uploads product image,
       enters campaign text, gets 3 social media formats'"""
       # ARRANGE
       request = GenerateRequest(
           product_image=upload_file("product.jpg"),
           campaign_text="50% Off Summer Sale",
           formats=["instagram_post", "facebook_ad", "twitter_card"]
       )
       # ACT
       response = await controller.generate(request)
       # ASSERT
       assert len(response.creatives) == 3
       assert response.creatives[0].format == "instagram_post"
       # Stakeholder can verify this matches their expectation
   ```

3. **After meeting:** Implement to make tests pass

**Result:** Executable requirements that stakeholders approved

---

## TDD Architecture Requirements

### Must-Haves for TDD to Work

#### 1. Interface Segregation
```python
# ✅ GOOD: Small, testable interfaces
class ImageGenPort(Protocol):
    def generate_png(self, prompt: str, size: str) -> bytes: ...

# ❌ BAD: Monolithic interface
class AIServicePort(Protocol):
    def generate_image(self, ...): ...
    def enhance_image(self, ...): ...
    def analyze_image(self, ...): ...
    def train_model(self, ...): ...  # Can't fake this!
```

#### 2. Dependency Injection
```python
# ✅ GOOD: Dependencies injected
class GenerateCreativesUseCase:
    def __init__(
        self,
        image_gen: ImageGenPort,  # Injected - can be fake
        vector_store: VectorStorePort,  # Injected - can be fake
    ):
        self._image_gen = image_gen
        self._vector_store = vector_store

# ❌ BAD: Hard-coded dependencies
class GenerateCreativesUseCase:
    def __init__(self):
        self._image_gen = OpenAIImageGen()  # Can't fake!
        self._vector_store = WeaviateStore()  # Can't fake!
```

#### 3. Fake Implementations (Not Mocks for Use Cases)
```python
# ✅ GOOD: Fake with real behavior
class InMemoryCalibrationRepository:
    def __init__(self):
        self._store: Dict[UUID, Calibration] = {}

    async def add_calibration(self, cal: Calibration) -> Calibration:
        if cal.id in self._store:
            raise DatabaseOperationError("Already exists")
        self._store[cal.id] = cal
        return cal

    async def get_by_id(self, id: UUID) -> Calibration | None:
        return self._store.get(id)

# ❌ BAD: Mock for everything
mock_repo = Mock()
mock_repo.add_calibration.return_value = fake_calibration
# ^ Doesn't test repository logic
```

**Why fakes > mocks for use cases:**
- Fakes test actual behavior (including edge cases)
- Mocks only test "was method called"
- Fakes catch bugs in repository logic

**When to use mocks:**
- Controllers (test orchestration, not implementation)
- External services (avoid real API calls)

#### 4. Entity Factories
```python
# tests/utils/entity_factories.py
def create_calibration(**overrides) -> Calibration:
    """Builder pattern for test data."""
    defaults = {
        "calibration_id": UUID("11111111-1111-1111-1111-111111111111"),
        "measurement": Measurement(value=1.0, type=CalibrationType.gain),
        "timestamp": Iso8601Timestamp("2024-01-01T12:00:00Z"),
        "username": "test_user",
        "tags": [],
    }
    return Calibration(**(defaults | overrides))

# Usage:
normal_cal = create_calibration()
offset_cal = create_calibration(measurement=Measurement(type=CalibrationType.offset))
```

---

## Impact on Structure Recommendations

### UPDATED Recommendation: Controllers for Enterprise PoCs

Based on TDD requirements, **add this decision criterion**:

**Use Controllers (Full CA) when:**
- ✅ Multiple stakeholders need spec alignment
- ✅ Enterprise approval process
- ✅ Complex domain rules
- ✅ Parallel team development
- ✅ **Specification-first development desired**

**Skip Controllers (Thin/Progressive CA) when:**
- Solo developer
- Simple CRUD
- 2-3 day spike
- No stakeholder alignment needed
- Exploratory/experimental PoC

### Updated Phase Structure

#### P1: Decomposition
**ADD:** "Test Scenario Definition" sub-phase
- Write Given/When/Then for each acceptance criteria
- Create test skeletons (not full tests yet)
- Get stakeholder sign-off

#### P3: Steel Thread
**OPTION A:** TDD Steel Thread
- Write controller tests first
- Implement with fakes
- **Deliverable:** Passing test suite + fake implementation

**OPTION B:** Exploratory Steel Thread
- Direct implementation
- Add tests after (if at all)
- **Deliverable:** Working code (no tests)

---

## Recommended Testing Strategy by Phase

### Phase 3-4: Steel Thread
```yaml
testing_approach: exploratory
rationale: "Proving concept, requirements unclear"
tests:
  - smoke_tests: optional
  - controller_tests: skip
  - use_case_tests: skip
```

### Phase 5: Implementation Planning
```yaml
testing_approach: specification_first
rationale: "Requirements clear, stakeholder buy-in needed"
tests:
  - controller_tests: write_first  # Define spec
  - use_case_tests: write_first    # Define business rules
  - adapter_tests: write_during    # Implement with TDD
```

### Phase 6: Execution
```yaml
testing_approach: tdd
workflow:
  1. red: "Run existing controller/use case tests (fail)"
  2. green: "Implement use cases with fakes (pass)"
  3. refactor: "Implement real adapters"
  4. green: "Run full suite (pass)"
```

### Phase 7+: Production
```yaml
testing_approach: comprehensive
requirements:
  - unit_coverage: "> 80%"
  - integration_tests: "all adapters"
  - e2e_tests: "critical paths"
  - mutation_testing: optional
```

---

## Example: TDD Workflow for Creative Generation PoC

### Step 1: Stakeholder Workshop (1 hour)

**Output:** Test scenarios
```python
# tests/unit/controllers/test_creative_controller.py

async def test_generate_creatives_for_summer_campaign():
    """Marketing team: Generate Instagram and Facebook ads for summer sale."""
    request = GenerateRequest(
        brand_id="acme-corp",
        campaign_message="50% Off All Summer Gear",
        products=[
            ProductPayload(id="sunglasses", name="Polarized Sunglasses"),
            ProductPayload(id="sandals", name="Beach Sandals"),
        ],
        aspects=["1:1", "9:16"]  # Instagram post, Story
    )

    response = await controller.generate(request)

    # Assertions defined with stakeholders:
    assert len(response.assets) == 4  # 2 products × 2 aspects
    assert all(asset.image_url.startswith("https://") for asset in response.assets)
    assert all(asset.brand_id == "acme-corp" for asset in response.assets)

async def test_rejects_invalid_aspect_ratio():
    """Business rule: Only support Instagram/Facebook formats."""
    request = GenerateRequest(
        aspects=["16:10"]  # Not supported
    )

    with pytest.raises(ValidationError, match="Unsupported aspect ratio"):
        await controller.generate(request)
```

### Step 2: Define Interfaces (30 minutes)

```python
# application/ports/image_gen.py
class ImageGenPort(Protocol):
    def generate_png(self, prompt: str, size: str) -> bytes: ...

# application/ports/vector_store.py
class VectorStorePort(Protocol):
    def find_existing(self, product_id: str, region: str, aspect: str) -> List[Asset]: ...
    def upsert(self, asset: Asset, image_bytes: bytes) -> None: ...
```

### Step 3: Write Use Case Tests (1 hour)

```python
# tests/unit/use_cases/test_generate_creatives.py

class FakeImageGen:
    def generate_png(self, prompt: str, size: str) -> bytes:
        return f"FAKE_IMAGE_{prompt}_{size}".encode()

class FakeVectorStore:
    def __init__(self):
        self._store: Dict[str, Asset] = {}

    def find_existing(self, product_id, region, aspect):
        key = f"{product_id}_{region}_{aspect}"
        return [self._store[key]] if key in self._store else []

    def upsert(self, asset, image_bytes):
        key = f"{asset.product_id}_{asset.region}_{asset.aspect}"
        self._store[key] = asset

async def test_generates_asset_for_each_product_aspect(
    fake_image_gen, fake_vector_store
):
    """Business rule: Generate one asset per product × aspect combination."""
    brief = Brief(
        brand_id="test",
        campaign_message="Sale",
        products=[
            Product(id="p1", name="Widget"),
            Product(id="p2", name="Gadget"),
        ],
        aspects=["1:1", "9:16"]
    )

    use_case = GenerateCreativesUseCase(fake_image_gen, fake_vector_store)

    assets = await use_case.execute(brief)

    assert len(assets) == 4  # 2 × 2
    assert fake_image_gen.generate_png.call_count == 4

async def test_reuses_existing_assets_from_vector_store():
    """Business rule: Don't regenerate if asset already exists."""
    # Setup: Pre-populate vector store
    existing_asset = Asset(product_id="p1", aspect="1:1", image_url="existing.png", reused=True)
    fake_vector_store.upsert(existing_asset, b"fake")

    brief = Brief(products=[Product(id="p1")], aspects=["1:1"])
    use_case = GenerateCreativesUseCase(fake_image_gen, fake_vector_store)

    assets = await use_case.execute(brief)

    assert assets[0].reused == True
    assert fake_image_gen.generate_png.call_count == 0  # Not called!
```

### Step 4: Implement Use Case (2 hours)

```python
# application/use_cases/generate_creatives.py
class GenerateCreativesUseCase:
    def __init__(
        self,
        image_gen: ImageGenPort,
        vector_store: VectorStorePort,
    ):
        self._image_gen = image_gen
        self._vector_store = vector_store

    async def execute(self, brief: Brief) -> List[Asset]:
        assets = []

        for product in brief.products:
            for aspect in brief.aspects:
                # Check if exists
                existing = self._vector_store.find_existing(
                    product.id, brief.target_region, aspect
                )

                if existing:
                    assets.append(existing[0])
                else:
                    # Generate new
                    prompt = self._build_prompt(product, brief)
                    image_bytes = self._image_gen.generate_png(prompt, aspect)

                    asset = Asset(
                        product_id=product.id,
                        aspect=aspect,
                        image_url=f"generated_{product.id}_{aspect}.png",
                        reused=False
                    )

                    self._vector_store.upsert(asset, image_bytes)
                    assets.append(asset)

        return assets
```

**Tests pass!** ✅ Business logic verified without external dependencies

### Step 5: Implement Adapters (Parallel, 4 hours)

**Team A:**
```python
# infrastructure/adapters/openai_image_gen.py
class OpenAIImageGen:
    def generate_png(self, prompt: str, size: str) -> bytes:
        response = openai.images.generate(model="dall-e-3", prompt=prompt, size=size)
        return response.data[0].b64_decode()
```

**Team B:**
```python
# infrastructure/adapters/weaviate_store.py
class WeaviateVectorStore:
    def find_existing(self, product_id, region, aspect):
        results = self._client.query.get("Asset").with_where(...).do()
        return [self._to_asset(r) for r in results]
```

### Step 6: Integration Tests (1 hour)

```python
# tests/integration/test_openai_adapter.py
@pytest.mark.integration
async def test_openai_generates_real_image():
    """Integration test with real OpenAI API."""
    adapter = OpenAIImageGen(api_key=os.getenv("OPENAI_API_KEY"))

    image_bytes = adapter.generate_png("A sunset over mountains", "1024x1024")

    assert len(image_bytes) > 0
    assert image_bytes.startswith(b'\x89PNG')  # Valid PNG header
```

### Total Time: ~8 hours
- 1 hour: Stakeholder workshop → tests as specs
- 1 hour: Interfaces
- 2 hours: Use case tests → business rules documented
- 2 hours: Use case implementation → tests pass
- 4 hours: Adapter implementation (parallel)
- 1 hour: Integration tests

**vs Traditional:** ~12+ hours with multiple misunderstandings and rewrites

---

## Conclusion: TDD as Accelerator

### Why You're Right

1. **Controllers enable specification-first**: Tests define "what" before "how"
2. **Stakeholders can participate**: Given/When/Then is accessible
3. **Expensive work delayed**: Get agreement before building adapters
4. **Parallel development**: Teams work on interfaces, not blocked
5. **Enterprise velocity**: Less rework = faster delivery

### Updated Methodology Recommendation

**For Enterprise PoCs (multi-stakeholder, complex domain):**
```yaml
base_structure: full_ca  # Controllers + rich use cases
tdd_approach: specification_first
workflow:
  P1: "Write test scenarios with stakeholders"
  P3: "Write controller/use case tests (red)"
  P5: "Implement use cases with fakes (green)"
  P6: "Implement adapters (green)"
```

**For Rapid Prototyping (solo, exploratory):**
```yaml
base_structure: progressive_ca
tdd_approach: optional
workflow:
  P3: "Build quickly, maybe add tests"
  P6: "Add tests if productionizing"
```

### Key Decision Point

**Ask:** "Do we need stakeholder sign-off on behavior before implementation?"
- **YES** → Use controllers + TDD + Outside-In workflow
- **NO** → Use progressive CA + implement directly

---

**Next Actions:**
1. Add TDD workflow to Phase 1 (Decomposition)
2. Create "Stakeholder Workshop" template with test skeleton
3. Add TDD decision criteria to REVIEW_GUIDE
4. Update CLAUDE.md with TDD as acceleration option

**Status:** Ready to integrate into methodology
**Impact:** CRITICAL - Changes enterprise PoC approach fundamentally
