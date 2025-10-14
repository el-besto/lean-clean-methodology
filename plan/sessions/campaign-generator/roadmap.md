# Campaign Generator: Implementation Roadmap

**Status:** Task 1 Deliverable
**Format:** 1-Slide Summary (PDF Requirement)
**Timeline:** 10 weeks (5 phases)
**Purpose:** Implementation plan for presentation (Slide 6)

---

## Executive Summary

**Project:** Creative Automation Pipeline for Social Ad Campaigns
**Client:** Global consumer goods company
**Objective:** Enable 100s of localized campaigns monthly through AI-driven automation
**Timeline:** 10 weeks (5 phases)
**Team Size:** 2-3 engineers + 1 product owner

---

## 5-Phase Roadmap Overview

```
Phase 1: Foundation        │██████████                                    │ Weeks 1-2
Phase 2: AI Integration    │          ██████████                          │ Weeks 3-4
Phase 3: Intelligence      │                    ██████████                │ Weeks 5-6
Phase 4: Production        │                              ██████████      │ Weeks 7-8
Phase 5: Agentic System    │                                        ██████│ Weeks 9-10
```

---

## Phase 1: Foundation (Weeks 1-2)

**Goal:** Establish core architecture and domain model

### Epics

**Epic 1.1: Domain Model & Entities** (3 days)
- [ ] Define 6 core entities (BrandSummary, CampaignBrief, CreativeAsset, Approval, Alert, ValidationResult)
- [ ] Implement Python dataclasses with validation methods
- [ ] Create entity unit tests
- [ ] **Deliverable:** `app/entities/` with 6 entity files

**Epic 1.2: Campaign Brief Schema** (2 days)
- [ ] Define YAML schema for campaign briefs
- [ ] Create 3 example campaign briefs (multi-product, multi-locale, multi-aspect)
- [ ] Implement YAML parser with validation
- [ ] **Deliverable:** `campaign-brief-schema.yaml` + `briefs/` examples

**Epic 1.3: CLI Foundation** (2 days)
- [ ] Set up Typer CLI structure
- [ ] Implement `generate`, `validate`, `list-brands` commands
- [ ] Add argument parsing and help text
- [ ] **Deliverable:** `drivers/cli/commands.py`

**Epic 1.4: Fake Adapters** (3 days)
- [ ] Create IAIAdapter protocol (generate_image, overlay_text, understand_brand, localize)
- [ ] Implement FakeAIAdapter (returns placeholder images/text)
- [ ] Create IStorageAdapter protocol (save, load, list)
- [ ] Implement LocalStorageAdapter (filesystem operations)
- [ ] **Deliverable:** `app/adapters/ai/fake.py`, `app/adapters/storage/local.py`

**Epic 1.5: Project Infrastructure** (2 days)
- [ ] Set up folder structure (Pragmatic CA layout)
- [ ] Create `requirements.txt` with dependencies
- [ ] Write initial README with setup instructions
- [ ] Add .gitignore for Python projects
- [ ] **Deliverable:** Project scaffold + documentation

**Phase 1 Milestone:** ✅ Core structure complete, CLI runs with fake adapters

**Time Budget:** 2 weeks (80 hours)
**Risk:** Low - No external dependencies yet

---

## Phase 2: AI Integration (Weeks 3-4)

**Goal:** Integrate OpenAI and Claude APIs for real generation

### Epics

**Epic 2.1: OpenAI Image Generation** (3 days)
- [ ] Implement OpenAI DALL-E 3 adapter
- [ ] Create prompt engineering for hero images (product + brand guidelines)
- [ ] Handle API errors and retries
- [ ] Add image quality validation
- [ ] **Deliverable:** `app/adapters/ai/openai_image.py`

**Epic 2.2: OpenAI Text Overlay** (2 days)
- [ ] Implement text overlay on images (OpenAI API or PIL)
- [ ] Handle multiple aspect ratios (1:1, 9:16, 16:9)
- [ ] Font selection and positioning logic
- [ ] **Deliverable:** Text overlay capability in OpenAI adapter

**Epic 2.3: Claude Vision for Brand Understanding** (3 days)
- [ ] Implement Claude Vision adapter
- [ ] Create prompt for brand analysis from assets
- [ ] Parse Claude response into BrandSummary entity
- [ ] Handle image upload and API interaction
- [ ] **Deliverable:** `app/adapters/ai/claude.py` (Vision)

**Epic 2.4: Claude Multilingual Localization** (2 days)
- [ ] Extend Claude adapter for multilingual translation
- [ ] Implement English → Spanish (US) localization
- [ ] Context preservation (brand voice, tone)
- [ ] **Deliverable:** Multilingual capability in Claude adapter

**Epic 2.5: Use Cases Implementation** (4 days)
- [ ] Implement UnderstandBrandUC (Claude Vision)
- [ ] Implement GenerateCampaignUC (OpenAI + Claude)
- [ ] Implement ValidateCampaignUC (legal checks, stubbed compliance)
- [ ] Wire adapters to use cases via dependency injection
- [ ] **Deliverable:** `app/use_cases/` with 3 use case files

**Phase 2 Milestone:** ✅ Real AI generation working, end-to-end campaign generation

**Time Budget:** 2 weeks (80 hours)
**Risk:** Medium - API dependencies, rate limits, prompt engineering

---

## Phase 3: Intelligence (Weeks 5-6)

**Goal:** Add intelligent asset reuse and brand matching

### Epics

**Epic 3.1: Weaviate Setup** (2 days)
- [ ] Add Weaviate to `docker-compose.yaml`
- [ ] Define schema for brands and assets
- [ ] Create initialization script
- [ ] **Deliverable:** Weaviate running locally, schema defined

**Epic 3.2: Brand Repository (Weaviate)** (3 days)
- [ ] Implement IBrandRepository protocol
- [ ] Create YAMLBrandRepository (load from config files)
- [ ] Create WeaviateBrandRepository (vector search)
- [ ] Implement brand similarity search
- [ ] **Deliverable:** `app/infrastructure/repositories/brand/weaviate.py`

**Epic 3.3: Asset Reuse via Vector Search** (4 days)
- [ ] Implement asset embedding generation (image → vector)
- [ ] Store assets in Weaviate with metadata
- [ ] Implement similarity search for asset reuse
- [ ] Update GenerateCampaignUC to check for existing assets first
- [ ] **Deliverable:** Intelligent asset reuse working

**Epic 3.4: Validation Pipeline** (3 days)
- [ ] Implement prohibited words check (legal validation)
- [ ] Stub brand compliance checks (logo presence, color usage)
- [ ] Create ValidationResult entity population
- [ ] Integrate validation into GenerateCampaignUC
- [ ] **Deliverable:** Validation pipeline complete

**Epic 3.5: Orchestrator & Presenters** (2 days)
- [ ] Implement CampaignOrchestrator (coordinate use cases)
- [ ] Implement CampaignPresenter (format outputs)
- [ ] Wire orchestrator to CLI
- [ ] **Deliverable:** `app/interface_adapters/orchestrators/campaign_orchestrator.py`

**Phase 3 Milestone:** ✅ Intelligent asset reuse, brand matching, validation working

**Time Budget:** 2 weeks (80 hours)
**Risk:** Medium - Weaviate learning curve, embedding quality

---

## Phase 4: Production (Weeks 7-8)

**Goal:** Production-ready features and stakeholder interfaces

### Epics

**Epic 4.1: Streamlit UI** (4 days)
- [ ] Create Streamlit dashboard layout
- [ ] Implement campaign brief upload/creation form
- [ ] Add asset gallery view (generated images)
- [ ] Add approval workflow UI (Task 3 foundation)
- [ ] **Deliverable:** `drivers/ui/streamlit/app.py`

**Epic 4.2: MinIO Integration** (2 days)
- [ ] Add MinIO to `docker-compose.yaml`
- [ ] Implement MinIOStorageAdapter (S3-compatible)
- [ ] Update orchestrator to use MinIO for production
- [ ] Test asset upload/download
- [ ] **Deliverable:** `app/adapters/storage/minio.py`

**Epic 4.3: Logging & Observability** (3 days)
- [ ] Implement structured logging throughout pipeline
- [ ] Add log aggregation (file-based or stdout)
- [ ] Create Approval checkpoints (simulated HITL)
- [ ] Log all GenAI API calls with parameters
- [ ] **Deliverable:** Comprehensive logging for Task 3

**Epic 4.4: Testing** (3 days)
- [ ] Write feature-level test (TestLocalizedCampaignFeature)
- [ ] Test end-to-end campaign generation
- [ ] Test validation pipeline
- [ ] Add unit tests for critical business logic
- [ ] **Deliverable:** `tests/features/test_localized_campaign.py`

**Epic 4.5: Documentation & Handoff** (2 days)
- [ ] Complete README with setup instructions
- [ ] Document API keys and environment setup
- [ ] Create architecture diagram (visual)
- [ ] Write deployment guide
- [ ] **Deliverable:** Production-ready documentation

**Phase 4 Milestone:** ✅ Production-ready PoC, CLI + UI working, comprehensive docs

**Time Budget:** 2 weeks (80 hours)
**Risk:** Low - Building on solid foundation

---

## Phase 5: Agentic System (Weeks 9-10)

**Goal:** AI-driven monitoring, alerting, and stakeholder communication

### Epics

**Epic 5.1: Monitoring System** (3 days)
- [ ] Implement log ingestion pipeline
- [ ] Define monitoring rules (asset count, failures, timeouts)
- [ ] Create Alert entity population logic
- [ ] **Deliverable:** Monitoring system detecting issues

**Epic 5.2: Alert Generation** (2 days)
- [ ] Implement alert triggers (insufficient variants, failures)
- [ ] Define alert severity levels (info, warning, error, critical)
- [ ] Store alerts in structured format (JSON/database)
- [ ] **Deliverable:** Alert generation working

**Epic 5.3: Model Context Protocol** (3 days)
- [ ] Define Model Context Protocol schema (alert → LLM context)
- [ ] Implement context builder (gather relevant campaign data)
- [ ] Create prompt for Claude to generate human-readable messages
- [ ] **Deliverable:** LLM-generated stakeholder messages

**Epic 5.4: Stakeholder Communication** (3 days)
- [ ] Implement email adapter (SMTP)
- [ ] Create email templates for different alert types
- [ ] Integrate Model Context Protocol → Email
- [ ] Test end-to-end alert → email flow
- [ ] **Deliverable:** `app/adapters/email/smtp.py`, email templates

**Epic 5.5: Agentic Design Documentation** (3 days)
- [ ] Write comprehensive agentic system design document
- [ ] Create sample stakeholder email examples
- [ ] Document Model Context Protocol schema
- [ ] Add Task 3 deliverable to presentation
- [ ] **Deliverable:** Task 3 documentation complete

**Phase 5 Milestone:** ✅ Agentic system complete, stakeholder communication automated

**Time Budget:** 2 weeks (80 hours)
**Risk:** Low - Extension of existing architecture

---

## Dependency Map

```
Phase 1 (Foundation)
    │
    ├─→ Epic 1.1 (Entities) ──────────┐
    ├─→ Epic 1.2 (Schema)             │
    ├─→ Epic 1.3 (CLI)                │
    ├─→ Epic 1.4 (Fake Adapters)      │
    └─→ Epic 1.5 (Infrastructure)     │
                                      │
                                      ▼
Phase 2 (AI Integration)          (Depends on Phase 1)
    │
    ├─→ Epic 2.1 (OpenAI Images)      │
    ├─→ Epic 2.2 (Text Overlay)       │
    ├─→ Epic 2.3 (Claude Vision)      │
    ├─→ Epic 2.4 (Multilingual)       │
    └─→ Epic 2.5 (Use Cases) ─────────┤
                                      │
                                      ▼
Phase 3 (Intelligence)            (Depends on Phase 2)
    │
    ├─→ Epic 3.1 (Weaviate)           │
    ├─→ Epic 3.2 (Brand Repo)         │
    ├─→ Epic 3.3 (Asset Reuse)        │
    ├─→ Epic 3.4 (Validation)         │
    └─→ Epic 3.5 (Orchestrator) ──────┤
                                      │
                                      ▼
Phase 4 (Production)              (Depends on Phase 3)
    │
    ├─→ Epic 4.1 (Streamlit)          │
    ├─→ Epic 4.2 (MinIO)              │
    ├─→ Epic 4.3 (Logging)            │
    ├─→ Epic 4.4 (Testing)            │
    └─→ Epic 4.5 (Documentation) ─────┤
                                      │
                                      ▼
Phase 5 (Agentic)                 (Depends on Phase 4 logging)
    │
    ├─→ Epic 5.1 (Monitoring)
    ├─→ Epic 5.2 (Alerts)
    ├─→ Epic 5.3 (Model Context)
    ├─→ Epic 5.4 (Communication)
    └─→ Epic 5.5 (Documentation)
```

---

## Critical Path

**Total Duration:** 10 weeks

**Critical Path Epics:**
1. Epic 1.1 (Entities) → Epic 2.5 (Use Cases) → Epic 3.5 (Orchestrator) → Epic 4.3 (Logging) → Epic 5.3 (Model Context Protocol)

**These epics must complete on time to avoid project delay.**

**Parallelizable Work:**
- Phase 1: Epics 1.2-1.5 can run parallel after 1.1 completes
- Phase 2: Epics 2.1-2.4 can run parallel, Epic 2.5 depends on all
- Phase 3: Epic 3.1 can start early, 3.2-3.4 parallel after Weaviate ready
- Phase 4: Epics 4.1, 4.2, 4.4 can run parallel with 4.3
- Phase 5: Epics 5.1-5.4 have dependencies, but 5.5 (docs) parallel

---

## Resource Allocation

### Team Composition

**Engineer 1: Backend/AI Specialist**
- Phase 1: Entities, Use Cases
- Phase 2: AI Integration (OpenAI + Claude)
- Phase 3: Weaviate, Asset Reuse
- Phase 5: Model Context Protocol

**Engineer 2: Full-Stack Generalist**
- Phase 1: CLI, Fake Adapters
- Phase 3: Orchestrator, Validation
- Phase 4: Streamlit UI, MinIO
- Phase 5: Monitoring, Alerting

**Engineer 3 (Part-time): DevOps/Infrastructure**
- Phase 1: Project scaffold, Docker
- Phase 3: Weaviate setup
- Phase 4: MinIO setup, Deployment
- Phase 5: Monitoring infrastructure

**Product Owner**
- All phases: Requirements, acceptance criteria, stakeholder demos
- Phase 5: Define stakeholder communication templates

---

## Risk Assessment

### High-Risk Items

**Risk 1: AI API Rate Limits**
- **Impact:** Delays in Phase 2
- **Mitigation:** Implement retry logic, use fake adapters for testing, request rate limit increases
- **Owner:** Engineer 1

**Risk 2: Weaviate Learning Curve**
- **Impact:** Delays in Phase 3
- **Mitigation:** Start Weaviate research in Phase 1, allocate extra time, have fallback (simple file-based matching)
- **Owner:** Engineer 1

**Risk 3: Prompt Engineering Complexity**
- **Impact:** Poor quality outputs in Phase 2
- **Mitigation:** Iterative prompt refinement, collect example outputs, A/B test prompts
- **Owner:** Engineer 1 + Product Owner

### Medium-Risk Items

**Risk 4: Streamlit UI Scope Creep**
- **Impact:** Delays in Phase 4
- **Mitigation:** Define strict scope (view only for PoC, defer complex workflows)
- **Owner:** Engineer 2 + Product Owner

**Risk 5: Multi-Locale Testing**
- **Impact:** Validation delays in Phase 2-3
- **Mitigation:** Start with English only, add Spanish incrementally, use Claude for validation
- **Owner:** Engineer 1 + Product Owner

---

## Success Metrics

### Phase 1 Success Criteria
- [ ] CLI runs without errors
- [ ] Fake adapters return placeholder data
- [ ] Project structure matches Pragmatic CA
- [ ] README setup works on fresh machine

### Phase 2 Success Criteria
- [ ] Generate 12 assets (2 products × 3 aspects × 2 locales)
- [ ] OpenAI API calls succeed with quality images
- [ ] Claude Vision extracts brand summary accurately
- [ ] Localized slogans preserve brand voice

### Phase 3 Success Criteria
- [ ] Weaviate finds similar brands (>80% accuracy)
- [ ] Asset reuse reduces API calls by 30%+
- [ ] Validation catches prohibited words (100% recall)
- [ ] Orchestrator coordinates use cases smoothly

### Phase 4 Success Criteria
- [ ] Streamlit UI displays campaign results
- [ ] MinIO stores/retrieves assets reliably
- [ ] Logging captures all key events
- [ ] Feature test passes end-to-end

### Phase 5 Success Criteria
- [ ] Alerts trigger on insufficient variants (<3)
- [ ] Model Context Protocol generates clear messages
- [ ] Stakeholder emails send successfully
- [ ] Agentic design documentation complete

---

## 1-Slide Summary (PDF Format)

**For Presentation Slide 6:**

```
┌─────────────────────────────────────────────────────────────────────────┐
│              CAMPAIGN GENERATOR: 10-WEEK ROADMAP                        │
└─────────────────────────────────────────────────────────────────────────┘

PHASE 1: FOUNDATION (Weeks 1-2)
✅ Core entities & domain model
✅ Campaign brief schema (YAML)
✅ CLI foundation (Typer)
✅ Fake adapters for testing
→ Milestone: Structure complete, CLI runs

PHASE 2: AI INTEGRATION (Weeks 3-4)
🔄 OpenAI DALL-E 3 (hero images)
🔄 OpenAI text overlay
🔄 Claude Vision (brand analysis)
🔄 Claude multilingual (localization)
🔄 Use cases implementation
→ Milestone: Real AI generation working

PHASE 3: INTELLIGENCE (Weeks 5-6)
📅 Weaviate vector DB setup
📅 Brand similarity search
📅 Asset reuse (vector search)
📅 Validation pipeline
📅 Orchestrator & presenters
→ Milestone: Intelligent reuse & validation

PHASE 4: PRODUCTION (Weeks 7-8)
📅 Streamlit UI dashboard
📅 MinIO (S3-compatible storage)
📅 Logging & observability
📅 Testing (feature-level)
📅 Documentation & handoff
→ Milestone: Production-ready PoC

PHASE 5: AGENTIC SYSTEM (Weeks 9-10)
📅 Monitoring system
📅 Alert generation
📅 Model Context Protocol
📅 Stakeholder communication
📅 Agentic design docs
→ Milestone: Automated monitoring & alerts

TIMELINE:  [██ ██ ██ ██ ██ ██ ██ ██ ██ ██]
           W1 W2 W3 W4 W5 W6 W7 W8 W9 W10

TEAM: 2-3 Engineers + 1 Product Owner
BUDGET: 10 weeks (~400 engineering hours)

KEY RISKS:
⚠️  AI API rate limits (Phase 2)
⚠️  Weaviate learning curve (Phase 3)
⚠️  Prompt engineering quality (Phase 2)

SUCCESS METRICS:
✓ Generate 12 assets (2×3×2) in <5 min
✓ Asset reuse reduces API calls 30%+
✓ Validation 100% recall on prohibited words
✓ Stakeholder alerts auto-generated
```

---

## Appendix: Epic Details

### Epic Sizing Guide
- **Small (1-2 days):** Single component, well-defined, low complexity
- **Medium (3-4 days):** Multiple components, some unknowns, medium complexity
- **Large (5+ days):** Complex integration, significant unknowns, high risk

### Epic Template
```
Epic X.Y: [Name]
- Duration: X days
- Owner: Engineer N
- Dependencies: [List epics]
- Acceptance Criteria:
  - [ ] Criterion 1
  - [ ] Criterion 2
- Risks: [Identified risks]
- Deliverable: [Concrete output]
```

---

## References

**Planning Documents:**
- `_decisions-needed.md` - All 13 architectural decisions resolved
- `entities.md` - Domain model (6 entities)
- `use-cases.md` - Business logic specifications (3 use cases)
- `function-composition.md` - Pipeline flow and functions
- `architecture-diagram.md` - System architecture (Pragmatic CA)

**Lean-Clean Methodology:**
- `/docs/framework-folder-structures.md` - Pragmatic CA reference
- `/plan/sessions/_session-03a.md` - Steel Thread PoC example

**FDE Requirements:**
- `_scenario-context.md` - Clean PDF extraction
- Task 1: Architecture + Roadmap (this document)
- Task 2: PoC Implementation
- Task 3: Agentic System Design

---

**Status:** ✅ Ready for presentation integration (Slide 6)
**Format:** 1-slide summary + detailed appendix
**Total Effort:** 10 weeks, 5 phases, ~400 engineering hours
