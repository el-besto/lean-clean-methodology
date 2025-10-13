# README.lean-clean.md Framework Integration Analysis

**Date:** 2025-10-13
**Purpose:** Analyze how to integrate `/docs/framework-folder-structures.md` into the main README

---

## Executive Summary

The README needs **more than just links** - it needs structural and terminology updates to align with Session 02 decisions. The current structure is inconsistent with the finalized framework and uses outdated references.

---

## Key Issues Identified

### Issue 1: Outdated Reference to Session 01

**Location:** Line 35
**Current:** "See Session 01 architectural decisions for progression paths."
**Problem:** Session 01 has been superseded by Session 02
**Fix:** Update to reference `/docs/framework-folder-structures.md`

---

### Issue 2: Structure Example Inconsistency

**Location:** Lines 36-54 (Framework Structure section)

**Current Structure:**
```
app/
├─ core/          # Domain models & use cases
├─ adapters/      # Infrastructure: storage, imagegen, vector, db
├─ utils/         # Helpers: observability, manifest, brief loader
├─ server.py      # FastAPI (optional micro-API)
├─ models.py      # SQLAlchemy models (Run, Approval, Alert)
└─ db.py          # DB session utilities (Postgres)
```

**Problems:**
1. ❌ Missing `drivers/` folder (Decision 9: CLI + UI always from day 1)
2. ❌ Uses `core/` instead of layered `entities/`, `use_cases/`, `interface_adapters/`
3. ❌ Doesn't show `adapters/` + `infrastructure/` split (Decision 10)
4. ❌ No mention of `orchestrators/` terminology (Decision 1)
5. ❌ `utils/` as cross-cutting (where does this fit in CA layers?)
6. ❌ `server.py` at root (should be in `drivers/rest/`)
7. ❌ `streamlit_app.py` at root (should be in `drivers/ui/`)
8. ⚠️ Mixes Clean Architecture with Phase 8 agentic features (agent_watcher, docker-compose)

**Analysis:** This structure appears to show an "example implementation with Phase 8 extensions" rather than the canonical Clean Architecture folder structure defined in the framework document.

---

### Issue 3: Phase 5 Lacks Framework References

**Location:** Lines 165-186 (Phase 5: Implementation Planning)

**Current Content:**
- Mentions "Select PoC type: Steel Thread, Pragmatic CA, Full CA"
- But doesn't link to detailed structures
- Doesn't explain what each type includes

**Needed:**
- Add reference to `/docs/framework-folder-structures.md` for complete structures
- Add PoC type summary table from framework doc
- Clarify when to choose each type

---

### Issue 4: Phase 7 Lacks Evolution Path Details

**Location:** Lines 206-225 (Phase 7: Reflection & Knowledge Capture)

**Current Content:**
- Mentions "Decide evolution path: Steel Thread → Pragmatic CA, Pragmatic CA → Full CA"
- But doesn't link to migration checklists
- Doesn't explain triggers for evolution

**Needed:**
- Add reference to framework-folder-structures.md Section 5 (Evolution Paths)
- Add decision criteria for when to evolve
- Reference migration checklists

---

### Issue 5: No Reference to Outside-In TDD

**Location:** Phase 6 (Execution & Iteration)

**Problem:** Framework doc emphasizes Outside-In TDD with fakes, but README doesn't mention this workflow

**Needed:**
- Add reference to framework doc Section 2 (Complete Feature Example)
- Mention acceptance tests with stakeholders → fakes → real adapters pattern

---

## Recommended Changes

### Change 1: Update Framework Structure Section (HIGH PRIORITY)

**Option A: Replace with Pragmatic CA structure from framework doc**
- Show canonical Pragmatic CA structure
- Add note: "For Phase 8 extensions (agent_watcher, observability), add to `tools/` and `drivers/`"
- Reference framework doc for complete details

**Option B: Keep current structure but clarify purpose**
- Add header: "Example PoC with Phase 8 Extensions (Agentic + Observability)"
- Add note: "For canonical Clean Architecture structures, see `/docs/framework-folder-structures.md`"
- Update to include `drivers/` folder

**Recommendation:** **Option A** - Use canonical structure, clarify extensions separately

---

### Change 2: Update Note at Line 35 (HIGH PRIORITY)

**Current:**
```markdown
**Note:** The methodology supports three PoC types—**Steel Thread** (1-2 days, minimal layers), **Pragmatic CA** (3-5 days, controllers + presenters), and **Full CA** (production-ready, comprehensive layers). Structure below represents Pragmatic CA. See Session 01 architectural decisions for progression paths.
```

**Proposed:**
```markdown
**Note:** The methodology supports three PoC types—**Steel Thread** (1-2 days, minimal layers), **Pragmatic CA** (3-5 days, orchestrators + presenters), and **Full CA** (production-ready, comprehensive layers). Structure below represents Pragmatic CA.

**For complete folder structures, code examples, and evolution paths, see:**
- [`/docs/framework-folder-structures.md`](docs/framework-folder-structures.md) - Authoritative framework structures for all 3 PoC types
- [`/docs/REFERENCE-IMPLEMENTATIONS.md`](docs/REFERENCE-IMPLEMENTATIONS.md) - Analysis of reference implementations
```

**Changes:**
- ✅ "controllers" → "orchestrators" (Decision 1)
- ✅ Removed outdated "Session 01" reference
- ✅ Added explicit links to framework docs
- ✅ Clarified where to find details

---

### Change 3: Enhance Phase 5 (MEDIUM PRIORITY)

**Add after line 174 ("Select PoC type:"):**

```markdown
**PoC Type Selection:**

| PoC Type | Timeline | Use Case | Evolution Trigger |
|----------|----------|----------|-------------------|
| **Steel Thread** | 1-2 days | Technical feasibility spike | Stakeholders approved, need production path |
| **Pragmatic CA** | 3-5 days | Multi-stakeholder workshop output | Scaling to multi-team or complex domain |
| **Full CA** | Production | Production-grade system | Already in production |

**For detailed folder structures and migration paths, see:**
- [`/docs/framework-folder-structures.md`](docs/framework-folder-structures.md) - Complete structures for all 3 types
```

**Benefits:**
- Quick reference for PoC type selection
- Clear evolution triggers
- Direct link to detailed structures

---

### Change 4: Enhance Phase 7 (MEDIUM PRIORITY)

**Add after line 218 ("Decide evolution path:"):**

```markdown
**Evolution Decision Criteria:**

**Steel Thread → Pragmatic CA:** When stakeholders approved and need production readiness
**Pragmatic CA → Full CA:** When scaling to multi-team, production load, or complex domain

**For migration checklists and bash commands, see:**
- [`/docs/framework-folder-structures.md` Section 5](docs/framework-folder-structures.md#5-evolution-path-how-poc-types-relate) - Complete migration paths

**Typical Evolution Time:**
- Steel Thread → Pragmatic CA: 1-2 days
- Pragmatic CA → Full CA: 2-4 weeks
```

**Benefits:**
- Clear decision criteria
- Time estimates for planning
- Direct link to migration checklists

---

### Change 5: Add Outside-In TDD Reference to Phase 6 (LOW PRIORITY)

**Add to Phase 6 after line 198 (between "Iterate or pivot" and "Outputs:"):**

```markdown
**Testing Approach:**

For enterprise PoCs with multiple stakeholders, use **Outside-In TDD**:
1. Write acceptance tests with stakeholders (executable specifications)
2. Implement with fakes (fast feedback, no API costs)
3. Implement real adapters (parallel development)

**For complete Outside-In workflow and examples, see:**
- [`/docs/framework-folder-structures.md` Section 2](docs/framework-folder-structures.md#2-complete-feature-example-localized-campaign-generation) - Complete feature example with acceptance tests
```

**Benefits:**
- Introduces key testing philosophy
- Links to complete example
- Emphasizes stakeholder collaboration

---

## Implementation Plan

### Phase 1: Critical Updates (Do Now)
1. **Update line 35 note** - Replace Session 01 reference with framework doc links
2. **Replace Framework Structure section** - Use Pragmatic CA canonical structure from framework doc
3. **Add clarification** - Note where Phase 8 extensions (tools/, observability) fit

### Phase 2: Enhanced Guidance (Next)
4. **Enhance Phase 5** - Add PoC type selection table and framework link
5. **Enhance Phase 7** - Add evolution decision criteria and migration link

### Phase 3: Optional Enrichment (If Time)
6. **Add Phase 6 testing reference** - Link to Outside-In TDD example

---

## Proposed New Framework Structure Section

**Replace lines 33-55 with:**

```markdown
## 🏗️ Framework Structure

**Note:** The methodology supports three PoC types—**Steel Thread** (1-2 days, minimal layers), **Pragmatic CA** (3-5 days, orchestrators + presenters), and **Full CA** (production-ready, comprehensive layers). Structure below represents Pragmatic CA.

**For complete folder structures, code examples, and evolution paths, see:**
- [`/docs/framework-folder-structures.md`](docs/framework-folder-structures.md) - Authoritative framework structures for all 3 PoC types
- [`/docs/REFERENCE-IMPLEMENTATIONS.md`](docs/REFERENCE-IMPLEMENTATIONS.md) - Analysis of reference implementations

### Pragmatic CA Structure (Clean Architecture)

```
campaign-generator/
├─ app/
│  ├─ entities/                        # Domain models
│  │  └─ campaign.py
│  ├─ use_cases/                       # Business logic
│  │  └─ generate_campaign_uc.py
│  ├─ interface_adapters/              # Orchestrators & Presenters
│  │  ├─ orchestrators/
│  │  │  └─ campaign_orchestrator.py
│  │  └─ presenters/
│  │     └─ campaign_presenter.py
│  ├─ adapters/                        # External services (OpenAI, S3)
│  │  ├─ imagegen/
│  │  │  ├─ protocol.py
│  │  │  ├─ fake.py
│  │  │  └─ openai.py
│  │  └─ storage/
│  │     ├─ protocol.py
│  │     ├─ fake.py
│  │     └─ s3.py
│  └─ infrastructure/                  # Persistence (Postgres, MongoDB)
│     └─ repositories/
│        └─ campaign/
│           ├─ protocol.py
│           ├─ in_memory.py
│           └─ postgres.py
│
├─ drivers/                            # Entry points (CLI + UI always)
│  ├─ cli/
│  │  └─ commands.py
│  ├─ rest/                            # When enterprise integration needed
│  │  ├─ main.py
│  │  └─ schemas/
│  └─ ui/
│     └─ streamlit/
│        └─ app.py
│
├─ tests/                              # Three-layer test structure
│  ├─ acceptance/                      # Stakeholder contracts (always fakes)
│  ├─ unit/                            # Isolated logic tests
│  └─ integration/                     # Real service tests
│
├─ tools/                              # Development utilities
│  ├─ validate.py
│  └─ init_db.py
│
├─ pyproject.toml
├─ Makefile
└─ docker-compose.yaml
```

### Phase 8 Extensions (Agentic + Observability)

When implementing Phase 8 (Agentic System Design), extend the structure:

```
tools/
├─ agent_watcher.py              # Automation watcher
├─ validate.py
└─ init_db.py

drivers/
└─ observability/                # Observability adapters
   ├─ phoenix.py
   └─ arize.py

docker-compose.yaml              # Full stack (app, phoenix, weaviate, postgres)
```

**Key Patterns:**
- **Drivers layer** - CLI + UI always (Enterprise PoC Reality - Decision 9)
- **Fakes in production code** - `adapters/*/fake.py` and `infrastructure/repositories/*/in_memory.py` (Axiom 3)
- **Orchestrators not Controllers** - Stakeholder-friendly terminology (Decision 1)
- **Outside-In TDD** - Acceptance tests with stakeholders → fakes → real adapters
```

**Benefits of this approach:**
1. ✅ Shows canonical Pragmatic CA structure (consistent with framework doc)
2. ✅ Clarifies where Phase 8 extensions fit
3. ✅ Uses correct terminology (orchestrators, drivers, fakes in production)
4. ✅ Links to authoritative framework document
5. ✅ Separates Clean Architecture from Phase 8 concerns
6. ✅ Includes key patterns summary

---

## Summary

**Recommendation:** Implement Phase 1 (critical updates) now. The README currently has:
1. Outdated Session 01 reference
2. Inconsistent structure example
3. Missing framework document references

**Impact of changes:**
- **HIGH:** Makes README consistent with finalized framework
- **HIGH:** Provides clear navigation to detailed structures
- **MEDIUM:** Adds practical guidance for PoC type selection
- **MEDIUM:** Clarifies evolution paths and migration

**Effort estimate:** 30-45 minutes to implement all Phase 1 + Phase 2 changes

---

## Questions for User

1. **Framework Structure section:** Should we replace with canonical Pragmatic CA structure (recommended) or keep current structure with clarifications?
2. **Phase 5 and 7 enhancements:** Should we add the summary tables and links now, or defer to later?
3. **Phase 6 Outside-In TDD:** Is this reference needed, or is it too much detail for README?

---

**Status:** Ready for your review and approval to proceed
