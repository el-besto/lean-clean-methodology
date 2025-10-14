# Session 02: All 12 Architectural Decisions - Complete Resolution

**Date:** 2025-10-13
**Status:** ✅ ALL DECISIONS RESOLVED
**Impact:** COMPLETE - Framework folder structures fully defined for all 3 PoC types

---

## Executive Summary

All 12 architectural decisions for Framework folder structures have been collaboratively resolved using Human-in-the-Loop methodology. The framework now defines complete, consistent folder structures for:
- **Steel Thread** (1-2 days)
- **Pragmatic CA** (3-5 days)
- **Full CA** (Production)

**Key Pattern Established:** Progressive Evolution with Two-Step Transformation
- Start simple, add organization, formalize when justified
- No rewrites - additive refinement only
- Consistent across ports, DTOs, and drivers

---

## Critical Architectural Patterns

### Pattern 1: Two-Step Evolution
Applied to ports, DTOs, and folder organization:
1. **Co-locate/Simplify** (Steel Thread/Pragmatic CA) - Keep related code together
2. **Extract/Formalize** (Full CA) - Separate concerns when complexity justifies

### Pattern 2: Enterprise PoC Reality
**From Decision 9:** Even Steel Thread needs drivers/ folder because:
- ✅ CLI (ALWAYS) - Automation, reproducibility, CI/CD
- ✅ Streamlit UI (ALWAYS) - Visual stakeholder feedback from day 1
- ⚠️ FastAPI (WHEN NEEDED) - Enterprise integrations, webhooks

Traditional PoCs start with "just a CLI." Enterprise multi-stakeholder PoCs need UI from day 1.

### Pattern 3: Fakes Have Two Forms
**From Decision 8:**
1. **Fake Adapters** → `app/adapters/` (external services, business stakeholders)
2. **In-Memory Repositories** → `app/infrastructure/repositories/` (persistence, IT stakeholders)

---

## Complete Folder Structures

### Steel Thread (1-2 days)

```
project/
├─ app/
│  ├─ entities/
│  │  └─ campaign.py                  # Domain model
│  ├─ use_cases/
│  │  └─ generate_campaign_uc.py      # Business logic (no DTOs - domain objects)
│  └─ adapters/
│     ├─ imagegen/
│     │  ├─ protocol.py               # IImageGenerator (co-located port)
│     │  ├─ fake.py                   # Fake adapter
│     │  └─ openai.py                 # Real implementation
│     └─ storage/
│        ├─ protocol.py               # IStorageAdapter (co-located port)
│        ├─ fake.py
│        └─ local.py
│
├─ drivers/                            # Decision 9: YES from start
│  ├─ cli.py                           # Automation, reproducibility
│  └─ ui/
│     └─ streamlit_app.py              # Stakeholder visual demos
│
├─ tests/
│  ├─ acceptance/
│  │  └─ features/
│  │     └─ test_campaign_feature.py  # Always fakes
│  ├─ unit/
│  │  └─ use_cases/
│  └─ integration/
│     └─ adapters/
│
└─ .streamlit/
   └─ config.toml
```

**Key Characteristics:**
- Zero DTOs (Decision 11)
- Ports co-located with implementations (Decisions 10 & 12)
- drivers/ with CLI + UI from start (Decision 9)
- Flat structure, minimal nesting (Decision 2)
- Fakes in adapters/ (Decision 8)

---

### Pragmatic CA (3-5 days)

```
project/
├─ app/
│  ├─ entities/                        # Domain models
│  │  ├─ campaign.py
│  │  └─ brand_guidelines.py
│  ├─ use_cases/                       # Still use domain objects (no use case DTOs yet)
│  │  ├─ generate_campaign_uc.py
│  │  └─ validate_brand_uc.py
│  ├─ interface_adapters/
│  │  ├─ orchestrators/                # Decision 1: "Orchestrator" not "Controller"
│  │  │  └─ campaign_orchestrator.py  # Converts driver DTOs → domain objects
│  │  └─ presenters/
│  │     └─ campaign_presenter.py
│  ├─ adapters/                        # External services (Decision 10)
│  │  ├─ imagegen/
│  │  │  ├─ protocol.py               # Still co-located (Decision 12)
│  │  │  ├─ fake.py                   # Fake adapter (Decision 8)
│  │  │  ├─ openai.py
│  │  │  └─ firefly.py
│  │  ├─ storage/
│  │  │  ├─ protocol.py
│  │  │  ├─ fake.py
│  │  │  └─ s3.py
│  │  └─ events/
│  │     ├─ protocol.py
│  │     ├─ fake.py
│  │     └─ amplitude.py
│  └─ infrastructure/                  # Persistence (Decision 10)
│     ├─ orm_models/                   # SQLAlchemy (if needed)
│     └─ repositories/
│        └─ campaign/
│           ├─ protocol.py            # ICampaignRepository (co-located)
│           ├─ in_memory.py           # In-memory fake (Decision 8)
│           └─ postgres.py
│
├─ drivers/
│  ├─ cli/
│  │  ├─ __main__.py                   # python -m drivers.cli
│  │  ├─ commands.py
│  │  └─ dependencies.py               # DI module
│  │
│  ├─ rest/                             # Decision 9: Added when enterprise integration
│  │  ├─ main.py                       # FastAPI app
│  │  ├─ routers/
│  │  │  └─ campaigns.py
│  │  ├─ schemas/                      # Decision 11: API DTOs here (Pydantic)
│  │  │  ├─ campaign_request.py       # CampaignRequest(BaseModel)
│  │  │  └─ campaign_response.py      # CampaignResponse(BaseModel)
│  │  └─ dependencies.py
│  │
│  └─ ui/
│     └─ streamlit/
│        ├─ app.py
│        ├─ pages/
│        │  ├─ 1_generate.py          # Creative Lead
│        │  └─ 2_approve.py           # Legal
│        └─ dependencies.py
│
├─ tests/
│  ├─ acceptance/features/
│  ├─ unit/
│  │  ├─ use_cases/
│  │  └─ orchestrators/
│  └─ integration/adapters/
│
└─ .streamlit/
   └─ config.toml
```

**Key Characteristics:**
- Minimal DTOs at driver boundaries only (Decision 11: `drivers/rest/schemas/`)
- Ports still co-located (Decisions 10 & 12)
- Orchestrators added (Decision 1)
- Split adapters/ + infrastructure/ (Decision 10)
- FastAPI added when integrating with enterprise (Decision 9)
- Moderate nesting (Decision 2)

---

### Full CA (Production)

```
project/
├─ app/
│  ├─ entities/                        # Domain models, value objects
│  │  ├─ campaign.py
│  │  ├─ brand_guidelines.py
│  │  └─ creative.py
│  │
│  ├─ application/                     # Decision 2: Organized under application/
│  │  ├─ use_cases/
│  │  │  ├─ generate_campaign/
│  │  │  │  ├─ use_case.py           # Now uses DTOs
│  │  │  │  └─ dtos.py               # Decision 11: Use case DTOs
│  │  │  │     # GenerateCampaignInput
│  │  │  │     # GenerateCampaignOutput
│  │  │  └─ validate_brand/
│  │  │     ├─ use_case.py
│  │  │     └─ dtos.py
│  │  │
│  │  └─ ports/                       # Decision 12: Ports extracted here
│  │     ├─ services/                 # External service contracts
│  │     │  ├─ image_generator.py    # IImageGenerator protocol
│  │     │  ├─ storage_adapter.py    # IStorageAdapter protocol
│  │     │  ├─ crm_client.py         # ICrmClient protocol
│  │     │  └─ analytics_client.py
│  │     └─ repositories/             # Persistence contracts
│  │        ├─ campaign_repository.py # ICampaignRepository protocol
│  │        └─ creative_repository.py
│  │
│  ├─ interface_adapters/
│  │  ├─ orchestrators/
│  │  │  └─ campaign_orchestrator.py  # Converts: API DTO → Use Case DTO → domain
│  │  └─ presenters/
│  │     └─ campaign_presenter.py
│  │
│  ├─ adapters/                        # Implements ports/services/*
│  │  ├─ imagegen/
│  │  │  ├─ fake.py                   # No protocol.py (moved to application/ports/)
│  │  │  ├─ openai.py
│  │  │  └─ firefly.py
│  │  ├─ crm/
│  │  │  ├─ fake_salesforce.py
│  │  │  └─ salesforce_client.py
│  │  └─ analytics/
│  │     ├─ fake_warehouse.py
│  │     └─ snowflake_client.py
│  │
│  └─ infrastructure/                  # Implements ports/repositories/*
│     ├─ orm_models/                   # SQLAlchemy models (separate from domain)
│     │  ├─ campaign_orm.py
│     │  └─ creative_orm.py
│     └─ repositories/
│        ├─ campaign/
│        │  ├─ in_memory.py           # No protocol.py (moved)
│        │  ├─ postgres.py
│        │  └─ mongodb.py
│        └─ creative/
│           ├─ in_memory.py
│           └─ postgres.py
│
├─ drivers/
│  ├─ cli/
│  │  ├─ __main__.py
│  │  ├─ commands/                    # Organized
│  │  │  ├─ generate.py
│  │  │  ├─ validate.py
│  │  │  └─ approve.py
│  │  ├─ formatters/                  # Output formatters
│  │  │  ├─ table.py
│  │  │  └─ json.py
│  │  └─ dependencies.py              # DI container
│  │
│  ├─ rest/
│  │  ├─ main.py
│  │  ├─ routers/
│  │  │  ├─ campaigns.py
│  │  │  ├─ approvals.py
│  │  │  └─ webhooks.py
│  │  ├─ schemas/                      # API DTOs (Pydantic)
│  │  │  ├─ campaign_request.py
│  │  │  ├─ campaign_response.py
│  │  │  └─ approval_request.py
│  │  ├─ middleware/
│  │  │  ├─ auth.py
│  │  │  ├─ logging.py
│  │  │  └─ telemetry.py
│  │  ├─ exception_handlers.py
│  │  └─ dependencies.py
│  │
│  └─ ui/
│     └─ streamlit/
│        ├─ app.py
│        ├─ pages/
│        │  ├─ 1_generate.py          # Creative Lead
│        │  ├─ 2_approve.py           # Legal
│        │  ├─ 3_monitor.py           # IT
│        │  └─ 4_compliance.py        # Legal
│        ├─ components/
│        │  ├─ campaign_card.py
│        │  └─ approval_widget.py
│        └─ dependencies.py
│
├─ tests/
│  ├─ acceptance/features/
│  ├─ unit/
│  │  ├─ use_cases/
│  │  ├─ orchestrators/
│  │  └─ presenters/
│  └─ integration/
│     ├─ adapters/
│     └─ repositories/
│
└─ .streamlit/
   └─ config.toml
```

**Key Characteristics:**
- Comprehensive DTOs (Decision 11: API + use case DTOs)
- Ports extracted to application/ports/ (Decisions 10 & 12)
- Deep nesting under application/ (Decision 2)
- Complete drivers layer with middleware (Decision 9)
- Separate ORM models from domain entities
- Components for Streamlit reusability

---

## Migration Paths

### Steel Thread → Pragmatic CA

**When:** Stakeholder feedback needs more structure, or integrating with enterprise systems

**Changes:**
1. Add orchestrators: `mkdir -p app/interface_adapters/orchestrators`
2. Organize CLI: `mv drivers/cli.py drivers/cli/__main__.py`
3. Add FastAPI (when needed): `mkdir -p drivers/rest/{routers,schemas}`
4. Expand Streamlit: `mkdir drivers/ui/streamlit/pages`
5. Add DI modules: Create `dependencies.py` in each driver

**Key:** No file moves between layers - just organizational nesting.

---

### Pragmatic CA → Full CA

**When:** Production deployment, complex domain, multiple implementations per port

**Changes:**

1. **Extract Ports (Decision 12):**
```bash
mkdir -p app/application/ports/{services,repositories}
mv app/adapters/imagegen/protocol.py app/application/ports/services/image_generator.py
mv app/adapters/storage/protocol.py app/application/ports/services/storage_adapter.py
mv app/infrastructure/repositories/campaign/protocol.py app/application/ports/repositories/campaign_repository.py
```

2. **Add Use Case DTOs (Decision 11):**
```bash
mkdir -p app/application/use_cases/generate_campaign
mv app/use_cases/generate_campaign_uc.py app/application/use_cases/generate_campaign/use_case.py
# Create app/application/use_cases/generate_campaign/dtos.py
```

3. **Update Imports:**
```bash
# Automated find-replace
# From: from app.adapters.imagegen.protocol import IImageGenerator
# To:   from app.application.ports.services.image_generator import IImageGenerator
```

4. **Add Middleware:**
```bash
mkdir -p drivers/rest/middleware
# Create auth.py, logging.py, telemetry.py
```

5. **Verify:**
```bash
pytest tests/acceptance/  # Should still pass
```

---

## Decision-by-Decision Resolution

### Decision 1: "Orchestrator" vs "Controller" Terminology ✅
**Resolution:** "Orchestrator" everywhere
- Folder: `app/interface_adapters/orchestrators/`
- Class: `LocalizedCampaignOrchestrator`
- **Why:** "Controller" overloaded in web dev; stakeholders understand "orchestration"

### Decision 2: Folder Structure Depth ✅
**Resolution:** Progressive (flat → moderate → deep)
- Steel Thread: Flat (2 levels)
- Pragmatic CA: Moderate (3-4 levels)
- Full CA: Deep (4-5 levels with application/)
- **Why:** Structure scales with PoC complexity

### Decision 3: Document Scope ✅
**Resolution:** Comprehensive single document
- All three PoC types in one doc
- **Why:** Evolution path is key insight; side-by-side shows progressive refinement

### Decision 4: Example Scenario ✅
**Resolution:** Localized Campaign Generation
- Multi-stakeholder (Creative, Ad Ops, IT, Legal)
- **Why:** Realistic enterprise AI PoC, shows orchestration patterns

### Decision 5: Code Detail Level ✅
**Resolution:** Detailed but incomplete
- Full class definitions, type hints, async/await
- Not runnable (shows patterns)
- **Why:** Balances clarity with brevity

### Decision 6: Interface Definition Style ✅
**Resolution:** Protocol (not ABC)
```python
from typing import Protocol

class IImageGenerator(Protocol):
    async def generate(self, prompt: str) -> bytes: ...
```
- **Why:** More Pythonic, no inheritance required

### Decision 7: Test Structure ✅
**Resolution:** acceptance/unit/integration layers
```
tests/
├─ acceptance/features/
├─ unit/{use_cases,orchestrators}/
└─ integration/adapters/
```
- **Why:** Matches Outside-In TDD workflow

### Decision 8: Fakes Location ✅
**Resolution:** Two types in different locations
1. **Fake Adapters** → `app/adapters/` (external services)
2. **In-Memory Repositories** → `app/infrastructure/repositories/` (persistence)
- **Why:** Different stakeholder audiences; Axiom 3 non-negotiable

### Decision 9: Drivers Layer ✅
**Resolution:** Progressive evolution with enterprise reality
- **Steel Thread:** drivers/ with CLI + UI (flat)
- **Pragmatic CA:** drivers/cli/, drivers/rest/, drivers/ui/ (organized)
- **Full CA:** Complete drivers with middleware (production)
- **Key:** CLI + UI ALWAYS, FastAPI WHEN NEEDED

### Decision 10: Gateways vs Repositories/Services ✅
**Resolution:** Split adapters/ + infrastructure/ with two-step port evolution
- **adapters/** - External services (OpenAI, CRM, analytics)
- **infrastructure/repositories/** - Persistence (Postgres, MongoDB, in-memory)
- **Ports:** Co-located early → extracted to application/ports/ in Full CA

### Decision 11: DTO Layer Strategy ✅
**Resolution:** Minimal DTOs with progressive evolution
- **Steel Thread:** No DTOs (domain objects everywhere)
- **Pragmatic CA:** API DTOs at `drivers/rest/schemas/` (Pydantic)
- **Full CA:** Add use case DTOs at `app/application/use_cases/[name]/dtos.py`
- **Key:** Same two-step pattern as ports

### Decision 12: Ports Location and Naming ✅
**Resolution:** Resolved by Decision 10's two-step evolution
- **Early:** Co-located (`protocol.py` with implementations)
- **Full CA:** Extracted to `application/ports/` split into `services/` and `repositories/`
- **Naming:** `I` prefix (IImageGenerator), Protocol-based (Decision 6)

---

## Key Insights for Implementation

### Insight 1: Enterprise PoC Reality
Traditional PoCs: "just a CLI"
Enterprise PoCs: CLI + UI from day 1 for stakeholder engagement

### Insight 2: Two-Step Evolution Everywhere
- Ports: Co-located → Extracted
- DTOs: None → Driver boundaries → Use case boundaries
- Drivers: Flat → Organized → Production-ready

### Insight 3: Fakes Have Stakeholder Audiences
- Fake Adapters (adapters/): Business stakeholders see these in workshops
- In-Memory Repos (infrastructure/): IT/DevOps stakeholders see these in tests

### Insight 4: Terminology Consistency
- "Orchestrator" not "Controller" (Decision 1)
- "Fakes" not "Mocks" (Axiom 3)
- Domain-friendly DTO naming: CampaignRequest not CampaignDTO (Axiom 5)

### Insight 5: Progressive = Additive
No rewrites during evolution:
- Steel Thread files stay in place
- Add layers, don't replace
- Imports paths extend (add depth), don't transform (change root)

---

## Documents Updated

1. **`/plan/_session-02-decisions-needed.md`**
   - All 12 decisions with comprehensive resolutions
   - Status: ALL DECISIONS RESOLVED

2. **`/docs/lean-clean-axioms.md`**
   - Updated Axiom 3 with two types of fakes
   - Updated Axiom 5 with DTO naming and locations

3. **`/plan/_session-02-resume-prompt-understanding.md`**
   - Updated with Decisions 8-10 insights

4. **`/plan/_session-02-decision-9-resolution.md`**
   - Context preservation for Decision 9 (drivers layer)

5. **`/lean-clean-blog/drafts/_wip-lean-clean-the-secret-sauce.md`**
   - Updated architecture diagram with drivers layer
   - Added enterprise PoC reality explanation

---

## Next: Framework Document Update

**Ready to update:** `/plan/_session-02-framework-folder-structures.md`

**Updates needed:**
1. Incorporate all 12 resolved decisions
2. Update Steel Thread structure
3. Update Pragmatic CA structure
4. Update Full CA structure
5. Add migration paths
6. Ensure consistency across all three
7. Add code examples matching resolved patterns

**Status:** ✅ ALL DECISIONS RESOLVED - Ready for framework document implementation
