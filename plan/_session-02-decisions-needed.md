# Session 02: Framework Decisions Needed

**Status:** ✅ **ALL 12 DECISIONS RESOLVED**

This document captures the key architectural decisions for Framework folder structures.

**Update:** After reviewing CLEAN_ARCHITECTURE_ANALYSIS.md, identified 5 additional critical decisions about drivers, gateways, DTOs, and ports that were missing from initial framework definition.

---

## Decision 1: "Controller" vs "Orchestrator" Terminology ⚠️ CRITICAL

**What I did:**
- Used "Controller" throughout the framework document
- Based on Session 01 Q4 which said "Call it 'Feature' at test level, map to 'Controller' at implementation level"

**Your concern:**
- The methodology explicitly calls for "Orchestrator" not "Controller"

**Question for you:**
Which term should we use in the Framework?

- [ ] **Option A: "Orchestrator"** (methodology term, more descriptive)
  - Folder: `app/interface_adapters/orchestrators/`
  - Class: `LocalizedCampaignOrchestrator`
  - Pro: Consistent with methodology, clearer intent
  - Con: Session 01 Q4 said "Controller"

- [ ] **Option B: "Controller"** (Clean Architecture term, industry standard)
  - Folder: `app/interface_adapters/controllers/`
  - Class: `LocalizedCampaignController`
  - Pro: Standard CA terminology, familiar to most developers
  - Con: Conflicts with methodology

- [ ] **Option C: Use both** (map terminology per context)
  - Methodology docs use "Orchestrator"
  - Code examples use "Controller"
  - Pro: Meets both needs
  - Con: Could be confusing

**Your decision:** ✅ **Option A: "Orchestrator"**

**Rationale:**
- "Controller" is overloaded in web dev (FastAPI request handlers)
- Stakeholders will see these names in tests during workshops
- Methodology must align terminology across docs, code, and tests
- This is NOT pure Clean Architecture—we're creating a hybrid methodology
- Consistency enables evolution (Steel Thread → Pragmatic CA → Full CA)

**Impact:** HIGH - Affects folder names, class names, documentation throughout

**Status:** ✅ RESOLVED - See `/docs/lean-clean-axioms.md` Axiom 2

---

## Decision 2: Folder Structure Depth

**What I did:**
- Created moderately nested structure (3-4 levels deep)
- Example: `app/core/use_cases/generate_campaign.py`

**Question for you:**
How deep should the folder structure go?

- [ ] **Option A: Flat** (2 levels max)
  ```
  app/
  ├─ entities/
  ├─ use_cases/
  ├─ adapters/
  └─ orchestrators/
  ```
  - Pro: Simple, easy to navigate
  - Con: Doesn't show layer relationships clearly

- [ ] **Option B: Moderate** (3-4 levels - what I did)
  ```
  app/
  ├─ core/
  │  ├─ entities/
  │  ├─ use_cases/
  │  └─ interfaces/
  ├─ adapters/
  │  ├─ imagegen/
  │  └─ storage/
  └─ interface_adapters/
     ├─ orchestrators/
     └─ presenters/
  ```
  - Pro: Clear layer boundaries (core vs adapters)
  - Con: More nesting

- [ ] **Option C: Deep** (4-5 levels)
  ```
  app/
  ├─ core/
  │  ├─ domain/
  │  │  ├─ entities/
  │  │  └─ value_objects/
  │  ├─ application/
  │  │  ├─ use_cases/
  │  │  └─ interfaces/
  ```
  - Pro: Maximum organization
  - Con: Deep nesting, violates "don't create overly abstract structures" anti-pattern

**Your decision:** ✅ **All three** (progressive refinement)

**Rationale:**
- This isn't a choice—it's the evolution path
- Steel Thread uses flat (Option A)
- Pragmatic CA uses moderate (Option B)
- Full CA uses deep (Option C)
- Structure depth scales with PoC complexity
- Maintains consistency: same components at each level, just more organization

**Impact:** MEDIUM - Affects import paths, navigation

**Status:** ✅ RESOLVED - Framework document already implements progressive depth

---

## Decision 3: Scope of Session 02 Document

**What I did:**
- Defined all three PoC types (Steel Thread, Pragmatic CA, Full CA) in one doc
- Created complete example with 6 code files
- Total: 1,666 lines

**Question for you:**
Should we have this much in one document?

- [ ] **Option A: Keep comprehensive** (what I did)
  - All three PoC types in one doc
  - Complete feature example
  - Pro: Complete reference, shows evolution clearly
  - Con: Long (1,666 lines), potentially overwhelming

- [ ] **Option B: Split into multiple docs**
  - `_session-02-pragmatic-ca.md` (start here)
  - `_session-02-steel-thread.md`
  - `_session-02-full-ca.md`
  - `_session-02-evolution.md`
  - Pro: Focused, easier to digest
  - Con: Relationships less obvious

- [ ] **Option C: Pragmatic CA only, iterate later**
  - Focus Session 02 on Pragmatic CA only
  - Add Steel Thread/Full CA in future sessions
  - Pro: Incremental, get feedback faster
  - Con: Missing the full picture

**Your decision:** ✅ **Option A: Keep comprehensive**

**Rationale:**
- Evolution path is the key insight—splitting would obscure it
- Side-by-side comparison shows progressive refinement clearly
- Complete reference enables informed PoC type selection
- Single source of truth for Framework (Component B)

**Impact:** LOW - Organizational preference

**Status:** ✅ RESOLVED - Keep all three PoC types in one document

---

## Decision 4: Example Feature Scenario

**What I did:**
- Used "Localized Campaign Generation" as the running example
- Multi-stakeholder (Creative Lead, Ad Ops, IT, Legal)
- Social media campaign with AI-generated images

**Question for you:**
Is this the right example scenario?

- [ ] **Option A: Keep Localized Campaign** (what I did)
  - Pro: Matches Session 0 context, complex enough to be realistic
  - Con: Specific to marketing domain

- [ ] **Option B: Different scenario**
  - Suggest alternative: [YOUR INPUT]
  - Pro: Better fit for methodology
  - Con: Need to rewrite examples

- [ ] **Option C: Generic/abstract example**
  - "Process Document Request" or similar
  - Pro: Domain-agnostic
  - Con: Less engaging, harder to visualize

**Your decision:** ✅ **Option A: Keep Localized Campaign**

**Rationale:**
- Matches Session 0 context (AI/Gen-AI enterprise PoCs)
- Demonstrates multi-stakeholder requirements clearly (Creative, Ad Ops, IT, Legal)
- Complex enough to show orchestration, use cases, adapters working together
- Realistic: actual enterprise use case, not toy example
- AI-generated images show adapter pattern clearly (fake vs OpenAI vs Firefly)

**Impact:** MEDIUM - Affects code examples throughout

**Status:** ✅ RESOLVED - Use Localized Campaign Generation throughout

---

## Decision 5: Code Detail Level

**What I did:**
- Provided detailed Python code examples with:
  - Full class definitions
  - Docstrings
  - Type hints
  - Async/await patterns
  - Error handling

**Question for you:**
How detailed should the code examples be?

- [ ] **Option A: Pseudocode/outline**
  ```python
  class CampaignController:
      def generate(request):
          # 1. Validate
          # 2. Generate
          # 3. Format
          pass
  ```
  - Pro: Focus on structure, not implementation
  - Con: Less concrete, harder to understand

- [ ] **Option B: Detailed but incomplete** (what I did)
  ```python
  class CampaignController:
      async def generate(self, request: Request) -> Response:
          """Orchestrate campaign generation."""
          # Step 1: Validate
          self.validate_uc.execute(request)
          # Step 2: Generate
          output = await self.generate_uc.execute(...)
          # Step 3: Format
          return self.presenter.to_response(output)
  ```
  - Pro: Shows pattern clearly, realistic
  - Con: Not runnable code

- [ ] **Option C: Full runnable code**
  - Complete implementations with tests
  - Pro: Can copy-paste and run
  - Con: Very long, distracts from architecture

**Your decision:** ✅ **Option B: Detailed but incomplete**

**Rationale:**
- Balances clarity with brevity (shows pattern without overwhelming detail)
- Realistic: developers see actual Python syntax, type hints, async patterns
- Educational: focuses on architecture decisions, not implementation minutiae
- Framework document is about structure—full implementations come in Session 03 (Code Paradigms)
- Developers can extrapolate from detailed examples to their own implementations

**Impact:** MEDIUM - Affects how developers will use the framework

**Status:** ✅ RESOLVED - Use detailed but incomplete code examples

---

## Decision 6: Interface Definition Style

**What I did:**
- Used Python `Protocol` (structural subtyping) instead of `ABC` (abstract base class)

**Question for you:**
How should we define interfaces?

- [ ] **Option A: Protocol** (what I did)
  ```python
  from typing import Protocol

  class IImageGenerator(Protocol):
      async def generate(self, prompt: str) -> bytes: ...
  ```
  - Pro: More Pythonic, no inheritance needed
  - Con: Less explicit, newer Python feature

- [ ] **Option B: ABC**
  ```python
  from abc import ABC, abstractmethod

  class IImageGenerator(ABC):
      @abstractmethod
      async def generate(self, prompt: str) -> bytes:
          pass
  ```
  - Pro: Explicit, enforced at runtime
  - Con: Requires inheritance

- [ ] **Option C: Both** (show both patterns)
  - Pro: Developers choose
  - Con: Inconsistent

**Your decision:** ✅ **Option A: Protocol**

**Rationale:**
- More Pythonic (structural subtyping, duck typing)
- No inheritance required—adapters implement interface implicitly
- Cleaner for fakes—`FakeImageGenerator` doesn't need `IImageGenerator` base class
- Modern Python best practice (Protocol introduced in Python 3.8+)
- Aligns with Dependency Inversion Principle without coupling through inheritance
- Consistency: use one pattern throughout methodology

**Impact:** LOW - Pattern preference

**Status:** ✅ RESOLVED - Use Protocol for interface definitions

---

## Decision 7: Test Structure Organization

**What I did:**
- Three test layers: `acceptance/`, `unit/`, `integration/`
- Feature tests in `acceptance/features/`
- Unit tests mirror app structure (`unit/use_cases/`, `unit/orchestrators/`)

**Question for you:**
Is this test structure correct?

- [ ] **Option A: Keep as-is** (what I did)
  ```
  tests/
  ├─ acceptance/features/
  ├─ unit/use_cases/
  ├─ unit/orchestrators/
  └─ integration/adapters/
  ```

- [ ] **Option B: Flatten**
  ```
  tests/
  ├─ test_features/
  ├─ test_use_cases/
  └─ test_adapters/
  ```

- [ ] **Option C: Different organization**
  - Your suggestion: [YOUR INPUT]

**Your decision:** ✅ **Option A: Keep as-is**

**Rationale:**
- Three test layers align with Outside-In TDD workflow:
  1. **Acceptance**: Stakeholder contracts with fakes (fast, always run)
  2. **Unit**: Isolated component tests (fast, always run)
  3. **Integration**: Real adapter tests (slow, nightly/pre-deploy)
- Test structure mirrors app structure—easy to find tests for components
- `acceptance/features/` uses stakeholder language ("feature")
- `unit/orchestrators/` uses implementation language ("orchestrator")
- Consistent across all three PoC types (same philosophy, different depth)

**Impact:** MEDIUM - Affects test organization

**Status:** ✅ RESOLVED - Use acceptance/unit/integration test layers

---

## Decision 8: Fakes Location

**What I did:**
- Put fakes in `app/adapters/imagegen/fake_imagegen.py` (production code)
- Not in `tests/` (test code)

**Question for you:**
Is this the right location for fakes?

- [ ] **Option A: In adapters/** (what I did)
  - `app/adapters/imagegen/fake_imagegen.py`
  - Pro: Fakes are first-class implementations, used in multiple contexts
  - Con: Mixes "real" and "fake" in same folder

- [ ] **Option B: In tests/**
  - `tests/fakes/fake_imagegen.py`
  - Pro: Clear separation, test-only code
  - Con: Can't use in CLI for demos, not "production" code

- [ ] **Option C: Separate fakes/ folder**
  - `app/fakes/fake_imagegen.py`
  - Pro: Clear boundary
  - Con: Extra top-level folder

**Your decision:** ✅ **Option A: In `adapters/`** (with important caveat)

**Rationale:**

**Alignment with Axiom 3:**
- Axiom 3 is non-negotiable: "Fakes are production code" in `adapters/`
- Supports workshop workflow: stakeholders demo with fakes before real APIs
- Philosophy: Fakes ARE implementations (not test doubles)

**Progressive Evolution:**
- Steel Thread → Pragmatic CA: Nest into subfolders (minimal import changes)
- Pragmatic CA → Full CA: Rename files within same location
- No layer changes = no rework

**Important Caveat - Two Types of "Fakes":**

1. **Fake Adapters** (External Services) → `app/adapters/`
   - `FakeImageGenerator` - simulates OpenAI API
   - `FakeStorageAdapter` - simulates S3 API
   - `FakeEventTracker` - simulates analytics API
   - Location: `app/adapters/` per Axiom 3

2. **In-Memory Repositories** (Persistence) → `app/infrastructure/repositories/`
   - `InMemoryCalibrationRepository` - simulates database storage
   - `InMemoryCampaignRepository` - simulates database storage
   - Location: `app/infrastructure/repositories/` per calibration-service pattern
   - See Decision 10 for full infrastructure layer organization

**Key Distinction:**
- Adapters = external service integrations (OpenAI, S3, Amplitude)
- Repositories = persistence layer (Postgres, MongoDB, In-Memory)
- Both are "fakes" conceptually, but live in different layers

**Connection to Decision 10:**
This decision primarily addresses fake **adapters** for external services. Decision 10 will determine the full infrastructure layer structure including where in-memory repositories live.

**Impact:** MEDIUM - Philosophical question about fakes, affects test imports and mental model

---

## Decision 9: Drivers Layer - Does It Exist?

**What I did:**
- No `drivers/` folder - Framework entry points (`server.py`, `cli.py`) at project root
- FastAPI routers would be inline or in top-level `routers/`

**Context from CLEAN_ARCHITECTURE_ANALYSIS:**
- **Calibration-service**: Has `drivers/rest/` with routers, schemas, dependencies, exception handlers
- **Lean-clean**: Has `drivers/rest/controllers/` with thin mappers
- **nikolovlazar diagram**: Shows "Frameworks & Drivers" as outermost layer

**Critical Enterprise PoC Reality:**
From workshop context: Tests and code written WITH stakeholders in 90-minute workshops. Stakeholders need:
- **Visual feedback** (Streamlit UI) - Creative Lead sees campaign images
- **Automation** (CLI) - Reproducible runs, CI/CD integration
- **Integration** (FastAPI) - When connecting to enterprise systems

**Question for you:**
Should we have an explicit `drivers/` layer?

- [ ] **Option A: No drivers/ folder**
  ```
  project/
  ├─ server.py          # FastAPI entry point
  ├─ cli.py             # Typer CLI entry point
  ├─ app/
  │  ├─ interface_adapters/orchestrators/
  ```
  - Pro: Simpler, fewer folders
  - Con: Entry points mixed with app code, unclear layer boundary
  - ❌ **Rejected:** Even Steel Thread needs CLI + UI = multiple entry points

- [ ] **Option B: drivers/rest/ with routers** (calibration-service pattern)
  ```
  project/
  ├─ app/
  │  ├─ interface_adapters/orchestrators/
  ├─ drivers/
  │  └─ rest/
  │     ├─ routers/           # FastAPI routes
  │     ├─ schemas/           # Pydantic models
  │     ├─ dependencies.py    # DI wiring
  │     └─ exception_handlers.py
  ```
  - Pro: Clear layer boundary, framework concerns isolated
  - Con: More nesting
  - ⚠️ **Incomplete:** Missing CLI and UI drivers

- [ ] **Option C: drivers/ calls use cases directly** (lean-clean pattern)
  ```
  project/
  ├─ drivers/
  │  └─ rest/
  │     └─ controllers/       # Thin mappers, call use cases
  ```
  - Pro: Skip orchestration for simple PoCs
  - Con: Violates CA (should call orchestrators, not use cases)
  - ❌ **Rejected:** Conflicts with Axiom 2 (Orchestrator-Centric)

**Your decision:** ✅ **Progressive Evolution with Enterprise Reality** (new option)

**Rationale:**

**Enterprise PoC Reality:**
- ✅ **CLI ALWAYS** - Automation, reproducibility, CI/CD
- ✅ **UI ALWAYS** - Stakeholder demos (Creative Lead, Ad Ops, Legal)
- ⚠️ **FastAPI WHEN NEEDED** - Enterprise integrations, webhooks, external access

**Progressive Evolution Approach:**

### **Steel Thread (1-2 days): Minimal drivers/ with CLI + UI**

```
project/
├─ app/
│  ├─ entities/
│  │  └─ campaign.py                  # Domain model
│  ├─ use_cases/
│  │  └─ generate_campaign_uc.py      # Business logic
│  └─ adapters/
│     ├─ imagegen/
│     │  ├─ protocol.py               # IImageGenerator interface
│     │  ├─ fake.py                   # Fast fake for workshops
│     │  └─ openai.py                 # Real implementation
│     └─ storage/
│        ├─ protocol.py               # IStorageAdapter interface
│        ├─ fake.py                   # In-memory
│        └─ local.py                  # Filesystem
│
├─ drivers/                            # ← YES, even Steel Thread
│  ├─ cli.py                           # Automation, reproducibility
│  └─ ui/
│     └─ streamlit_app.py              # Stakeholder visual demos
│
└─ .streamlit/
   └─ config.toml                      # Streamlit configuration
```

**Why drivers/ from the start:**
- ✅ CLI + UI = 2 entry points → folder organization warranted
- ✅ Stakeholders need visual feedback (not just CLI logs)
- ✅ Clear mental model: "drivers call use cases"
- ✅ No refactoring when adding FastAPI later

**Example: CLI (Direct DI):**
```python
# drivers/cli.py
import typer
from app.use_cases.generate_campaign_uc import GenerateCampaignUseCase
from app.adapters.imagegen.fake import FakeImageGenerator  # Fast for demos

app = typer.Typer()

@app.command()
def run(brief_path: str):
    uc = GenerateCampaignUseCase(
        image_gen=FakeImageGenerator(),  # Fake for speed
        storage=LocalStorage()
    )
    result = uc.execute(load_brief(brief_path))
    typer.echo(f"✓ Generated {len(result.campaigns)} campaigns")
```

**Example: Streamlit UI (Stakeholder Demo):**
```python
# drivers/ui/streamlit_app.py
import streamlit as st
from app.use_cases.generate_campaign_uc import GenerateCampaignUseCase
from app.adapters.imagegen.fake import FakeImageGenerator

st.title("🎨 Localized Campaign Generator")

# Creative Lead inputs
brand_colors = st.color_picker("Primary Brand Color", "#FF6B35")
tone = st.selectbox("Tone", ["energetic", "premium", "playful"])

if st.button("Generate Summer Sale Campaign"):
    uc = GenerateCampaignUseCase(
        image_gen=FakeImageGenerator(),  # Fast for workshop
        storage=LocalStorage()
    )

    with st.spinner("Generating campaigns..."):
        result = uc.execute(sample_brief)

    # Show to Creative Lead
    for campaign in result.campaigns:
        st.image(campaign.image_url)
        st.caption(f"Market: {campaign.market}")
```

---

### **Pragmatic CA (3-5 days): Organized drivers/ with orchestrators**

```
project/
├─ app/
│  ├─ entities/                        # Domain models
│  ├─ use_cases/                       # Business logic
│  ├─ interface_adapters/
│  │  ├─ orchestrators/
│  │  │  └─ campaign_orchestrator.py  # Coordinates use cases
│  │  └─ presenters/
│  │     └─ campaign_presenter.py     # Format outputs
│  ├─ adapters/                        # External services
│  │  ├─ imagegen/
│  │  ├─ storage/
│  │  └─ events/
│  └─ infrastructure/                  # Persistence (if needed)
│     └─ repositories/
│        └─ campaign/
│
├─ drivers/
│  ├─ cli/
│  │  ├─ __main__.py                   # Entry point: python -m drivers.cli
│  │  ├─ commands.py                   # Typer commands organized
│  │  └─ dependencies.py               # DI container for CLI
│  │
│  ├─ rest/                             # ← Added when integrating with enterprise
│  │  ├─ main.py                       # FastAPI app
│  │  ├─ routers/
│  │  │  └─ campaigns.py               # Campaign endpoints
│  │  ├─ schemas/                      # Pydantic request/response
│  │  │  ├─ campaign_request.py
│  │  │  └─ campaign_response.py
│  │  └─ dependencies.py               # FastAPI DI
│  │
│  └─ ui/
│     └─ streamlit/
│        ├─ app.py                     # Main Streamlit entry
│        ├─ pages/                     # Multi-page app
│        │  ├─ 1_generate.py          # Creative Lead: Generate campaigns
│        │  └─ 2_approve.py           # Legal: Approve content
│        └─ dependencies.py            # Streamlit DI
│
└─ .streamlit/
   └─ config.toml
```

**When to add FastAPI:**
- ✅ Integration with enterprise DAM system
- ✅ External stakeholders need API access
- ✅ Webhook receivers for approval workflows
- ✅ Async job processing

**Example: FastAPI router calling orchestrator:**
```python
# drivers/rest/routers/campaigns.py
from fastapi import APIRouter, Depends
from drivers.rest.schemas.campaign_request import CampaignRequest
from drivers.rest.dependencies import get_orchestrator

router = APIRouter()

@router.post("/campaigns")
async def create_campaign(
    request: CampaignRequest,
    orchestrator = Depends(get_orchestrator)  # Calls orchestrator, not use case
):
    result = await orchestrator.generate(request)
    return {"campaign_id": result.id, "status": "processing"}
```

**Example: Streamlit multi-page for workshops:**
```python
# drivers/ui/streamlit/pages/1_generate.py
import streamlit as st
from drivers.ui.streamlit.dependencies import get_orchestrator

st.title("🎨 Generate Campaigns")

# Creative Lead inputs
brand_colors = st.color_picker("Primary Brand Color")
tone = st.selectbox("Tone", ["energetic", "premium", "playful"])

if st.button("Generate"):
    orchestrator = get_orchestrator()

    # Show progress to stakeholders
    with st.status("Generating campaigns...") as status:
        st.write("🎨 Generating images...")
        st.write("✅ 5 markets created")

        result = orchestrator.generate(request)
        status.update(label="Complete!", state="complete")

    # Creative Lead reviews
    st.subheader("Review Campaigns")
    for campaign in result.campaigns:
        col1, col2 = st.columns(2)
        with col1:
            st.image(campaign.image)
        with col2:
            st.write(f"**Market:** {campaign.market}")
            st.button("✓ Approve", key=campaign.id)  # Legal reviews
```

---

### **Full CA (Production): Complete drivers/ layer**

```
drivers/
├─ cli/
│  ├─ __main__.py                      # Entry point
│  ├─ commands/                        # Organized commands
│  │  ├─ generate.py                   # Campaign generation
│  │  ├─ validate.py                   # Brief validation
│  │  └─ approve.py                    # Approval workflow
│  ├─ formatters/                      # Output formatters
│  │  ├─ table.py                      # Rich table output
│  │  └─ json.py                       # JSON output
│  └─ dependencies.py                  # DI container
│
├─ rest/
│  ├─ main.py                          # FastAPI app
│  ├─ routers/
│  │  ├─ campaigns.py                  # Campaign endpoints
│  │  ├─ approvals.py                  # Approval endpoints
│  │  └─ webhooks.py                   # External webhooks
│  ├─ schemas/                         # Pydantic models
│  ├─ middleware/                      # Cross-cutting concerns
│  │  ├─ auth.py                       # Enterprise SSO
│  │  ├─ logging.py                    # Request logging
│  │  └─ telemetry.py                  # Phoenix/Arize instrumentation
│  ├─ exception_handlers.py            # Error handling
│  └─ dependencies.py                  # FastAPI DI
│
├─ ui/
│  └─ streamlit/
│     ├─ app.py                        # Main entry
│     ├─ pages/                        # Multi-stakeholder pages
│     │  ├─ 1_generate.py             # Creative Lead: Generate
│     │  ├─ 2_approve.py              # Legal: Approve
│     │  ├─ 3_monitor.py              # IT: Dashboard
│     │  └─ 4_compliance.py           # Legal: Compliance review
│     ├─ components/                   # Reusable UI components
│     │  ├─ campaign_card.py
│     │  └─ approval_widget.py
│     └─ dependencies.py               # Streamlit DI
│
└─ graphql/                             # Optional: If enterprise needs GraphQL
   ├─ schema.py
   ├─ resolvers/
   └─ main.py
```

---

### **Migration Path**

**Steel Thread → Pragmatic CA:**

When: Stakeholder feedback needs more structure, or integrating with other systems

Changes:
1. Add orchestrators (`app/interface_adapters/orchestrators/`)
2. Organize CLI into commands (`drivers/cli/commands/`)
3. Add FastAPI if needed (`drivers/rest/`)
4. Expand Streamlit to multi-page (`drivers/ui/streamlit/pages/`)
5. Add DI containers (`dependencies.py` in each driver)

Migration Steps:
```bash
# 1. Create orchestrator layer
mkdir -p app/interface_adapters/orchestrators

# 2. Organize CLI
mkdir drivers/cli/commands
mv drivers/cli.py drivers/cli/__main__.py

# 3. Add FastAPI (if needed)
mkdir -p drivers/rest/{routers,schemas}

# 4. Expand Streamlit
mkdir drivers/ui/streamlit/pages
mv drivers/ui/streamlit_app.py drivers/ui/streamlit/app.py
```

**No file moves between layers** - just organizational nesting.

---

**Pragmatic CA → Full CA:**

When: Production deployment, enterprise security requirements, complex workflows

Changes:
1. Add middleware (`drivers/rest/middleware/`)
2. Add exception handlers (`drivers/rest/exception_handlers.py`)
3. Expand CLI with formatters (`drivers/cli/formatters/`)
4. Add Streamlit components (`drivers/ui/streamlit/components/`)
5. Add additional driver types (GraphQL) if needed

Migration Steps:
```bash
# 1. Add middleware
mkdir drivers/rest/middleware

# 2. Add CLI formatters
mkdir drivers/cli/formatters

# 3. Add Streamlit components
mkdir drivers/ui/streamlit/components

# 4. Add additional drivers (if needed)
mkdir drivers/graphql
```

---

### **Dependency Injection Strategy**

**Steel Thread: Direct Instantiation**
```python
# drivers/cli.py
uc = GenerateCampaignUseCase(
    image_gen=FakeImageGenerator(),
    storage=LocalStorage()
)
```

**Pragmatic CA: Dependencies Module**
```python
# drivers/cli/dependencies.py
def build_orchestrator() -> CampaignOrchestrator:
    config = load_config()

    # Adapters (fake or real based on config)
    image_gen = (
        FakeImageGenerator() if config.use_fakes
        else OpenAIImageGenerator()
    )

    # Use Cases
    generate_uc = GenerateCampaignUseCase(image_gen=image_gen)

    # Orchestrator
    return CampaignOrchestrator(generate_uc=generate_uc)

# drivers/cli/commands.py
@app.command()
def run(brief: str):
    orchestrator = build_orchestrator()
    result = orchestrator.generate(...)
```

**Full CA: Dependency Injection Container**
```python
# drivers/rest/dependencies.py
from dependency_injector import containers, providers

class Container(containers.DeclarativeContainer):
    config = providers.Configuration()

    # Adapters
    image_gen = providers.Singleton(
        OpenAIImageGenerator,
        api_key=config.openai_api_key
    )

    # Use Cases
    generate_uc = providers.Factory(
        GenerateCampaignUseCase,
        image_gen=image_gen
    )

    # Orchestrators
    campaign_orchestrator = providers.Factory(
        CampaignOrchestrator,
        generate_uc=generate_uc
    )
```

---

**Why This Works:**

✅ **Progressive Evolution:**
- Steel Thread: `drivers/` with CLI + UI (flat)
- Pragmatic CA: `drivers/cli/`, `drivers/rest/`, `drivers/ui/` (organized)
- Full CA: Complete drivers with middleware, components (production-ready)

✅ **Stakeholder Workshop Clarity:**
- CLI for reproducibility ("run this command to regenerate")
- Streamlit UI for visual feedback (Creative Lead sees images)
- FastAPI when needed (integration with enterprise systems)

✅ **Consistency with Axioms:**
- Axiom 2 (Orchestrator-Centric): All drivers call orchestrators from Pragmatic CA+
- Drivers are outermost layer (matches Clean Architecture)

✅ **Pragmatic vs. Dogmatic:**
- Recognizes enterprise reality: PoCs need UI from day 1
- FastAPI optional until needed (not every PoC needs REST API)
- Streamlit chosen for speed (not React/Vue complexity)

**Impact:** HIGH - Affects project structure, entry point organization, DI strategy, AND aligns with multi-stakeholder workshop reality

**Status:** ✅ RESOLVED - Progressive evolution with drivers/ from Steel Thread (CLI + UI always), FastAPI added in Pragmatic CA when integrating with enterprise systems

---

## Decision 10: Gateways vs Repositories/Services Terminology

**What I did:**
- Used `adapters/` as generic term for all external integrations
- `adapters/imagegen/`, `adapters/storage/`, `adapters/events/`

**Context from CLEAN_ARCHITECTURE_ANALYSIS:**
- **Calibration-service**: Distinguishes `repositories/` (persistence) from `services/` (external APIs)
- **Lean-clean**: Uses `gateways/` for all vendor integrations
- **Uncle Bob CA**: Uses "Gateways" in original diagram

**Question for you:**
What terminology should we use for external integrations?

- [ ] **Option A: adapters/** (what I did - generic)
  ```
  app/adapters/
  ├─ imagegen/
  ├─ storage/
  └─ events/
  ```
  - Pro: Generic, covers all cases
  - Con: Doesn't distinguish persistence from external services

- [ ] **Option B: gateways/** (lean-clean, matches CA diagram)
  ```
  app/interface_adapters/gateways/
  ├─ openai_gateway.py
  ├─ s3_gateway.py
  └─ amplitude_gateway.py
  ```
  - Pro: Matches Uncle Bob's terminology, clear vendor integrations
  - Con: Doesn't distinguish repositories

- [ ] **Option C: repositories/ + services/** (calibration-service)
  ```
  app/adapters/
  ├─ repositories/        # Persistence (DB)
  │  └─ campaign_repo.py
  └─ services/            # External APIs
     ├─ openai_service.py
     └─ s3_service.py
  ```
  - Pro: Clear distinction between persistence and external services
  - Con: More folders

- [ ] **Option D: Infrastructure layer separate** (synthesis proposal)
  ```
  app/
  ├─ interface_adapters/gateways/  # External service clients
  └─ infrastructure/               # ORM, framework-specific
     ├─ orm_models/
     └─ repositories/
  ```
  - Pro: Separates framework concerns from business adapters
  - Con: Most complex

**Your decision:** ✅ **Option B: Split `adapters/` + `infrastructure/repositories/`** (with evolution refinement pending)

**Rationale:**

**Current Recommendation - Complete Layer Structure:**

```
app/
├─ entities/                         # Domain models, value objects
│
├─ application/                      # Business logic + Ports (interfaces)
│  ├─ use_cases/
│  ├─ ports/                         # ALL INTERFACES (connects to Decision 12)
│  │  ├─ repositories/               # Persistence contracts
│  │  │  ├─ campaign_repository.py  # ICampaignRepository protocol
│  │  │  └─ creative_repository.py  # ICreativeRepository protocol
│  │  └─ services/                   # External service contracts
│  │     ├─ image_generator.py      # IImageGenerator protocol
│  │     ├─ storage_adapter.py      # IStorageAdapter protocol
│  │     ├─ crm_client.py           # ICrmClient protocol (internal service)
│  │     └─ analytics_client.py     # IAnalyticsClient protocol (third-party)
│  └─ dtos/                          # (Decision 11)
│
├─ interface_adapters/               # Boundary translation
│  ├─ orchestrators/                 # Coordinate use cases
│  └─ presenters/                    # Format outputs + telemetry
│
├─ adapters/                         # External service IMPLEMENTATIONS
│  ├─ imagegen/                      # Implements ports/services/image_generator
│  │  ├─ fake_imagegen.py           # Fake (Decision 8)
│  │  ├─ openai_imagegen.py         # Third-party
│  │  └─ firefly_imagegen.py        # Third-party
│  ├─ crm/                           # Implements ports/services/crm_client
│  │  ├─ fake_salesforce.py
│  │  └─ salesforce_client.py       # Internal enterprise service
│  └─ analytics/
│     ├─ fake_warehouse.py
│     └─ snowflake_client.py        # Internal data warehouse
│
└─ infrastructure/                   # Persistence IMPLEMENTATIONS
   ├─ orm_models/                    # SQLAlchemy models (separate from domain)
   └─ repositories/                  # Implements ports/repositories/*
      ├─ campaign/
      │  ├─ in_memory_repository.py # In-memory fake (Decision 8)
      │  ├─ postgres_repository.py  # Relational
      │  └─ mongodb_repository.py   # Document
      └─ creative/
         ├─ in_memory_repository.py
         └─ postgres_repository.py
```

**Why This Structure:**

1. **Enterprise Complexity**: Multi-agent RAG system with 5+ data stores, multiple internal/third-party services
2. **Clear Architectural Distinction**: External services (adapters/) vs persistence (infrastructure/)
3. **Proven Pattern**: Matches calibration-service battle-tested structure
4. **Scales Progressively**: Steel Thread starts simple, adds layers as needed
5. **No Internal vs Third-Party Distinction**: Both are external to app, implement same ports
6. **In-Memory Repos with Real Repos**: Consistent location in infrastructure/repositories/
7. **Dependency Flow**: Use cases → ports (interfaces) → implementations (adapters/infrastructure)

**Connecting Decisions:**
- **Decision 8**: Fake adapters in adapters/, in-memory repos in infrastructure/repositories/
- **Decision 12**: Ports live in application/ports/ split into repositories/ and services/

**Refined Decision: Two-Step Evolution Approach**

**Steel Thread (1-2 days): Co-Located, Minimal**

```
app/
├─ entities/
│  └─ campaign.py                    # Domain model
├─ use_cases/
│  └─ generate_campaign_uc.py        # Business logic
└─ adapters/
   ├─ imagegen/
   │  ├─ protocol.py                 # ← IImageGenerator CO-LOCATED
   │  ├─ fake.py
   │  └─ openai.py
   └─ storage/
      ├─ protocol.py                 # ← IStorageAdapter CO-LOCATED
      ├─ fake.py
      └─ s3.py
```

**Pragmatic CA (3-5 days): Co-Located, Organized**

```
app/
├─ entities/
│  ├─ campaign.py
│  └─ creative.py
├─ use_cases/
│  ├─ generate_campaign_uc.py
│  └─ validate_brand_uc.py
├─ interface_adapters/
│  ├─ orchestrators/
│  │  └─ campaign_orchestrator.py
│  └─ presenters/
│     └─ campaign_presenter.py
├─ adapters/                         # External services
│  ├─ imagegen/
│  │  ├─ protocol.py                 # ← Still co-located
│  │  ├─ fake.py
│  │  ├─ openai.py
│  │  └─ firefly.py
│  ├─ storage/
│  │  ├─ protocol.py
│  │  ├─ fake.py
│  │  └─ s3.py
│  └─ events/
│     ├─ protocol.py
│     ├─ fake.py
│     └─ amplitude.py
└─ infrastructure/                   # Persistence (added when needed)
   └─ repositories/
      └─ campaign/
         ├─ protocol.py              # ← ICampaignRepository CO-LOCATED
         ├─ in_memory.py
         └─ postgres.py
```

**Full CA (Production): Ports extracted to application/ layer**
- `application/ports/services/image_generator.py` ← All service contracts
- `application/ports/repositories/campaign_repository.py` ← All repository contracts
- **Rationale:** Complexity justifies clean boundaries, enterprise aggregates need clear contracts

**Migration Path (Pragmatic CA → Full CA):**
1. Create `application/ports/` structure
2. Move all `protocol.py` files to `application/ports/services/` or `application/ports/repositories/`
3. Update imports (automated find-replace)
4. Move `use_cases/` under `application/`
5. Tests catch broken imports

**Trade-off Accepted:**
- ✅ Early-stage simplicity (80% of PoCs benefit)
- ❌ One-time migration cost (but scripted and straightforward)
- ✅ Clean architecture at scale (20% that reach Full CA)

**Impact:** HIGH - Affects all external integration organization, persistence layer structure, port definitions, AND Decision 12 (ports location varies by PoC type)

---

## Decision 11: DTO Layer Strategy

**What I did:**
- Minimal DTOs: Request/Response at orchestrator level
- Use cases receive domain commands, return domain outputs
- Example: `LocalizedCampaignRequest` → `GenerateCampaignCommand`

**Context from CLEAN_ARCHITECTURE_ANALYSIS:**
- **Calibration-service**: Separate Input/Output DTOs per use case (`AddCalibrationInput`, `AddCalibrationOutput`)
- **Lean-clean**: Uses domain Commands/Results at boundaries
- **CA principle**: DTOs at boundaries prevent domain leakage

**Question for you:**
How comprehensive should the DTO layer be?

- [ ] **Option A: Minimal DTOs** (what I did)
  ```python
  # Orchestrator level
  @dataclass
  class LocalizedCampaignRequest:  # From API
      global_message: str
      markets: list[str]

  # Use case level
  @dataclass
  class GenerateCampaignCommand:   # Domain object
      message: str
      markets: list[str]
  ```
  - Pro: Less boilerplate, faster development
  - Con: Domain objects leak to boundaries

- [ ] **Option B: Comprehensive DTOs** (calibration-service)
  ```python
  # Use case folder: use_cases/generate_campaign/dtos.py
  @dataclass
  class GenerateCampaignInput:     # Use case input DTO
      message: str
      markets: list[str]

  @dataclass
  class GenerateCampaignOutput:    # Use case output DTO
      campaign_id: str
      creative_sets: dict
  ```
  - Pro: Clear boundaries, prevents domain leakage
  - Con: More conversion logic

- [ ] **Option C: Commands/Results as domain objects** (lean-clean)
  ```python
  # Domain commands used directly
  SendMessageCmd = tuple[Messages, LlmParams]
  LlmResult = tuple[str, Usage]
  ```
  - Pro: Minimal, leverages type system
  - Con: Less explicit, harder to extend

**Your decision:** ✅ **Option A: Minimal DTOs with Progressive Evolution**

**Rationale:**

**Progressive DTO Evolution Pattern:**

### **Steel Thread (1-2 days): No DTOs**

```
app/
├─ entities/
│  └─ campaign.py                  # Domain models
├─ use_cases/
│  └─ generate_campaign_uc.py      # ← Accepts/returns domain objects
└─ adapters/
   └─ imagegen/
      ├─ protocol.py               # Uses domain objects
      ├─ fake.py
      └─ openai.py
```

**Code example:**
```python
# drivers/cli.py - No DTOs
from app.entities.campaign import Campaign

@app.command()
def run(brief_path: str):
    uc = GenerateCampaignUseCase(...)
    campaigns: list[Campaign] = uc.execute(brief_data)  # ← Domain object
    typer.echo(f"✓ Generated {len(campaigns)} campaigns")
```

**Key:** Zero DTO files - domain objects cross all boundaries.

---

### **Pragmatic CA (3-5 days): DTOs at Driver Boundaries Only**

```
app/
├─ entities/                        # Domain models
│  ├─ campaign.py
│  └─ brand_guidelines.py
├─ use_cases/                       # ← Still use domain objects
│  ├─ generate_campaign_uc.py      # Accepts CampaignBrief (domain)
│  └─ validate_brand_uc.py
├─ interface_adapters/
│  ├─ orchestrators/
│  │  └─ campaign_orchestrator.py  # ← Converts driver DTOs → domain
│  └─ presenters/
│     └─ campaign_presenter.py
├─ adapters/
└─ infrastructure/

drivers/
├─ cli/
│  ├─ __main__.py                   # ← Uses domain objects directly
│  ├─ commands.py
│  └─ dependencies.py
├─ rest/                             # When integrating with enterprise
│  ├─ main.py
│  ├─ routers/
│  │  └─ campaigns.py               # ← Uses schemas (DTOs)
│  ├─ schemas/                      # ← DTOs HERE (Pydantic)
│  │  ├─ campaign_request.py       # CampaignRequest(BaseModel)
│  │  └─ campaign_response.py      # CampaignResponse(BaseModel)
│  └─ dependencies.py
└─ ui/
   └─ streamlit/
      ├─ app.py                     # ← Uses domain objects directly
      └─ dependencies.py
```

**DTO Locations:**
- ✅ `drivers/rest/schemas/` - FastAPI Pydantic DTOs (API boundary)
- ❌ No use case DTOs - use cases accept/return domain objects
- ✅ Orchestrators convert: Pydantic DTO → domain object → use case

**Code example:**
```python
# drivers/rest/schemas/campaign_request.py (API DTO)
class CampaignRequest(BaseModel):  # ← Pydantic DTO for validation
    global_message: str
    markets: list[str]
    brand_colors: list[str]

# drivers/rest/routers/campaigns.py
@router.post("/campaigns")
async def create_campaign(
    request: CampaignRequest,  # ← DTO at API boundary
    orchestrator = Depends(get_orchestrator)
):
    result = orchestrator.generate(request)  # ← Orchestrator handles conversion
    return {"campaign_id": result.id}

# app/interface_adapters/orchestrators/campaign_orchestrator.py
class CampaignOrchestrator:
    async def generate(self, request: CampaignRequest) -> dict:
        # Convert DTO → domain object
        brief = CampaignBrief(
            message=request.global_message,
            markets=request.markets,
            brand=BrandGuidelines(colors=request.brand_colors)
        )

        # Call use case with domain object
        campaigns = await self.generate_uc.execute(brief)  # ← Domain object

        return {"campaigns": campaigns}

# app/use_cases/generate_campaign_uc.py
class GenerateCampaignUseCase:
    async def execute(self, brief: CampaignBrief) -> list[Campaign]:  # ← Domain objects
        # Business logic
        return campaigns
```

---

### **Full CA (Production): Use Case DTOs Added**

```
app/
├─ entities/                        # Domain models
├─ application/
│  ├─ use_cases/
│  │  ├─ generate_campaign/
│  │  │  ├─ use_case.py           # ← Uses DTOs now
│  │  │  └─ dtos.py               # ← Use case DTOs
│  │  │     # GenerateCampaignInput
│  │  │     # GenerateCampaignOutput
│  │  └─ validate_brand/
│  │     ├─ use_case.py
│  │     └─ dtos.py
│  └─ ports/                       # Ports extracted (Decision 10)
│     ├─ services/
│     └─ repositories/
├─ interface_adapters/
│  ├─ orchestrators/
│  │  └─ campaign_orchestrator.py  # ← Converts driver DTOs → use case DTOs
│  └─ presenters/
├─ adapters/
└─ infrastructure/

drivers/
├─ cli/
├─ rest/
│  ├─ routers/
│  ├─ schemas/                      # ← API DTOs (Pydantic)
│  │  ├─ campaign_request.py
│  │  └─ campaign_response.py
│  └─ dependencies.py
└─ ui/
```

**DTO Locations:**
- ✅ `drivers/rest/schemas/` - API DTOs (Pydantic)
- ✅ `app/application/use_cases/[name]/dtos.py` - Use case DTOs (dataclasses)
- ✅ Orchestrators convert: API DTO → use case DTO → domain objects

**Code example:**
```python
# drivers/rest/schemas/campaign_request.py (API DTO)
class CampaignRequest(BaseModel):  # ← Pydantic for FastAPI
    global_message: str
    markets: list[str]

# app/application/use_cases/generate_campaign/dtos.py (Use Case DTO)
@dataclass
class GenerateCampaignInput:  # ← Use case input DTO
    message: str
    markets: list[str]
    brand_colors: list[str]

@dataclass
class GenerateCampaignOutput:  # ← Use case output DTO
    campaign_id: str
    campaigns: list[dict]
    metadata: dict

# drivers/rest/routers/campaigns.py
@router.post("/campaigns")
async def create_campaign(
    request: CampaignRequest,  # ← API DTO
    orchestrator = Depends(get_orchestrator)
):
    result = orchestrator.generate(request)
    return result

# app/interface_adapters/orchestrators/campaign_orchestrator.py
class CampaignOrchestrator:
    async def generate(self, api_request: CampaignRequest) -> dict:
        # Convert API DTO → Use Case DTO
        uc_input = GenerateCampaignInput(
            message=api_request.global_message,
            markets=api_request.markets,
            brand_colors=["#FF6B35"]
        )

        # Call use case with Use Case DTO
        uc_output = await self.generate_uc.execute(uc_input)  # ← Use case DTO

        # Convert Use Case DTO → API response
        return {
            "campaign_id": uc_output.campaign_id,
            "campaigns": uc_output.campaigns
        }

# app/application/use_cases/generate_campaign/use_case.py
class GenerateCampaignUseCase:
    async def execute(self, input: GenerateCampaignInput) -> GenerateCampaignOutput:
        # Convert Use Case DTO → domain objects
        brief = CampaignBrief(message=input.message, markets=input.markets)

        # Business logic with domain objects
        campaigns = await self.image_gen.generate(brief)

        # Convert domain objects → Use Case DTO
        return GenerateCampaignOutput(
            campaign_id=generate_id(),
            campaigns=[c.to_dict() for c in campaigns],
            metadata={}
        )
```

---

### **Migration Path**

**Steel Thread → Pragmatic CA (Add API DTOs):**

When: Adding FastAPI for enterprise integration

```bash
# Create API DTOs folder
mkdir -p drivers/rest/schemas

# Create Pydantic schemas
# drivers/rest/schemas/campaign_request.py
# drivers/rest/schemas/campaign_response.py
```

**Pragmatic CA → Full CA (Add Use Case DTOs):**

When: Multiple drivers need same use case, or domain evolving rapidly

```bash
# 1. Reorganize use cases
mkdir -p app/application/use_cases/generate_campaign
mv app/use_cases/generate_campaign_uc.py app/application/use_cases/generate_campaign/use_case.py

# 2. Create use case DTOs
# app/application/use_cases/generate_campaign/dtos.py

# 3. Update orchestrators to convert API DTO → Use Case DTO

# 4. Update use cases to accept Input DTO, return Output DTO
```

---

### **Why This Works**

✅ **Progressive Evolution:**
- Steel Thread: Zero DTOs (domain objects everywhere)
- Pragmatic CA: Minimal DTOs (driver boundaries only - `drivers/rest/schemas/`)
- Full CA: Comprehensive DTOs (use case boundaries - `app/application/use_cases/[name]/dtos.py`)
- Clear migration path, no rewrites

✅ **Stakeholder Workshop Clarity:**
- Domain objects richer than DTOs for test readability
- Business language in domain models (`CampaignBrief`, `BrandGuidelines`)
- Tests use domain objects directly (more expressive)

✅ **Consistency with Decisions 9 & 10:**
- **Decision 9:** `drivers/rest/schemas/` already established for Pydantic DTOs
- **Decision 10:** Ports follow two-step evolution (co-located → extracted)
- **Decision 11:** DTOs follow same pattern (none → minimal → comprehensive)

✅ **Pragmatic vs. Dogmatic:**
- 80% of PoCs don't need use case DTOs (Steel Thread/Pragmatic CA)
- Add DTOs when boundaries justify cost (API validation, stability)
- Avoids premature abstraction

**Integration with Prior Decisions:**
- **Decision 9 (Drivers):** `drivers/rest/schemas/` for API DTOs ← Decision 11 uses this location
- **Decision 10 (Ports):** Two-step evolution (co-located → extracted) ← Same pattern for DTOs
- **All three decisions** share progressive evolution philosophy

**Impact:** MEDIUM - Affects use case interfaces, orchestrator conversion logic, and test patterns

**Status:** ✅ RESOLVED - Minimal DTOs with progressive evolution (no DTOs → driver DTOs → use case DTOs)

---

## Decision 12: Ports Location and Naming

**What I did:**
- Ports in `core/interfaces/`
- Named as interfaces: `IImageGenerator`, `IStorageAdapter`

**Context from CLEAN_ARCHITECTURE_ANALYSIS:**
- **Calibration-service**: `application/repositories/` (ABC) and `application/services/`
- **Lean-clean**: `application/ports/` (Protocol)
- **nikolovlazar**: Shows interfaces in Application layer

**Question for you:**
Where should ports/interfaces live and how should they be named?

- [ ] **Option A: core/interfaces/** (what I did - nikolovlazar)
  ```
  app/core/
  ├─ entities/
  ├─ use_cases/
  └─ interfaces/
     ├─ image_generator.py    # IImageGenerator
     └─ storage.py            # IStorageAdapter
  ```
  - Pro: Follows nikolovlazar pattern, clear "interfaces" folder
  - Con: Doesn't distinguish repositories from services

- [ ] **Option B: application/ports/** (lean-clean)
  ```
  app/application/
  ├─ use_cases/
  └─ ports/
     ├─ llm_port.py
     └─ storage_port.py
  ```
  - Pro: Matches Ports & Adapters terminology
  - Con: "ports" less familiar than "interfaces"

- [ ] **Option C: application/repositories/ + services/** (calibration-service)
  ```
  app/application/
  ├─ use_cases/
  ├─ repositories/
  │  └─ calibration_repository.py
  └─ services/
     └─ external_api_service.py
  ```
  - Pro: Distinguishes persistence from external services
  - Con: More folders, assumes repository pattern

**Your decision:** ✅ **Resolved by Decision 10's Two-Step Evolution**

**Rationale:**

Decision 10 already established the complete ports location and naming strategy through its two-step evolution approach.

### **Steel Thread & Pragmatic CA: Co-Located Protocols**

Ports (protocols) live **alongside implementations**:

```
app/
├─ adapters/
│  ├─ imagegen/
│  │  ├─ protocol.py        # ← IImageGenerator (co-located)
│  │  ├─ fake.py
│  │  └─ openai.py
│  └─ storage/
│     ├─ protocol.py        # ← IStorageAdapter (co-located)
│     ├─ fake.py
│     └─ s3.py
└─ infrastructure/
   └─ repositories/
      └─ campaign/
         ├─ protocol.py      # ← ICampaignRepository (co-located)
         ├─ in_memory.py
         └─ postgres.py
```

**Why co-located:**
- ✅ Simplicity: Port + implementations in one place
- ✅ Fast discovery: Easy to find interface and implementations together
- ✅ Minimal boilerplate: No separate folder structure needed
- ✅ 80% of PoCs benefit from this simplicity

---

### **Full CA: Extracted to application/ports/**

Ports extracted to **centralized location** split by type:

```
app/
├─ entities/
├─ application/
│  ├─ use_cases/
│  ├─ ports/                         # ← Ports extracted here
│  │  ├─ services/                   # External service contracts
│  │  │  ├─ image_generator.py      # IImageGenerator protocol
│  │  │  ├─ storage_adapter.py      # IStorageAdapter protocol
│  │  │  ├─ crm_client.py           # ICrmClient protocol
│  │  │  └─ analytics_client.py     # IAnalyticsClient protocol
│  │  └─ repositories/               # Persistence contracts
│  │     ├─ campaign_repository.py  # ICampaignRepository protocol
│  │     └─ creative_repository.py  # ICreativeRepository protocol
│  └─ dtos/
├─ interface_adapters/
│  ├─ orchestrators/
│  └─ presenters/
├─ adapters/                         # Implements ports/services/*
│  ├─ imagegen/
│  │  ├─ fake.py
│  │  ├─ openai.py
│  │  └─ firefly.py
│  ├─ crm/
│  │  ├─ fake_salesforce.py
│  │  └─ salesforce_client.py
│  └─ analytics/
│     ├─ fake_warehouse.py
│     └─ snowflake_client.py
└─ infrastructure/                   # Implements ports/repositories/*
   ├─ orm_models/
   └─ repositories/
      ├─ campaign/
      │  ├─ in_memory.py
      │  ├─ postgres.py
      │  └─ mongodb.py
      └─ creative/
         ├─ in_memory.py
         └─ postgres.py
```

**Why extracted:**
- ✅ Clean boundaries: Ports independent of implementations
- ✅ Clear contracts: All interfaces in one location
- ✅ Enterprise aggregates: Complex domains benefit from formalized contracts
- ✅ Multi-implementation support: One port, many implementations (Postgres, MongoDB, In-Memory)

---

### **Naming Conventions**

From Decision 6 (Protocol), we use **Protocol-based interfaces**:

```python
# app/adapters/imagegen/protocol.py (Steel Thread/Pragmatic CA - co-located)
from typing import Protocol

class IImageGenerator(Protocol):
    """Interface for image generation services.

    Implementations: FakeImageGenerator, OpenAIImageGenerator, FireflyImageGenerator
    """
    async def generate(self, prompt: str, size: str) -> bytes: ...
```

```python
# app/application/ports/services/image_generator.py (Full CA - extracted)
from typing import Protocol

class IImageGenerator(Protocol):
    """Port for image generation services.

    Adapters: app/adapters/imagegen/
    - fake.py: FakeImageGenerator
    - openai.py: OpenAIImageGenerator
    - firefly.py: FireflyImageGenerator
    """
    async def generate(self, prompt: str, size: str) -> bytes: ...
```

**Naming pattern:**
- `I` prefix for interface/protocol (e.g., `IImageGenerator`, `ICampaignRepository`)
- Protocol filename matches concept (e.g., `image_generator.py`, not `i_image_generator.py`)
- Implementation filenames descriptive (e.g., `openai.py`, `postgres.py`)

---

### **Organization: Services vs Repositories**

Split ports by **type of external dependency**:

1. **`ports/services/`** - External service integrations
   - Third-party APIs (OpenAI, Firefly)
   - Internal enterprise services (CRM, data warehouse)
   - Event tracking, storage, analytics

2. **`ports/repositories/`** - Persistence layer
   - Database access (Postgres, MongoDB)
   - In-memory fakes for testing
   - Cache layers

This distinction clarifies **architectural boundaries** and matches **stakeholder mental models** (DevOps thinks "repositories" = persistence, "services" = external integrations).

---

### **Migration Path (Pragmatic CA → Full CA)**

When: Complex domain with many implementations per port, or production deployment

```bash
# 1. Create ports structure
mkdir -p app/application/ports/{services,repositories}

# 2. Extract adapter ports
mv app/adapters/imagegen/protocol.py app/application/ports/services/image_generator.py
mv app/adapters/storage/protocol.py app/application/ports/services/storage_adapter.py
mv app/adapters/events/protocol.py app/application/ports/services/analytics_client.py

# 3. Extract repository ports
mv app/infrastructure/repositories/campaign/protocol.py app/application/ports/repositories/campaign_repository.py

# 4. Update imports (automated find-replace)
# From: from app.adapters.imagegen.protocol import IImageGenerator
# To:   from app.application.ports.services.image_generator import IImageGenerator

# 5. Move use_cases under application/
mkdir -p app/application/use_cases
mv app/use_cases/* app/application/use_cases/

# 6. Run tests (catch broken imports)
pytest tests/acceptance/
```

---

### **Why This Resolution Works**

✅ **Consistency with Decision 6 (Protocol):**
- Use Protocol for all interfaces (not ABC)
- No inheritance required

✅ **Consistency with Decision 10 (Two-Step Evolution):**
- Co-located in early stages
- Extracted when complexity justifies it
- Same philosophy as DTOs (Decision 11)

✅ **Consistency with Decision 8 (Fakes):**
- Fake adapters in `adapters/` (Decision 8)
- In-memory repos in `infrastructure/repositories/` (Decision 8)
- Both implement ports/protocols

✅ **Pragmatic vs. Dogmatic:**
- Don't extract ports prematurely (Steel Thread/Pragmatic CA)
- Extract when enterprise complexity demands it (Full CA)
- Migration path clear and scripted

---

### **Terminology Summary**

| Term | Meaning | Location (Full CA) |
|------|---------|-------------------|
| **Port** | Interface contract defined by application | `application/ports/` |
| **Protocol** | Python typing.Protocol implementation | `protocol.py` files |
| **Service** | External service integration port | `ports/services/` |
| **Repository** | Persistence layer port | `ports/repositories/` |
| **Adapter** | Concrete implementation of service port | `adapters/` |
| **Infrastructure** | Concrete implementation of repository port | `infrastructure/repositories/` |

**Example:**
- **Port:** `IImageGenerator` in `application/ports/services/image_generator.py`
- **Adapters:** `FakeImageGenerator`, `OpenAIImageGenerator`, `FireflyImageGenerator` in `adapters/imagegen/`

---

**Impact:** MEDIUM - Affects interface organization, import paths, and migration strategy (but already resolved by Decision 10)

**Status:** ✅ RESOLVED - Two-step evolution: co-located ports (Steel Thread/Pragmatic CA) → extracted to `application/ports/` split into `services/` and `repositories/` (Full CA)

---

## Summary of Decisions

| # | Decision | Priority | Status |
|---|----------|----------|--------|
| 1 | Controller vs Orchestrator | ⚠️ CRITICAL | ✅ RESOLVED (Orchestrator) |
| 2 | Folder depth | MEDIUM | ✅ RESOLVED (Progressive: flat→moderate→deep) |
| 3 | Document scope | LOW | ✅ RESOLVED (Keep comprehensive) |
| 4 | Example scenario | MEDIUM | ✅ RESOLVED (Localized Campaign) |
| 5 | Code detail level | MEDIUM | ✅ RESOLVED (Detailed but incomplete) |
| 6 | Interface style | LOW | ✅ RESOLVED (Protocol) |
| 7 | Test structure | MEDIUM | ✅ RESOLVED (acceptance/unit/integration) |
| 8 | Fakes location | MEDIUM | ✅ RESOLVED (In adapters/ with caveat) |
| 9 | Drivers layer | HIGH | ✅ RESOLVED (Progressive: CLI+UI always, REST when needed) |
| 10 | Gateways vs Repos/Services | HIGH | ✅ RESOLVED (adapters/ + infrastructure/, two-step evolution) |
| 11 | DTO layer strategy | MEDIUM | ✅ RESOLVED (Minimal DTOs, progressive evolution) |
| 12 | Ports location/naming | MEDIUM | ✅ RESOLVED (Co-located → extracted, Decision 10) |

---

## Next Steps

**✅ Session 02 Decision Phase - COMPLETE**

All 12 architectural decisions have been collaboratively resolved:

1. ✅ **Decision 1:** "Orchestrator" terminology (not "Controller")
2. ✅ **Decision 2:** Progressive folder depth (flat → moderate → deep)
3. ✅ **Decision 3:** Comprehensive single document (all three PoC types)
4. ✅ **Decision 4:** Localized Campaign Generation example scenario
5. ✅ **Decision 5:** Detailed but incomplete code examples
6. ✅ **Decision 6:** Protocol-based interfaces (not ABC)
7. ✅ **Decision 7:** Three-layer test structure (acceptance/unit/integration)
8. ✅ **Decision 8:** Fakes in adapters/ (external) + infrastructure/repositories/ (persistence)
9. ✅ **Decision 9:** drivers/ folder with CLI + UI always, FastAPI when needed
10. ✅ **Decision 10:** adapters/ (external) + infrastructure/ (persistence), two-step port evolution
11. ✅ **Decision 11:** Minimal DTOs with progressive evolution (no DTOs → driver DTOs → use case DTOs)
12. ✅ **Decision 12:** Ports co-located (early) → extracted to application/ports/ (Full CA)

---

**Ready for Implementation Phase:**

1. **Update Framework Document** (`_session-02-framework-folder-structures.md`)
   - Incorporate all 12 resolved decisions
   - Ensure Steel Thread, Pragmatic CA, and Full CA are consistent
   - Add migration paths for all evolution points

2. **Update Session 02 Summary**
   - Document all decisions and rationale
   - Capture key insights (enterprise PoC reality, two-step evolution pattern)
   - Create resolution context files as needed

3. **Verify Documentation Consistency**
   - Check axioms document (already updated)
   - Check blog post (already updated)
   - Ensure all examples match resolved patterns

4. **Commit Session 02 Work**
   - All decisions resolved and documented
   - Framework ready for implementation examples

**Status:** ✅ **ALL 12 DECISIONS RESOLVED** - Ready to update framework document
