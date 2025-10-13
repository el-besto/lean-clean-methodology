# Session 02: Framework Decisions Needed

**Status:** 🚧 Awaiting stakeholder input

This document captures the key decisions I made unilaterally that need your approval/revision.

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

**Status:** ✅ RESOLVED - See `/plan/_lean-clean-axioms.md` Axiom 2

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

**Your decision:** [AWAITING INPUT]

**Rationale:**

**Impact:** MEDIUM - Affects import paths, navigation

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

**Your decision:** [AWAITING INPUT]

**Rationale:**

**Impact:** LOW - Organizational preference

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

**Your decision:** [AWAITING INPUT]

**Rationale:**

**Impact:** MEDIUM - Affects code examples throughout

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

**Your decision:** [AWAITING INPUT]

**Rationale:**

**Impact:** MEDIUM - Affects how developers will use the framework

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

**Your decision:** [AWAITING INPUT]

**Rationale:**

**Impact:** LOW - Pattern preference

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

**Your decision:** [AWAITING INPUT]

**Rationale:**

**Impact:** MEDIUM - Affects test organization

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

## Summary of Decisions

| # | Decision | Priority | Status |
|---|----------|----------|--------|
| 1 | Controller vs Orchestrator | ⚠️ CRITICAL | 🚧 Awaiting |
| 2 | Folder depth | MEDIUM | 🚧 Awaiting |
| 3 | Document scope | LOW | 🚧 Awaiting |
| 4 | Example scenario | MEDIUM | 🚧 Awaiting |
| 5 | Code detail level | MEDIUM | 🚧 Awaiting |
| 6 | Interface style | LOW | 🚧 Awaiting |
| 7 | Test structure | MEDIUM | 🚧 Awaiting |
| 8 | Fakes location | MEDIUM | 🚧 Awaiting |

---

## Next Steps

Once you've made decisions:
1. I'll revise `_session-02-framework-folder-structures.md` based on your choices
2. We'll review the updated document together
3. Commit the revised version

**Ready to discuss these decisions?** Let's start with Decision 1 (Controller vs Orchestrator) since it's the most critical.
