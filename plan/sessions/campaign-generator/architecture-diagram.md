# Campaign Generator: Architecture Diagram

**Status:** Task 1 Deliverable
**Based On:** Pragmatic CA (Decision 1), Technology Stack (Decisions 2-3, 11)
**Purpose:** Visual system architecture for 30-minute presentation (Slide 4)

---

## System Architecture Overview

This document provides a textual and ASCII representation of the Campaign Generator architecture following **Pragmatic Clean Architecture** patterns. This diagram serves as Task 1's primary deliverable and will be converted to visual format for presentation.

---

## High-Level Architecture: Pragmatic Clean Architecture

```
┌───────────────────────────────────────────────────────────────────────────┐
│                                                                           │
│                        CAMPAIGN GENERATOR SYSTEM                          │
│                     Pragmatic Clean Architecture                          │
│                                                                           │
└───────────────────────────────────────────────────────────────────────────┘

┌───────────────────────────────────────────────────────────────────────────┐
│ LAYER 5: DRIVERS (Entry Points)                                           │
│                                                                           │
│  ┌─────────────────────┐              ┌─────────────────────┐             │
│  │   CLI Driver        │              │   UI Driver         │             │
│  │   (Typer)           │              │   (Streamlit)       │             │
│  ├─────────────────────┤              ├─────────────────────┤             │
│  │ commands.py         │              │ app.py              │             │
│  │  - generate         │              │  - Dashboard        │             │
│  │  - validate         │              │  - Asset viewer     │             │
│  │  - list-brands      │              │  - Approval UI      │             │
│  └─────────┬───────────┘              └──────────┬──────────┘             │
│            │                                     │                        │
│            └─────────────────┬───────────────────┘                        │
└──────────────────────────────┼────────────────────────────────────────────┘
                               │
                               │ Dependency Injection
                               ▼
┌───────────────────────────────────────────────────────────────────────────┐
│ LAYER 4: INTERFACE ADAPTERS (Orchestration & Presentation)                │
│                                                                           │
│  ┌─────────────────────────────────┐  ┌──────────────────────────────┐    │
│  │   Orchestrators                 │  │   Presenters                 │    │
│  │   (Workflow Coordination)       │  │   (Response Formatting)      │    │
│  ├─────────────────────────────────┤  ├──────────────────────────────┤    │
│  │ campaign_orchestrator.py        │  │ campaign_presenter.py        │    │
│  │  - coordinate_generation()      │  │  - format_assets()           │    │
│  │  - coordinate_validation()      │  │  - format_summary()          │    │
│  │  - log_approval_checkpoint()    │  │  - format_alert_email()      │    │
│  └─────────────┬───────────────────┘  └──────────┬───────────────────┘    │
│                │                                  │                       │
│                └──────────────────┬───────────────┘                       │
└───────────────────────────────────┼───────────────────────────────────────┘
                                    │
                                    │ Calls Use Cases
                                    ▼
┌─────────────────────────────────────────────────────────────────────────┐
│ LAYER 3: USE CASES (Business Logic)                                     │
│                                                                         │
│  ┌────────────────┐  ┌──────────────────┐  ┌───────────────────────┐    │
│  │ Understand     │  │ Generate         │  │ Validate              │    │
│  │ Brand UC       │  │ Campaign UC      │  │ Campaign UC           │    │
│  ├────────────────┤  ├──────────────────┤  ├───────────────────────┤    │
│  │ understand_    │  │ generate_hero_   │  │ validate_brand_       │    │
│  │   brand()      │  │   images()       │  │   compliance()        │    │
│  │ search_        │  │ localize_        │  │ check_legal_          │    │
│  │   similar_     │  │   slogans()      │  │   content()           │    │
│  │   brands()     │  │ overlay_text()   │  │ create_validation_    │    │
│  │                │  │                  │  │   result()            │    │
│  └────┬───────────┘  └────┬─────────────┘  └──────┬────────────────┘    │
│       │                   │                        │                    │
│       │ Uses              │ Uses                   │ Uses               │
│       ▼                   ▼                        ▼                    │
└─────────────────────────────────────────────────────────────────────────┘
        │                   │                        │
        │                   │                        │
        ▼                   ▼                        ▼
┌───────────────────────────────────────────────────────────────────────────┐
│ LAYER 2: ENTITIES (Domain Models)                                         │
│                                                                           │
│  ┌──────────────┐  ┌──────────────┐  ┌──────────────┐  ┌─────────────┐    │
│  │ Brand        │  │ Campaign     │  │ Creative     │  │ Approval    │    │
│  │ Summary      │  │ Brief        │  │ Asset        │  │             │    │
│  └──────────────┘  └──────────────┘  └──────────────┘  └─────────────┘    │
│                                                                           │
│  ┌──────────────┐  ┌──────────────┐                                       │
│  │ Alert        │  │ Validation   │                                       │
│  │              │  │ Result       │                                       │
│  └──────────────┘  └──────────────┘                                       │
│                                                                           │
└───────────────────────────────────────────────────────────────────────────┘
        │                   │                        │
        │ Depends on        │ Depends on             │ Depends on
        ▼                   ▼                        ▼
┌───────────────────────────────────────────────────────────────────────────┐
│ LAYER 1: ADAPTERS & INFRASTRUCTURE (External Dependencies)                │
│                                                                           │
│  ┌─────────────────────────────────────────────────────────────────────┐  │
│  │ ADAPTERS (External Services)                                        │  │
│  ├─────────────────────────────────────────────────────────────────────┤  │
│  │                                                                     │  │
│  │  ┌───────────────────┐  ┌───────────────────┐  ┌────────────────┐   │  │
│  │  │ AI Adapters       │  │ Storage Adapters  │  │ Other Adapters │   │  │
│  │  ├───────────────────┤  ├───────────────────┤  ├────────────────┤   │  │
│  │  │ protocol.py       │  │ protocol.py       │  │ email.py       │   │  │
│  │  │ (IAIAdapter)      │  │ (IStorageAdapter) │  │ (IEmailSender) │   │  │
│  │  ├───────────────────┤  ├───────────────────┤  └────────────────┘   │  │
│  │  │ fake.py           │  │ local.py          │                       │  │
│  │  │ openai_image.py   │  │ minio.py          │                       │  │
│  │  │ claude.py         │  │                   │                       │  │
│  │  └───────────────────┘  └───────────────────┘                       │  │
│  │                                                                     │  │
│  └─────────────────────────────────────────────────────────────────────┘  │
│                                                                           │
│  ┌─────────────────────────────────────────────────────────────────────┐  │
│  │ INFRASTRUCTURE (Persistence)                                        │  │
│  ├─────────────────────────────────────────────────────────────────────┤  │
│  │                                                                     │  │
│  │  ┌────────────────────────────────────────────────────────────────┐ │  │
│  │  │ Brand Repository                                               │ │  │
│  │  ├────────────────────────────────────────────────────────────────┤ │  │
│  │  │ protocol.py (IBrandRepository)                                 │ │  │
│  │  │ yaml_file.py (Load brands from YAML config)                    │ │  │
│  │  │ weaviate.py (Vector similarity search for brands)              │ │  │
│  │  └────────────────────────────────────────────────────────────────┘ │  │
│  │                                                                     │  │
│  └─────────────────────────────────────────────────────────────────────┘  │
│                                                                           │
└───────────────────────────────────────────────────────────────────────────┘
```

---

## Dependency Flow: Outside-In

```
┌─────────────────────────────────────────────────────────────────────────┐
│                         DEPENDENCY RULE                                 │
│                                                                         │
│  Dependencies point INWARD (toward Entities)                            │
│  Outer layers depend on inner layers, NEVER the reverse                 │
│  Use Cases depend on INTERFACES (defined in Application layer)          │
│  Infrastructure IMPLEMENTS those interfaces (Dependency Inversion)      │
│                                                                         │
└─────────────────────────────────────────────────────────────────────────┘

User Request (CLI/UI)
    ↓
Drivers (CLI/Streamlit)
    ↓
Interface Adapters (Orchestrators)
    ↓
Use Cases (Business Logic)
    ↓
Entities (Domain Models) ←───────────┐
    ↑                                │
    │                                │
    │ (Uses via Interface)           │ (Implements Interface)
    │                                │
Adapter Protocols (Interfaces)  →   Concrete Adapters/Infrastructure
```

---

## Data Flow: Campaign Generation Pipeline

```
┌─────────────────────────────────────────────────────────────────────────┐
│                      CAMPAIGN GENERATION FLOW                           │
└─────────────────────────────────────────────────────────────────────────┘

1. INPUT
   ┌────────────────────────────────────────┐
   │ briefs/holiday-gift-2025-01.yaml       │
   │  - Brand: natural-suds-co              │
   │  - Products: [Lavender Soap, Citrus]   │
   │  - Aspects: [1:1, 9:16, 16:9]          │
   │  - Locales: [en-US, es-US]             │
   └────────────────┬───────────────────────┘
                    │
                    ▼
2. BRAND UNDERSTANDING
   ┌────────────────────────────────────────┐
   │ Use Case: Understand Brand             │
   │  ↓ Load brand config (YAML/Weaviate)   │
   │  ↓ Validate brand_id exists            │
   │  → BrandSummary                        │
   └────────────────┬───────────────────────┘
                    │
                    ▼
3. LOCALIZATION
   ┌────────────────────────────────────────┐
   │ Function: localize_campaign_slogans()  │
   │  ↓ Claude Multilingual API             │
   │  ↓ "Gift Wellness" → "Regalo Bienestar"│
   │  → Dict[locale, slogan]                │
   └────────────────┬───────────────────────┘
                    │
                    ▼
4. GENERATION (Per Product × Aspect × Locale)
   ┌────────────────────────────────────────┐
   │ Use Case: Generate Campaign            │
   │  ↓ Check asset reuse (Weaviate)        │
   │  ↓ Generate hero image (OpenAI DALL-E) │
   │  ↓ Add text overlay (OpenAI API)       │
   │  → CreativeAsset                       │
   └────────────────┬───────────────────────┘
                    │
                    ▼
5. VALIDATION
   ┌────────────────────────────────────────┐
   │ Use Case: Validate Campaign            │
   │  ↓ Legal checks (prohibited words)     │
   │  ↓ Brand compliance (stubbed)          │
   │  → ValidationResult                    │
   └────────────────┬───────────────────────┘
                    │
                    ▼
6. APPROVAL (Simulated)
   ┌────────────────────────────────────────┐
   │ Orchestrator: log_approval_checkpoint  │
   │  ↓ Log checkpoint to structured logs   │
   │  ↓ (Future: HITL interactive approval) │
   │  → Approval                            │
   └────────────────┬───────────────────────┘
                    │
                    ▼
7. OUTPUT
   ┌────────────────────────────────────────┐
   │ out/assets/                            │
   │  ├─ lavender-soap/                     │
   │  │  ├─ en-US/ [1x1.png, 9x16.png ...]  │
   │  │  └─ es-US/ [1x1.png, 9x16.png ...]  │
   │  └─ citrus-shower-gel/                 │
   │     ├─ en-US/ [...]                    │
   │     └─ es-US/ [...]                    │
   └────────────────────────────────────────┘

8. MONITORING (Task 3: Agentic System)
   ┌────────────────────────────────────────┐
   │ Agent monitors:                        │
   │  - Asset count < 3 variants? → Alert   │
   │  - Generation failures? → Alert        │
   │  - Validation failures? → Alert        │
   │  → Model Context Protocol → Email      │
   └────────────────────────────────────────┘
```

---

## Technology Integration Map

```
┌─────────────────────────────────────────────────────────────────────────┐
│                         TECHNOLOGY STACK                                │
└─────────────────────────────────────────────────────────────────────────┘

┌──────────────────────────┐
│ DRIVERS                  │
├──────────────────────────┤
│ CLI: Typer (Python)      │────┐
│ UI: Streamlit            │    │
└──────────────────────────┘    │
                                │
┌──────────────────────────┐    │
│ INTERFACE ADAPTERS       │    │
├──────────────────────────┤    │
│ Python Dataclasses       │◄───┘
│ Function Composition     │
└──────────────────────────┘
                │
                ▼
┌──────────────────────────┐
│ USE CASES                │
├──────────────────────────┤
│ Pure Python Logic        │
└──────────────────────────┘
                │
                ▼
┌──────────────────────────┐
│ ENTITIES                 │
├──────────────────────────┤
│ Python Dataclasses       │
│ Validation Methods       │
└──────────────────────────┘
                │
                ▼
┌──────────────────────────────────────────────────────┐
│ ADAPTERS & INFRASTRUCTURE                            │
├──────────────────────────────────────────────────────┤
│                                                      │
│  AI Services:                                        │
│  ├─ OpenAI API (DALL-E 3, text overlay)              │
│  ├─ Claude API (Vision, multilingual)                │
│  └─ Fake Adapter (demo mode)                         │
│                                                      │
│  Storage:                                            │
│  ├─ Local Filesystem (default)                       │
│  └─ MinIO (S3-compatible, Docker)                    │
│                                                      │
│  Repositories:                                       │
│  ├─ YAML File Loader (brands config)                 │
│  └─ Weaviate (vector similarity search)              │
│                                                      │
└──────────────────────────────────────────────────────┘
```

---

## Adapter Pattern: AI Service Substitution

```
┌─────────────────────────────────────────────────────────────────────────┐
│                       ADAPTER PATTERN EXAMPLE                           │
│                    (AI Service Substitution)                            │
└─────────────────────────────────────────────────────────────────────────┘

┌─────────────────────────────────────────────────┐
│ Use Case: generate_campaign()                   │
│  ↓                                              │
│  Uses: ai_adapter.generate_image(prompt)        │
│         ai_adapter.overlay_text(image, text)    │
└──────────────────────┬──────────────────────────┘
                       │
                       │ Depends on INTERFACE (not implementation)
                       ▼
┌─────────────────────────────────────────────────┐
│ Interface: IAIAdapter (protocol.py)             │
│  - generate_image(prompt) -> bytes              │
│  - overlay_text(image, text) -> bytes           │
└──────────────────────┬──────────────────────────┘
                       │
                       │ Multiple Implementations
                       ▼
    ┌──────────────────┴──────────────┬─────────────────┐
    │                                 │                 │
    ▼                                 ▼                 ▼
┌─────────────┐              ┌─────────────────┐  ┌─────────────┐
│ Fake        │              │ OpenAI          │  │ Claude      │
│ Adapter     │              │ Adapter         │  │ Adapter     │
├─────────────┤              ├─────────────────┤  ├─────────────┤
│ Returns     │              │ OpenAI DALL-E 3 │  │ Claude      │
│ placeholder │              │ API calls       │  │ API calls   │
│ images      │              │                 │  │             │
│             │              │                 │  │             │
│ Use: Demo   │              │ Use: Production │  │ Use: Vision │
│ without API │              │ image gen       │  │ + multi-    │
│ keys        │              │                 │  │ lingual     │
└─────────────┘              └─────────────────┘  └─────────────┘

Benefits:
✅ Easy to switch implementations (config change)
✅ Test with fakes, deploy with real APIs
✅ No Use Case changes when switching providers
✅ Demonstrates Dependency Inversion Principle
```

---

## Folder Structure: Implementation Layout

```
campaign-generator/
├─ app/
│  ├─ entities/                         # LAYER 2: Domain Models
│  │  ├─ brand_summary.py
│  │  ├─ campaign_brief.py
│  │  ├─ product.py
│  │  ├─ creative_asset.py
│  │  ├─ approval.py
│  │  ├─ alert.py
│  │  └─ validation_result.py
│  │
│  ├─ use_cases/                        # LAYER 3: Business Logic
│  │  ├─ understand_brand_uc.py
│  │  ├─ generate_campaign_uc.py
│  │  └─ validate_campaign_uc.py
│  │
│  ├─ interface_adapters/               # LAYER 4: Orchestration
│  │  ├─ orchestrators/
│  │  │  └─ campaign_orchestrator.py
│  │  └─ presenters/
│  │     └─ campaign_presenter.py
│  │
│  ├─ adapters/                         # LAYER 1: External Services
│  │  ├─ ai/
│  │  │  ├─ protocol.py                 # IAIAdapter interface
│  │  │  ├─ fake.py                     # Demo without API keys
│  │  │  ├─ openai_image.py             # DALL-E 3 + text overlay
│  │  │  └─ claude.py                   # Vision + multilingual
│  │  ├─ storage/
│  │  │  ├─ protocol.py                 # IStorageAdapter
│  │  │  ├─ local.py                    # Local filesystem
│  │  │  └─ minio.py                    # S3-compatible
│  │  └─ email/
│  │     ├─ protocol.py                 # IEmailSender
│  │     └─ smtp.py                     # SMTP email
│  │
│  └─ infrastructure/                   # LAYER 1: Persistence
│     └─ repositories/
│        └─ brand/
│           ├─ protocol.py              # IBrandRepository
│           ├─ yaml_file.py             # YAML loader
│           └─ weaviate.py              # Vector store
│
├─ drivers/                             # LAYER 5: Entry Points
│  ├─ cli/
│  │  └─ commands.py                    # Typer CLI
│  └─ ui/
│     └─ streamlit/
│        └─ app.py                      # Streamlit UI
│
├─ tests/                               # Testing
│  ├─ features/
│  │  └─ test_localized_campaign.py    # Feature-level test
│  ├─ unit/
│  └─ integration/
│
├─ brands/                              # Brand configs (YAML)
│  └─ natural-suds-co.yaml
│
├─ briefs/                              # Campaign briefs (YAML)
│  └─ holiday-gift-2025-01.yaml
│
├─ out/                                 # Generated outputs
│  └─ assets/
│     ├─ lavender-soap/
│     │  ├─ en-US/ [1x1.png, 9x16.png, 16x9.png]
│     │  └─ es-US/ [1x1.png, 9x16.png, 16x9.png]
│     └─ citrus-shower-gel/
│        ├─ en-US/ [...]
│        └─ es-US/ [...]
│
├─ requirements.txt                     # Dependencies
├─ docker-compose.yaml                  # MinIO + Weaviate
├─ Makefile                             # Common commands
└─ README.md                            # Setup instructions
```

---

## Key Architectural Decisions

### Decision 1: Pragmatic Clean Architecture
- **Chosen:** Pragmatic CA (Clean Architecture lite)
- **Rationale:** Balances architectural sophistication with 6-8 hour time budget
- **Impact:** Clear layer boundaries, testable, extensible for Task 3

### Decision 2: Dual AI Implementation (Fake + Real)
- **Chosen:** Fake adapter (default) + OpenAI + Claude adapters
- **Rationale:** Demo without API keys, shows adapter pattern mastery
- **Impact:** Easy substitution, testability, interview-friendly

### Decision 3: CLI + UI from Day 1
- **Chosen:** Both CLI (Typer) and UI (Streamlit) drivers
- **Rationale:** Lean-Clean principle, stakeholder empathy
- **Impact:** Automation + visual demo capabilities

### Decision 4: YAML-Driven Configuration
- **Chosen:** Campaign briefs and brand configs in YAML
- **Rationale:** Human-readable, methodology alignment
- **Impact:** Easy hand-crafting, version control friendly

### Decision 5: Adapter Interfaces for Evolution
- **Chosen:** Protocol classes for all external dependencies
- **Rationale:** Easy evolution (local → cloud, fake → real)
- **Impact:** Production-ready path, demonstrates DIP

---

## Extension Points for Task 3 (Agentic System)

```
┌─────────────────────────────────────────────────────────────────────────┐
│                    AGENTIC SYSTEM INTEGRATION                           │
└─────────────────────────────────────────────────────────────────────────┘

Current Architecture (Task 2):
    Use Cases → Entities → Adapters

Agentic Extension (Task 3):
    Use Cases → Entities → Adapters
         │          │
         ▼          ▼
    Monitoring   Alerting
         │          │
         └────┬─────┘
              ▼
         Model Context Protocol
              ↓
         LLM-Generated Stakeholder Communication
              ↓
         Email / Slack / Dashboard Alerts

Extension Points:
1. Orchestrator logs → Monitoring system ingests
2. Alert entity → Triggers Model Context Protocol
3. Model Context Protocol → Claude API generates human-readable message
4. Email adapter → Sends stakeholder notifications
```

---

## Presentation Visual Conversion

This textual architecture should be converted to visual format using:
- Draw.io / Lucidchart for layered architecture diagram
- Mermaid for data flow diagrams
- PowerPoint/Keynote for presentation-ready slides

**Target Slide:** Presentation Slide 4 (Architecture Diagram)

**Key Messages:**
1. Clean separation of concerns (5 layers)
2. Dependency Inversion (outer layers depend on inner)
3. Adapter pattern for AI service substitution
4. Testability via fakes and interfaces
5. Production evolution path via adapters

---

## References

**Lean-Clean Methodology:**
- `/docs/framework-folder-structures.md` - Pragmatic CA reference
- `/docs/lean-clean-axioms.md` - Core architectural principles

**Session Decisions:**
- `_decisions-needed.md` - All 13 architectural decisions resolved
- `entities.md` - Domain model definitions
- `use-cases.md` - Business logic specifications
- `function-composition.md` - Pipeline flow and function composition

**Clean Architecture Principles:**
- Dependency Rule: Dependencies point inward
- Dependency Inversion: Use Cases depend on interfaces, not implementations
- Single Responsibility: Each layer has one clear purpose
- Open/Closed: Open for extension (new adapters), closed for modification (Use Cases)

---

**Status:** ✅ Ready for visual conversion and presentation integration
