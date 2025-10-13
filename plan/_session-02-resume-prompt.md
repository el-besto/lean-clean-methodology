# Session 03: Architectural Decisions for Framework Folder Structures

## Context

I'm continuing Session 02 work on defining the **Framework (Component B)** for the Lean-Clean Methodology. Session 02 defined folder structures for three PoC types (Steel Thread, Pragmatic CA, Full CA) but we've identified **5 critical architectural decisions** that need resolution before the Framework is complete.

## What's Been Completed (Session 02)

✅ Created comprehensive Framework document: `/plan/_session-02-framework-folder-structures.md`
- Defined Pragmatic CA folder structure (3-5 day enterprise PoCs)
- Defined Steel Thread folder structure (1-2 day spikes)
- Defined Full CA folder structure (production-grade)
- Included complete feature example: Localized Campaign Generation
- Documented progressive refinement evolution path

✅ Created Core Axioms document: `/docs/lean-clean-axioms.md`
- 10 non-negotiable principles guiding methodology decisions
- **Key Axiom 2:** Orchestrator-Centric Architecture (NOT Controller)
- **Key Axiom 3:** Fakes are Production Code (in adapters/, not tests/)

✅ Resolved 7 of 12 architectural decisions:
1. ✅ **Terminology:** "Orchestrator" not "Controller"
2. ✅ **Folder depth:** Progressive (flat → moderate → deep)
3. ✅ **Document scope:** Keep all three PoC types in one comprehensive doc
4. ✅ **Example scenario:** Localized Campaign Generation (multi-stakeholder AI PoC)
5. ✅ **Code detail:** Detailed but incomplete (shows patterns, not runnable)
6. ✅ **Interface style:** Protocol (not ABC)
7. ✅ **Test structure:** acceptance/unit/integration layers

## What Needs Decision (5 Remaining)

After reviewing `/lean-clean-code/CLEAN_ARCHITECTURE_ANALYSIS.md`, we identified **5 critical architectural decisions** that impact folder structure but weren't addressed:

### 🚧 Decision 8: Fakes Location
**Question:** Where should fakes live?

**Current Framework:** `app/adapters/imagegen/fake_imagegen.py` (production code)

**Options:**
- **A:** In `adapters/` alongside real implementations (what framework shows)
- **B:** In `tests/fakes/` (test-only code)
- **C:** Separate `app/fakes/` folder (clear boundary)

**Key Consideration:** Axiom 3 says "Fakes are Production Code" - used in workshops, local dev, CI/CD, not just tests.

---

### 🚧 Decision 9: Drivers Layer ⚠️ HIGH PRIORITY
**Question:** Should we have an explicit `drivers/` layer?

**Current Framework:** No `drivers/` folder - `server.py`/`cli.py` at project root

**Options:**
- **A:** No `drivers/` (current framework - entry points at root)
- **B:** `drivers/rest/` with routers, schemas, dependencies (calibration-service pattern)
- **C:** `drivers/rest/controllers/` that call use cases directly (lean-clean pattern)

**Key Considerations:**
- **Calibration-service:** Drivers call Orchestrators (not use cases)
- **Lean-clean:** Drivers call use cases directly (skips orchestration layer)
- **Uncle Bob CA:** "Frameworks & Drivers" is outermost layer
- **Our methodology:** Uses "Orchestrator" terminology - should drivers call orchestrators?

**Impact:** This affects what the entry point code looks like and where framework-specific concerns live.

---

### 🚧 Decision 10: Gateways vs Repositories/Services Terminology
**Question:** How should we organize and name external integrations?

**Current Framework:** Generic `adapters/` folder with subfolders by integration type

**Options:**
- **A:** `adapters/` (generic - what framework shows)
  ```
  app/adapters/
  ├─ imagegen/
  ├─ storage/
  └─ events/
  ```

- **B:** `gateways/` (Uncle Bob CA terminology, lean-clean pattern)
  ```
  app/interface_adapters/gateways/
  ├─ openai_gateway.py
  ├─ s3_gateway.py
  └─ amplitude_gateway.py
  ```

- **C:** `repositories/` + `services/` (calibration-service pattern)
  ```
  app/adapters/
  ├─ repositories/      # Persistence (DB)
  └─ services/          # External APIs
  ```

- **D:** Separate Infrastructure layer (CLEAN_ARCHITECTURE_ANALYSIS synthesis)
  ```
  app/
  ├─ interface_adapters/gateways/  # External service clients
  └─ infrastructure/               # ORM, framework-specific
     ├─ orm_models/
     └─ repositories/
  ```

**Key Considerations:**
- Enterprise PoCs often have **multiple data stores** (relational, document, data warehouse, external APIs)
- Example: Person entity might aggregate data from profile DB, purchase history, CRM, etc.
- Our use case: Multi-agent RAG system integrating multiple enterprise systems

**Impact:** How we distinguish persistence from external services, whether we have an infrastructure layer separate from interface adapters.

---

### 🚧 Decision 11: DTO Layer Strategy
**Question:** How comprehensive should the DTO layer be?

**Current Framework:** Minimal DTOs - Request/Response at orchestrator level, domain Commands at use case level

**Options:**
- **A:** Minimal DTOs (what framework shows)
  ```python
  # Orchestrator level
  LocalizedCampaignRequest  # From API

  # Use case level
  GenerateCampaignCommand   # Domain object
  ```

- **B:** Comprehensive DTOs (calibration-service pattern)
  ```python
  # Use case folder: use_cases/generate_campaign/dtos.py
  GenerateCampaignInput     # Use case input DTO
  GenerateCampaignOutput    # Use case output DTO
  ```

- **C:** Commands/Results as domain tuples (lean-clean pattern)
  ```python
  SendMessageCmd = tuple[Messages, LlmParams]
  LlmResult = tuple[str, Usage]
  ```

**Key Considerations:**
- DTOs prevent domain leakage to API layer
- More DTOs = more conversion logic = slower PoC development
- This should scale with PoC type (Steel Thread minimal, Full CA comprehensive)

**Impact:** Use case interfaces, conversion logic in orchestrators, testability.

---

### 🚧 Decision 12: Ports Location and Naming
**Question:** Where should ports/interfaces live and how should they be organized?

**Current Framework:** `core/interfaces/` with generic interface names

**Options:**
- **A:** `core/interfaces/` (nikolovlazar pattern - what framework shows)
  ```
  app/core/
  ├─ entities/
  ├─ use_cases/
  └─ interfaces/
     ├─ image_generator.py  # IImageGenerator
     └─ storage.py          # IStorageAdapter
  ```

- **B:** `application/ports/` (Ports & Adapters terminology)
  ```
  app/application/
  ├─ use_cases/
  └─ ports/
     ├─ llm_port.py
     └─ storage_port.py
  ```

- **C:** `application/repositories/` + `services/` (calibration-service)
  ```
  app/application/
  ├─ use_cases/
  ├─ repositories/
  │  └─ campaign_repository.py
  └─ services/
     └─ image_generator_service.py
  ```

**Key Considerations:**
- "Ports" matches Hexagonal Architecture terminology (mentioned in Axiom 6: Hybrid Methodology)
- Distinguishing repositories from services may help organize complex enterprise integrations
- Consistency with whatever we choose for Decision 10 (gateways vs repositories/services)

**Impact:** Import paths, conceptual model (CA vs Hexagonal), how we talk about interfaces in workshops.

---

## Documents to Review

Please read these documents to inform your decisions:

1. **`/docs/lean-clean-axioms.md`** - Core non-negotiable principles
   - Axiom 2: Orchestrator-Centric Architecture
   - Axiom 3: Fakes are Production Code
   - Axiom 6: Hybrid Methodology (Not Pure CA)
   - Axiom 10: Consistency Enables Evolution

2. **`/plan/_session-02-framework-folder-structures.md`** - Current framework definition
   - Section 1: Pragmatic CA folder structure (lines 30-145)
   - Section 3: Steel Thread folder structure (lines 738-866)
   - Section 4: Full CA folder structure (lines 870-1227)
   - Section 5: Evolution path and migration checklists (lines 1230-1369)

3. **`/lean-clean-code/CLEAN_ARCHITECTURE_ANALYSIS.md`** - Architectural analysis
   - Section 1: Controller Responsibilities (lines 13-48)
   - Section 4: Drivers Layer (lines 118-164)
   - Section 5: Gateways vs Adapters Terminology (lines 168-185)
   - Synthesis Proposal (lines 244-428)

4. **`/plan/_session-02-decisions-needed.md`** - Decisions document with all options
   - Decisions 8-12 with detailed options and trade-offs (lines 362-636)

5. **`README.lean-clean.md`** - Methodology overview (if you need broader context)

## Visual References

If you need architectural diagrams:
- **nikolovlazar's CA diagram:** `/images/ca/clean-architecture-diagram-nikolovlazar.jpg`
- **Bob Martin's CA diagram:** `/images/ca/clean-architecture-diagram-bob-martin.jpg`

## Key Questions to Consider

As you review, consider:

1. **Progressive Refinement:** These decisions should support evolution from Steel Thread → Pragmatic CA → Full CA. Do the options scale appropriately?

2. **Multi-Stakeholder Workshops:** Tests and folder names are visible to stakeholders (Creative Lead, Ad Ops, IT, Legal). Does the terminology make sense to them?

3. **Enterprise Context:** Our target is AI/Gen-AI PoCs with multiple external integrations (LLMs, storage, analytics, data warehouses). Do the folder structures accommodate this complexity?

4. **Outside-In TDD:** Fakes must enable workshop demos without expensive API calls. Does the folder structure support this workflow?

5. **Hybrid Methodology:** We're not pure CA - we blend CA, Hexagonal Architecture, DDD, BDD. Are we being pragmatic or dogmatic?

## Your Task

For each of Decisions 8-12:

1. **Choose an option** (A, B, C, or D)
2. **Provide rationale** (why this choice supports the methodology goals)
3. **Consider evolution** (does this work across Steel Thread, Pragmatic CA, Full CA?)

Once you've made decisions, I will:
1. Update `/plan/_session-02-framework-folder-structures.md` with the resolved folder structures
2. Ensure consistency across all three PoC types
3. Update Session 02 summary document
4. Commit all Session 02 work

## Format Your Response

Please respond in this format for each decision:

```
**Decision X: [Name]**

My choice: Option [A/B/C/D]

Rationale:
- [Why this supports multi-stakeholder workshops]
- [How this enables Outside-In TDD]
- [How this scales across PoC types]
- [Any trade-offs I'm accepting]

Impact on folder structure:
- [Specific changes needed to framework document]
```

## Ready?

I'm ready to discuss these 5 architectural decisions and finalize the Framework folder structures for the Lean-Clean Methodology.
