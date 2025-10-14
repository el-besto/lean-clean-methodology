# Campaign Generator Planning Session Notes

**Date:** 2025-10-14
**Time:** 10:30am - 12:00pm (approx)
**Session Type:** Human-in-the-Loop Decision Making & Planning
**Status:** ✅ Phase 1 Complete - Ready for Deliverable Creation

---

## Session Objectives

✅ **Primary Goal:** Establish complete architectural foundation for FDE take-home exercise
✅ **Secondary Goal:** Create planning artifacts ready for subsequent implementation sessions

---

## Accomplishments

### 1. Agent Understanding (Complete)

**File:** `_agent-understanding.md` (262 lines)

**Contents:**
- Extracted complete requirements from PDF (Tasks 1-3)
- Mapped to Lean-Clean methodology phases (P0, P2, P3, P8)
- Defined success criteria and deliverable file tree
- Documented business goals, pain points, stakeholders
- Recommended Pragmatic CA structure for 6-8 hour constraint
- Identified initial domain entities and pipeline functions
- Listed 13 open architectural decisions

**Commit:** `c628bb3`

### 2. Architectural Decisions (Complete - 13/13 Resolved)

**File:** `_decisions-needed.md` (895 lines)

**Key Decisions:**

| Decision | Choice | Rationale |
|----------|--------|-----------|
| **1. PoC Architecture** | Pragmatic CA | Demonstrates sophistication within time budget |
| **2. Storage Strategy** | Local + Adapter Interface | MinIO/Weaviate dockerfiles ready |
| **3. GenAI Provider** | Dual (Fake + OpenAI + Claude) | Multiple AI adapters for different functions |
| **4. Input Format** | YAML | Lean-Clean methodology alignment |
| **5. Brand Guidelines** | YAML Config + Adapter | Evolution path to Weaviate |
| **6. Locale Handling** | Campaign-level | Supports multi-locale campaigns |
| **7. Localization Scope** | English + Spanish US | Bonus feature for interview |
| **8. Asset Reuse** | Vector Similarity | Details deferred to session-02 |
| **9. HITL Approval** | Simulated + Logging | Foundation for Task 3 |
| **10. Nice-to-Have** | Logging + Legal Checks | Stubbed in orchestrator |
| **11. UI** | CLI + Streamlit | Lean-Clean drivers pattern |
| **12. Testing** | One Feature Test | Blog post style |
| **13. Tooling** | Python + pip | Maximum compatibility |

**Technology Stack:**
- **OpenAI API:** Image generation, text overlay
- **Claude Vision API:** Brand summary generation
- **Claude API:** Multilingual localization
- **MinIO:** S3-compatible storage (Docker)
- **Weaviate:** Vector store (Docker)
- **Streamlit:** UI dashboard
- **Python + pip:** Core implementation

**Commit:** `3578172`

### 3. Scenario Context (Complete)

**File:** `_scenario-context.md` (284 lines)

**Contents:**
- Client overview and scale
- 5 business goals (detailed)
- 5 pain points (detailed)
- 4 stakeholder profiles
- Complete Task 1, 2, 3 breakdown
- Time management guidance
- Success metrics
- Technical constraints
- Interview context insights

**Commit:** `f0e0e4e`

---

## Domain Model (From Ideation - Pending Formal Documentation)

### Core Entities

**BrandSummary:**
```python
class BrandSummary:
    brand_id: str
    colors: List[str]
    description: str
    campaign_slogans: List[str]
    target_audiences: List[str]
    target_regions: List[str]
    products: List[str]
```

**CampaignBrief:**
```python
class CampaignBrief:
    brand_id: str
    target_region: str
    target_audience: str
    target_locales: List[str]  # Decision 6: campaign-level
    campaign_slogan: str
    products: List[Product]
    aspects: List[str]  # e.g., ["1:1", "9:16", "16:9"]
```

**Product:**
```python
class Product:
    name: str
    palette_words: List[str]
```

**CreativeAsset:**
```python
class CreativeAsset:
    brand_id: str
    product_id: str
    audience: str
    message: str
    locale: str
    aspect: str  # "1:1", "9:16", "16:9"
    image_url: str
    reused: bool
    meta: dict
```

**Approval:** (HITL tracking - Task 3)
```python
class Approval:
    id: str
    brief_id: str
    status: str
    started_at: datetime
    finished_at: Optional[datetime]
    product: str
    ratio: str
    approved: bool
    comment: str
```

**Alert:** (Telemetry - Task 3)
```python
class Alert:
    id: str
    brief_id: str
    created_at: datetime
    alert_type: str
    message: str
```

### Core Pipeline Functions

**Brand Understanding & Consistency:**
1. `understand_brand(input_artifacts) -> BrandSummary` - Claude Vision
2. `search_similar_brands(brand_summary) -> List[BrandSummary]` - Weaviate
3. `accept_brand_choice(brand_summary) -> BrandSummary` - HITL (simulated + logging)

**Campaign Generation:**
4. `generate_hero_images(brand_summary, products, aspects) -> List[Image]` - OpenAI DALL-E 3
5. `add_slogan_to_images(images, slogans, locales) -> List[CreativeAsset]` - OpenAI text overlay
6. `localize_campaign_slogans(slogan, locales) -> Dict[str, str]` - Claude multilingual

**Validation:**
7. `validate_brand_compliance(assets, brand_summary) -> ValidationResult` - Stubbed
8. `check_legal_content(campaign_message) -> ValidationResult` - Prohibited words check

**HITL Workflows:**
9. `log_approval_checkpoint(checkpoint_name, data) -> None` - Structured logging

---

## Pragmatic CA Structure (Decision 1)

```
campaign-generator/
├─ app/
│  ├─ entities/                         # Domain models
│  │  ├─ brand_summary.py
│  │  ├─ campaign_brief.py
│  │  ├─ product.py
│  │  ├─ creative_asset.py
│  │  ├─ approval.py
│  │  └─ alert.py
│  │
│  ├─ use_cases/                        # Business logic
│  │  ├─ understand_brand_uc.py
│  │  ├─ generate_campaign_uc.py
│  │  └─ validate_campaign_uc.py
│  │
│  ├─ interface_adapters/               # Orchestrators & Presenters
│  │  ├─ orchestrators/
│  │  │  └─ campaign_orchestrator.py   # Main workflow coordinator
│  │  └─ presenters/
│  │     └─ campaign_presenter.py
│  │
│  ├─ adapters/                         # External services
│  │  ├─ ai/
│  │  │  ├─ protocol.py                # IAIAdapter interface
│  │  │  ├─ fake.py                    # Fake for demo without API
│  │  │  ├─ openai_image.py            # DALL-E 3 + text overlay
│  │  │  └─ claude.py                  # Vision + multilingual
│  │  └─ storage/
│  │     ├─ protocol.py                # IStorageAdapter interface
│  │     ├─ local.py                   # Local filesystem
│  │     └─ minio.py                   # S3-compatible (Docker)
│  │
│  └─ infrastructure/                   # Persistence
│     └─ repositories/
│        └─ brand/
│           ├─ protocol.py             # IBrandRepository interface
│           ├─ yaml_file.py            # YAML config loader
│           └─ weaviate.py             # Vector store (Docker)
│
├─ drivers/                             # Entry points
│  ├─ cli/
│  │  └─ commands.py                   # Typer CLI
│  └─ ui/
│     └─ streamlit/
│        └─ app.py                     # Streamlit dashboard
│
├─ tests/                               # One feature test (Decision 12)
│  └─ features/
│     └─ test_localized_campaign.py   # Blog post style
│
├─ tools/                               # Development utilities
│  └─ validate_brief.py
│
├─ brands/                              # Brand YAML configs
│  └─ example-brand.yaml
│
├─ briefs/                              # Campaign brief examples
│  └─ example-brief.yaml
│
├─ out/                                 # Generated assets
│  └─ assets/                          # Organized by product/aspect
│
├─ requirements.txt
├─ Makefile
├─ docker-compose.yaml                  # MinIO + Weaviate
└─ README.md
```

---

## Remaining Deliverables (For Next Session)

### Planning Documents (Session 01 Focus)

1. ✅ **entities.md** - Formal entity definitions with Python type hints
2. ✅ **campaign-brief-schema.yaml** - Input contract with examples
3. ✅ **use-cases.md** - Use case definitions with signatures
4. ✅ **function-composition.md** - Pipeline flow and dependencies
5. ✅ **architecture-diagram.md** - Textual representation for diagram creation
6. ✅ **roadmap.md** - Implementation roadmap (Task 1 deliverable)

### Session Files (Session 01 & 02)

7. ✅ **session-01-architecture-roadmap.md** - Complete Task 1 session guide
8. ✅ **session-02-steel-thread-poc.md** - Complete Task 2 session guide (based on `_session-03a.md`)

### Presentation

9. ✅ **presentation.md** - 10-slide outline (30-minute presentation)

---

## Next Session: Deliverable Creation

**Objective:** Create remaining planning artifacts and session guides

**Sequence:**
1. `entities.md` - Domain model (informed by decisions)
2. `campaign-brief-schema.yaml` - Input contract (YAML format, Decision 4)
3. `use-cases.md` + `function-composition.md` - Pipeline definition
4. `architecture-diagram.md` - Visual representation (Pragmatic CA)
5. `roadmap.md` - Task 1 deliverable (1-slide format)
6. `session-01-architecture-roadmap.md` - Task 1 session guide
7. `session-02-steel-thread-poc.md` - Task 2 session guide
8. `presentation.md` - 10-slide outline tying everything together
9. Commit all artifacts

**Estimated Time:** 1-2 hours

---

## Key References for Implementation

### Lean-Clean Methodology

- `/docs/lean-clean-axioms.md` - Core architectural principles
- `/docs/framework-folder-structures.md` - Pragmatic CA reference
- `/plan/sessions/_session-03a.md` - Steel Thread PoC example
- `../lean-clean-blog/drafts/_wip-lean-clean-the-secret-sauce.md` - TestLocalizedCampaignFeature

### Claude API Documentation

- **Vision:** https://docs.claude.com/en/docs/build-with-claude/vision
- **Multilingual:** https://docs.claude.com/en/docs/build-with-claude/multilingual-support

### Technology Stack

- **OpenAI API:** DALL-E 3 image generation + text overlay
- **Claude API:** Vision (brand understanding) + multilingual (localization)
- **MinIO:** S3-compatible storage (local Docker)
- **Weaviate:** Vector database (local Docker)
- **Streamlit:** Dashboard UI
- **Python + pip:** Core implementation (Decision 13)

---

## Git Log

```
f0e0e4e docs(campaign-generator): add scenario context extraction from PDF
3578172 docs(campaign-generator): resolve all 13 architectural decisions
c628bb3 docs(campaign-generator): add agent understanding document
```

---

## Session End

**Status:** ✅ Foundation Complete - Ready for Next Phase

**What We Have:**
- Complete understanding of requirements
- All architectural decisions resolved and documented
- Clean scenario extraction from PDF
- Domain model outlined
- Technology stack chosen
- Pragmatic CA structure defined

**What's Next:**
- Formalize planning documents (entities, schemas, use cases)
- Create architecture diagram and roadmap
- Write session guides for Task 1 and Task 2
- Create presentation outline

**Ready to proceed with deliverable creation in next session.**
