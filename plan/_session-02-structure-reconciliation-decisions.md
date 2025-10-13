# Session 02 STRUCTURE.md Reconciliation - Decisions Document

**Date:** 2025-10-13
**Purpose:** Identify all conflicts between STRUCTURE.md and Session 02 framework decisions
**Process:** Human-in-the-Loop collaborative decision-making

---

## Executive Summary

STRUCTURE.md was created on 2025-10-11 before Session 02 architectural decisions (2025-10-13). This document identifies all conflicts and proposes decisions for reconciling the two documents to establish a single source of truth for folder structures.

---

## Section 1: Terminology Conflicts

### Conflict 1.1: Domain vs Entities Folder Naming

**STRUCTURE.md:** Uses `domain/` folder
```
src/
├── domain/                  # Core business models
│   └── models.py
```

**Session 02:** Uses `entities/` folder (all 3 PoC types)
```
app/
├── entities/                # Domain models
│   └── campaign.py
```

**Options:**
- **A. Update STRUCTURE.md to use `entities/`** (align with Session 02)
- **B. Keep `domain/` in STRUCTURE.md** (maintain existing)
- **C. Document both as valid alternatives** (flexibility)

**Recommendation:** Option A - Use `entities/` everywhere for consistency

---

### Conflict 1.2: Controllers vs Orchestrators

**STRUCTURE.md:** Uses "controllers" terminology (optional)
```
interface_adapters/
├── controllers/
│   └── creative_controller.py
```

**Session 02 (Decision 1):** Uses "orchestrators" everywhere
```
interface_adapters/
├── orchestrators/
│   └── campaign_orchestrator.py
```

**Options:**
- **A. Update STRUCTURE.md to use `orchestrators/`** (align with Decision 1)
- **B. Keep both terms** (confusing)
- **C. Add clarification note** (explain terminology choice)

**Recommendation:** Option A - Update to "orchestrators" per Decision 1

---

## Section 2: Folder Structure Organization

### Conflict 2.1: Single vs Progressive Structure

**STRUCTURE.md:** Presents single "Pragmatic Clean Architecture" structure
**Session 02:** Defines 3 progressive structures (Steel Thread → Pragmatic CA → Full CA)

**Options:**
- **A. Replace STRUCTURE.md content with Session 02 progressive structures**
- **B. Keep STRUCTURE.md as-is, point to Session 02 for progressive approach**
- **C. Update STRUCTURE.md to show all 3 structures with migration paths**
- **D. Make STRUCTURE.md focus on reference implementations only**

**Recommendation:** Option D - Refocus STRUCTURE.md on reference implementation analysis, defer to Session 02 for framework structures

---

### Conflict 2.2: Application Layer Organization

**STRUCTURE.md:** Has `application/` folder from start
```
src/
├── application/
│   ├── ports/
│   ├── use_cases/
│   └── dtos.py
```

**Session 02:** Progressive approach
- Steel Thread: No `application/` folder (just `use_cases/`)
- Pragmatic CA: Still no `application/` folder
- Full CA: Adds `application/` folder with nested organization

**Options:**
- **A. Update STRUCTURE.md to show progressive evolution**
- **B. Keep STRUCTURE.md showing "ideal" structure**
- **C. Add note about progressive refinement**

**Recommendation:** Option A - Show progressive evolution

---

## Section 3: Infrastructure Layer Organization

### Conflict 3.1: Adapters vs Infrastructure Split

**STRUCTURE.md:** Single `infrastructure/` folder with all external integrations
```
infrastructure/
├── adapters/
│   ├── storage/
│   ├── generation/
│   └── vector/
```

**Session 02 (Decision 10):** Split into `adapters/` and `infrastructure/`
```
adapters/                    # External services
├── imagegen/
├── storage/
└── events/

infrastructure/              # Persistence
├── orm_models/
└── repositories/
```

**Options:**
- **A. Update STRUCTURE.md to match Session 02 split**
- **B. Keep STRUCTURE.md unified approach**
- **C. Document both patterns with trade-offs**

**Recommendation:** Option A - Adopt Session 02 split for clarity

---

### Conflict 3.2: Fakes Location

**STRUCTURE.md:** No explicit fake location strategy

**Session 02 (Decision 8):** Two types of fakes
- Fake adapters in `adapters/` (external services)
- In-memory repos in `infrastructure/repositories/` (persistence)

**Options:**
- **A. Add Session 02 fakes strategy to STRUCTURE.md**
- **B. Leave fakes location undefined in STRUCTURE.md**
- **C. Create separate section for testing patterns**

**Recommendation:** Option A - Add explicit fakes location guidance

---

## Section 4: Drivers Layer

### Conflict 4.1: Drivers Layer Presence and Organization

**STRUCTURE.md:** Shows drivers as simple entry points
```
drivers/
├── cli/
└── api/
```

**Session 02 (Decision 9):** Enterprise PoC Reality - CLI + UI always, FastAPI when needed
```
drivers/
├── cli/        # ALWAYS
├── ui/         # ALWAYS (Streamlit for stakeholder demos)
└── rest/       # WHEN NEEDED (enterprise integration)
```

**Options:**
- **A. Update STRUCTURE.md to emphasize CLI + UI always pattern**
- **B. Keep STRUCTURE.md generic**
- **C. Add enterprise PoC context section**

**Recommendation:** Option A - Emphasize enterprise PoC reality

---

## Section 5: Ports and Interfaces

### Conflict 5.1: Ports Location Strategy

**STRUCTURE.md:** Ports in `application/ports/` from start
```
application/
└── ports/
    ├── storage.py
    ├── generation.py
    └── vector_store.py
```

**Session 02 (Decision 12):** Two-step evolution
- Early: Co-located `protocol.py` with implementations
- Full CA: Extracted to `application/ports/`

**Options:**
- **A. Update STRUCTURE.md to show two-step evolution**
- **B. Keep STRUCTURE.md showing "final" location**
- **C. Add progressive refinement note**

**Recommendation:** Option A - Show two-step evolution pattern

---

### Conflict 5.2: Interface Definition Style

**STRUCTURE.md:** Uses Protocol (aligned with Session 02)
**Session 02 (Decision 6):** Protocol not ABC

**Status:** ✅ No conflict - already aligned

---

## Section 6: DTOs Strategy

### Conflict 6.1: DTO Layer Organization

**STRUCTURE.md:** DTOs in `application/dtos.py`

**Session 02 (Decision 11):** Progressive evolution
- Steel Thread: No DTOs
- Pragmatic CA: DTOs at `drivers/rest/schemas/`
- Full CA: Use case DTOs at `application/use_cases/[name]/dtos.py`

**Options:**
- **A. Update STRUCTURE.md to show progressive DTO evolution**
- **B. Keep simplified approach**
- **C. Add section on DTO strategies**

**Recommendation:** Option A - Show progressive evolution

---

## Section 7: Test Structure

### Conflict 7.1: Test Organization Terminology

**STRUCTURE.md:**
```
tests/
├── unit/
├── integration/
└── e2e/
```

**Session 02 (Decision 7):**
```
tests/
├── acceptance/    # Stakeholder contracts (ALWAYS fakes)
├── unit/
└── integration/
```

**Options:**
- **A. Update STRUCTURE.md to use acceptance/unit/integration**
- **B. Keep both patterns documented**
- **C. Explain terminology differences**

**Recommendation:** Option A - Adopt Session 02 test structure

---

## Section 8: Document Purpose and Scope

### Conflict 8.1: Overall Document Purpose

**STRUCTURE.md:** Mix of reference implementation analysis + recommended structure

**Session 02:** Authoritative framework definition with 3 PoC types

**Options:**
- **A. Refocus STRUCTURE.md on reference implementations only**
- **B. Merge Session 02 content into STRUCTURE.md**
- **C. Rename STRUCTURE.md to REFERENCE-IMPLEMENTATIONS.md**
- **D. Update STRUCTURE.md superseded notice to point to Session 02**

**Recommendation:** Option C + D - Rename and update superseded notice

---

## Section 9: Content Preservation

### Decision 9.1: What to Preserve from STRUCTURE.md

**Valuable content to preserve:**
1. Reference implementation analysis (calibration-service, creative-ai-poc, lean-clean-core-skeleton)
2. Technology stack recommendations
3. Common pitfalls and solutions
4. Migration path concepts (even if updating specifics)
5. Testing strategy patterns
6. Python patterns integration

**Options:**
- **A. Keep all valuable content, update conflicting sections**
- **B. Extract to separate documents**
- **C. Archive original, create new version**

**Recommendation:** Option A - Preserve valuable content in place

---

## Section 10: Document Finalization

### Decision 10.1: Which Documents Move to `/docs/`

**Candidates for `/docs/` (finalized):**
- `_session-02-framework-folder-structures.md` → `framework-folder-structures.md`
- Updated `STRUCTURE.md` → `REFERENCE-IMPLEMENTATIONS.md`
- `lean-clean-axioms.md` (already there)

**Keep in `/plan/` (working documents):**
- All `_session-02-*.md` decision documents
- Templates
- Session-specific files

**Archive/Remove:**
- Superseded content after migration
- Duplicate information

**Options:**
- **A. Move framework document to `/docs/` now**
- **B. Keep everything in `/plan/` until Session 03**
- **C. Gradual migration as documents stabilize**

**Recommendation:** Option A - Move framework to `/docs/`

---

## Section 11: Superseded Notice Update

### Decision 11.1: How to Update STRUCTURE.md Notice

**Current notice:** Points to Session 01

**Options:**
- **A. Update to point to Session 02 framework document**
- **B. Add comprehensive mapping table (old → new)**
- **C. Convert to redirect document**

**Recommendation:** Option A + B - Update notice with mapping

---

## Recommended Action Plan

### Phase 1: Immediate Updates
1. Rename `STRUCTURE.md` to `REFERENCE-IMPLEMENTATIONS.md`
2. Update superseded notice to reference Session 02
3. Update all terminology conflicts (domain→entities, controllers→orchestrators)

### Phase 2: Content Reorganization
1. Extract pure reference implementation analysis
2. Remove conflicting structure recommendations
3. Add cross-references to Session 02 framework

### Phase 3: Document Finalization
1. Move `_session-02-framework-folder-structures.md` to `/docs/framework-folder-structures.md`
2. Move updated `REFERENCE-IMPLEMENTATIONS.md` to `/docs/`
3. Create navigation guide

### Phase 4: Session 02 Closure
1. Update all cross-references
2. Verify consistency across documents
3. Create final commit for Session 02

---

## Summary of Recommendations

| Conflict | Recommendation | Priority |
|----------|---------------|----------|
| Domain vs Entities | Use `entities/` | HIGH |
| Controllers vs Orchestrators | Use `orchestrators/` | HIGH |
| Single vs Progressive Structure | Defer to Session 02 | HIGH |
| Infrastructure Split | Adopt adapters/ + infrastructure/ | HIGH |
| Drivers Pattern | Emphasize CLI + UI always | HIGH |
| Ports Location | Show two-step evolution | MEDIUM |
| DTOs Strategy | Show progressive evolution | MEDIUM |
| Test Structure | Use acceptance/unit/integration | MEDIUM |
| Document Purpose | Rename to REFERENCE-IMPLEMENTATIONS | HIGH |
| Document Location | Move finalized to `/docs/` | HIGH |

---

## Next Steps

**Awaiting your decisions on:**
1. Overall reconciliation approach (update vs rename vs deprecate)
2. Which specific options to choose for each conflict
3. Document finalization strategy
4. Timeline for changes

Once you approve the recommendations, I will:
1. Implement all approved changes
2. Create updated documents
3. Prepare document moves to `/docs/`
4. Complete Session 02 closure

---

**Status:** Ready for your review and decisions