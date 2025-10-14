# Campaign Generator - Planning Session Artifacts

**Project:** FDE Take-Home Exercise - Creative Automation for Social Campaigns
**Session Date:** 2025-10-14
**Status:** ✅ Foundation Complete - Ready for Implementation

---

## Quick Start

**To continue with implementation:**

1. **Review foundation documents** (all committed):
   - `_agent-understanding.md` - Complete requirements analysis
   - `_decisions-needed.md` - All 13 architectural decisions resolved
   - `_scenario-context.md` - Clean PDF extraction
   - `SESSION-NOTES.md` - Comprehensive session summary

2. **Use planning deliverables**:
   - `entities.md` - Domain model with Python code examples
   - `campaign-brief-schema.yaml` - Input contract with 3 examples
   - `presentation.md` - 30-min presentation outline (update iteratively)

3. **Next steps** (remaining work):
   - Create `use-cases.md` + `function-composition.md`
   - Create `architecture-diagram.md` + `roadmap.md`
   - Write `session-01-architecture-roadmap.md` (Task 1 guide)
   - Write `session-02-steel-thread-poc.md` (Task 2 guide)

---

## Session Summary

### Completed Deliverables (7 files, 5 commits)

| File                         | Lines | Purpose                             | Status                |
|------------------------------|-------|-------------------------------------|-----------------------|
| `_agent-understanding.md`    | 262   | Requirements + initial analysis     | ✅ Committed (c628bb3) |
| `_decisions-needed.md`       | 895   | 13 architectural decisions resolved | ✅ Committed (3578172) |
| `_scenario-context.md`       | 284   | PDF requirements extraction         | ✅ Committed (f0e0e4e) |
| `SESSION-NOTES.md`           | 347   | Session summary + domain model      | ✅ Committed (10fb0a1) |
| `entities.md`                | ~400  | 6 entities with Python dataclasses  | ✅ Committed (453926a) |
| `campaign-brief-schema.yaml` | ~250  | Input contract + 3 examples         | ✅ Committed (453926a) |
| `presentation.md`            | ~383  | 10-slide presentation outline       | ✅ Committed (453926a) |

**Total:** ~2,800 lines of planning documentation

### Key Decisions Made (13/13 Resolved)

1. **Architecture:** Pragmatic CA (Clean Architecture lite)
2. **Storage:** Local filesystem with adapter interface (MinIO/Weaviate ready)
3. **GenAI:** Dual implementation (Fake + OpenAI + Claude adapters)
4. **Input Format:** YAML (Lean-Clean methodology alignment)
5. **Brand Guidelines:** YAML config with adapter for Weaviate evolution
6. **Locale Handling:** Campaign-level (not product-level)
7. **Localization Scope:** English + Spanish US (bonus feature)
8. **Asset Reuse:** Vector similarity search (details deferred to session-02)
9. **HITL Approval:** Simulated approval with logging (Task 3 foundation)
10. **Nice-to-Have Features:** Logging + legal checks (stubbed methods)
11. **UI:** CLI + Streamlit (Lean-Clean drivers pattern)
12. **Testing:** One feature-level test (blog post style)
13. **Tooling:** Python + pip/requirements.txt (maximum compatibility)

### Technology Stack

| Layer              | Technology            | Purpose                         |
|--------------------|-----------------------|---------------------------------|
| **Image Gen**      | OpenAI DALL-E 3       | Hero images                     |
| **Text Overlay**   | OpenAI API            | Campaign messages on images     |
| **Brand Analysis** | Claude Vision         | Brand understanding from assets |
| **Localization**   | Claude Multilingual   | English ↔ Spanish translation   |
| **Asset Search**   | Weaviate              | Vector similarity for reuse     |
| **Storage**        | MinIO (S3-compatible) | Asset management (Docker)       |
| **UI**             | Streamlit             | Stakeholder dashboard           |
| **CLI**            | Typer (Python)        | Automation interface            |
| **Language**       | Python 3.11+          | Core implementation             |
| **Dependencies**   | pip/requirements.txt  | Package management              |

---

## Domain Model

### Core Entities (6)

```python
# 1. BrandSummary - Brand identity and guidelines
brand_id, name, description, colors, voice_tone, target_audiences,
target_regions, products, campaign_slogans, logo_url

# 2. CampaignBrief - Input contract (YAML)
brief_id, brand_id, campaign_slogan, target_region, target_audience,
target_locales, products: List[Product], aspects: List[str]

# 3. Product - Product details
name, palette_words: List[str]

# 4. CreativeAsset - Generated output
asset_id, brief_id, brand_id, product_name, audience, locale,
aspect_ratio, message, image_url, reused, meta

# 5. Approval - HITL tracking (Task 3)
approval_id, brief_id, checkpoint_name, status, product_name,
aspect_ratio, approved_by, comment, started_at, finished_at

# 6. Alert - Agentic monitoring (Task 3)
alert_id, brief_id, alert_type, severity, message, context,
created_at, resolved_at, resolution
```

### Pipeline Functions (9 core)

**Brand Understanding:**
1. `understand_brand(input_artifacts) -> BrandSummary` (Claude Vision)
2. `search_similar_brands(brand_summary) -> List[BrandSummary]` (Weaviate)
3. `accept_brand_choice(brand_summary) -> BrandSummary` (HITL simulated)

**Campaign Generation:**
4. `generate_hero_images(brand, products, aspects) -> List[Image]` (OpenAI DALL-E 3)
5. `add_slogan_to_images(images, slogans, locales) -> List[CreativeAsset]` (OpenAI)
6. `localize_campaign_slogans(slogan, locales) -> Dict[str, str]` (Claude)

**Validation:**
7. `validate_brand_compliance(assets, brand) -> ValidationResult` (stubbed)
8. `check_legal_content(message) -> ValidationResult` (prohibited words)
9. `log_approval_checkpoint(checkpoint_name, data) -> None` (structured logging)

---

## Folder Structure (Pragmatic CA)

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
│  │  │  └─ campaign_orchestrator.py   # Main workflow
│  │  └─ presenters/
│  │     └─ campaign_presenter.py
│  │
│  ├─ adapters/                         # External services
│  │  ├─ ai/
│  │  │  ├─ protocol.py                # IAIAdapter interface
│  │  │  ├─ fake.py                    # Demo without API keys
│  │  │  ├─ openai_image.py            # DALL-E 3 + text
│  │  │  └─ claude.py                  # Vision + multilingual
│  │  └─ storage/
│  │     ├─ protocol.py                # IStorageAdapter
│  │     ├─ local.py                   # Local filesystem
│  │     └─ minio.py                   # S3-compatible
│  │
│  └─ infrastructure/                   # Persistence
│     └─ repositories/
│        └─ brand/
│           ├─ protocol.py             # IBrandRepository
│           ├─ yaml_file.py            # YAML loader
│           └─ weaviate.py             # Vector store
│
├─ drivers/                             # Entry points
│  ├─ cli/commands.py                  # Typer CLI
│  └─ ui/streamlit/app.py              # Streamlit UI
│
├─ tests/features/
│  └─ test_localized_campaign.py       # One feature test
│
├─ brands/example-brand.yaml           # Brand configs
├─ briefs/example-brief.yaml           # Campaign briefs
├─ out/assets/                         # Generated outputs
├─ requirements.txt
├─ docker-compose.yaml                 # MinIO + Weaviate
└─ README.md
```

---

## Remaining Work (For Next Session)

### Planning Documents (Est: 1-2 hours)

1. **use-cases.md** (~200 lines)
   - 3 use cases with signatures
   - Input/output specifications
   - Dependencies and workflow

2. **function-composition.md** (~150 lines)
   - Pipeline flow diagram
   - Function dependencies
   - Error handling patterns

3. **architecture-diagram.md** (~200 lines)
   - Textual representation of Pragmatic CA
   - Layer interactions
   - Data flow

4. **roadmap.md** (~150 lines)
   - Task 1 deliverable (1-slide format)
   - 5 phases with timelines
   - Epics and dependencies

### Session Guides (Est: 2-3 hours)

5. **session-01-architecture-roadmap.md** (~400 lines)
   - Complete Task 1 guide
   - Create architecture diagram
   - Create roadmap slide
   - Update presentation (Slides 4, 6)

6. **session-02-steel-thread-poc.md** (~600 lines)
   - Complete Task 2 guide (based on `_session-03a.md`)
   - Steel Thread PoC approach
   - Implementation sequence
   - Testing strategy
   - Update presentation (Slide 7)

**Total Estimated:** 3-5 hours of focused work

---

## How to Use These Artifacts

### For Task 1 (Architecture + Roadmap)

**Input Documents:**
- `_decisions-needed.md` → Technology choices
- `entities.md` → Domain model
- `_scenario-context.md` → Business requirements

**Create:**
1. Architecture diagram (based on Pragmatic CA structure)
2. Roadmap slide (5 phases, timeline, epics)

**Update:**
- `presentation.md` Slides 4 & 6

**Deliverable:** Architecture diagram + 1-slide roadmap (PDF requirement)

### For Task 2 (PoC Implementation)

**Input Documents:**
- `entities.md` → Domain model
- `campaign-brief-schema.yaml` → Input contract
- `_decisions-needed.md` → Technology stack

**Create:**
1. Implement entities in `app/entities/`
2. Create fake adapters in `app/adapters/ai/fake.py`, `app/adapters/storage/local.py`
3. Implement CLI in `drivers/cli/commands.py`
4. Write one feature test in `tests/features/test_localized_campaign.py`
5. Create README with setup instructions

**Update:**
- `presentation.md` Slide 7 (demo)

**Deliverable:** Working PoC + GitHub repository (PDF requirement)

### For Task 3 (Agentic Design)

**Input Documents:**
- `entities.md` → Alert and Approval entities
- `_scenario-context.md` → Monitoring requirements

**Create:**
1. Agentic system design document
2. Sample stakeholder email template
3. Model Context Protocol definition

**Update:**
- `presentation.md` Slide 8

**Deliverable:** Agentic design + stakeholder email (PDF requirement)

### For Presentation (30 minutes)

**Iterative Updates:**
- After Task 1: Update Slides 4, 6 (architecture, roadmap)
- After Task 2: Update Slide 7 (demo screenshots)
- After Task 3: Update Slide 8 (alert examples, email)

**Final Polish:**
- Add visual assets
- Time each section
- Practice transitions
- Prepare Q&A responses

**Deliverable:** 30-minute presentation + demo recording (PDF requirement)

---

## Git History

```
453926a docs(campaign-generator): add entities, schema, and presentation outline
10fb0a1 docs(campaign-generator): add comprehensive session notes
f0e0e4e docs(campaign-generator): add scenario context extraction from PDF
3578172 docs(campaign-generator): resolve all 13 architectural decisions
c628bb3 docs(campaign-generator): add agent understanding document
```

**Branch:** `v1/campaign-generator`

---

## Key Contacts & References

### Lean-Clean Methodology

- `/docs/lean-clean-axioms.md` - Core principles
- `/docs/framework-folder-structures.md` - Pragmatic CA reference
- `/plan/sessions/_session-03a.md` - Steel Thread PoC example

### Claude API Documentation

- **Vision:** https://docs.claude.com/en/docs/build-with-claude/vision
- **Multilingual:** https://docs.claude.com/en/docs/build-with-claude/multilingual-support

### FDE Exercise Requirements

- Original PDF: `_scenario-and-deliverables.pdf` (untracked, will be removed)
- Clean extraction: `_scenario-context.md`
- Time budget: 6-8 hours total across Tasks 1-3

---

## Session Timing

**Start:** 10:30am
**End:** ~1:00pm
**Duration:** ~2.5 hours

**Breakdown:**
- Requirements analysis: 30 min
- Decision making (HITL): 45 min
- Documentation creation: 75 min

**Efficiency:** ~1,100 lines/hour of planning documentation

---

## Success Criteria ✅

- [x] Complete understanding of requirements
- [x] All architectural decisions resolved and documented
- [x] Domain model defined with code examples
- [x] Input contract (YAML schema) with examples
- [x] Presentation outline with iterative update instructions
- [x] Clear next steps for remaining work
- [x] All work committed to git (5 commits, 7 files)

**Status:** ✅ Ready for implementation!

---

## Next Session Prompt

```
Continue campaign-generator planning by creating remaining deliverables.

Context files (all committed):
- plan/sessions/campaign-generator/README.md (this file - start here!)
- plan/sessions/campaign-generator/_decisions-needed.md
- plan/sessions/campaign-generator/entities.md
- plan/sessions/campaign-generator/campaign-brief-schema.yaml
- plan/sessions/campaign-generator/presentation.md

Create:
1. use-cases.md + function-composition.md
2. architecture-diagram.md
3. roadmap.md
4. session-01-architecture-roadmap.md (Task 1 guide)
5. session-02-steel-thread-poc.md (Task 2 guide)

Follow decisions in _decisions-needed.md and use entities from entities.md.
Update presentation.md with 📝 markers as you complete each deliverable.
```
