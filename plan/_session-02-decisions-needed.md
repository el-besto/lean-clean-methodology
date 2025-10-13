# Session 02: Framework Decisions Needed

**Status:** ✅ 7 of 12 Resolved | 🚧 5 Awaiting Discussion

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

**Your decision:** [AWAITING INPUT]

**Rationale:**

**Impact:** MEDIUM - Philosophical question about fakes

---

## Decision 9: Drivers Layer - Does It Exist?

**What I did:**
- No `drivers/` folder - Framework entry points (`server.py`, `cli.py`) at project root
- FastAPI routers would be inline or in top-level `routers/`

**Context from CLEAN_ARCHITECTURE_ANALYSIS:**
- **Calibration-service**: Has `drivers/rest/` with routers, schemas, dependencies, exception handlers
- **Lean-clean**: Has `drivers/rest/controllers/` with thin mappers
- **nikolovlazar diagram**: Shows "Frameworks & Drivers" as outermost layer

**Question for you:**
Should we have an explicit `drivers/` layer?

- [ ] **Option A: No drivers/ folder** (what I did)
  ```
  project/
  ├─ server.py          # FastAPI entry point
  ├─ cli.py             # Typer CLI entry point
  ├─ app/
  │  ├─ interface_adapters/orchestrators/
  ```
  - Pro: Simpler, fewer folders
  - Con: Entry points mixed with app code, unclear layer boundary

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

- [ ] **Option C: drivers/ calls use cases directly** (lean-clean pattern)
  ```
  project/
  ├─ drivers/
  │  └─ rest/
  │     └─ controllers/       # Thin mappers, call use cases
  ```
  - Pro: Skip orchestration for simple PoCs
  - Con: Violates CA (should call orchestrators, not use cases)

**Your decision:** [AWAITING INPUT]

**Rationale:**

**Impact:** HIGH - Affects project structure and entry point organization

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

**Your decision:** [AWAITING INPUT]

**Rationale:**

**Impact:** MEDIUM - Affects adapter organization and terminology

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

**Your decision:** [AWAITING INPUT]

**Rationale:**

**Impact:** MEDIUM - Affects use case interfaces and conversion logic

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

**Your decision:** [AWAITING INPUT]

**Rationale:**

**Impact:** MEDIUM - Affects interface organization

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
| 8 | Fakes location | MEDIUM | 🚧 AWAITING |
| 9 | Drivers layer | HIGH | 🚧 AWAITING |
| 10 | Gateways vs Repos/Services | MEDIUM | 🚧 AWAITING |
| 11 | DTO layer strategy | MEDIUM | 🚧 AWAITING |
| 12 | Ports location/naming | MEDIUM | 🚧 AWAITING |

---

## Next Steps

**Completed:**
- ✅ Resolved Decisions 1-7 collaboratively
- ✅ Reviewed CLEAN_ARCHITECTURE_ANALYSIS.md
- ✅ Identified 5 additional architectural decisions (8-12)
- ✅ Updated this decisions document with missing decisions

**Awaiting Your Input (5 decisions):**
1. **Decision 8: Fakes Location** - adapters/ vs tests/ vs separate fakes/
2. **Decision 9: Drivers Layer** ⚠️ HIGH PRIORITY - Does drivers/ exist? What calls orchestrators?
3. **Decision 10: Gateways vs Repos/Services** - Terminology and organization
4. **Decision 11: DTO Layer Strategy** - Minimal vs Comprehensive DTOs
5. **Decision 12: Ports Location/Naming** - core/interfaces/ vs application/ports/ vs repositories/services/

**After Decisions:**
1. Update framework document with resolved folder structures
2. Ensure consistency across all three PoC types
3. Update Session 02 summary
4. Commit all Session 02 work

**Status:** 7 of 12 decisions resolved | 5 awaiting collaborative review
