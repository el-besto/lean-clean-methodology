# Campaign Generator: Decisions Needed

> **Template Version:** 1.0 (2025-10-13)
>
> **Related Documents:**
> - `_agent-understanding.md` - Complete context and requirements analysis
> - `_scenario-and-deliverables.pdf` - FDE take-home exercise requirements

---

**Status:** ✅ 13 of 13 Resolved | 0 Awaiting Discussion

This document captures the key architectural decisions for the Campaign Generator PoC (FDE Take-Home Exercise). These decisions must be resolved before creating architecture diagrams, entity definitions, and session files.

**Context:** Global consumer goods company needs creative automation pipeline for hundreds of localized social ad campaigns monthly. Time budget: 6-8 hours total for Tasks 1-3.

---

## Decision 1: PoC Architecture Type ⚠️ CRITICAL

**What I did:**
- Not yet started - decision identified during requirements analysis

**Context:**
From `_agent-understanding.md`, we have three PoC patterns from Lean-Clean methodology:
1. **Steel Thread:** Minimal structure, single use case, procedural code
2. **Pragmatic CA:** Clean Architecture lite with entities, use cases, adapters, infrastructure
3. **Full CA:** Complete Clean Architecture with all layers and patterns

PDF requirements suggest evolution path:
- Minimum requirements (Task 2) are simple enough for Steel Thread
- Nice-to-have features (brand compliance, legal checks) suggest extensibility
- Task 3 (agentic system) requires observability hooks
- Multiple stakeholders (Creative Lead, Ad Ops, IT, Legal) suggest clear boundaries

**Question for you:**
Which PoC architecture pattern should we use given the 6-8 hour constraint?

- [ ] **Option A: Steel Thread** (simplest, fastest)
  - Single-file or minimal structure
  - Procedural/functional code
  - ✅ Pro: Fastest to implement (fits 6-8 hour budget easily)
  - ✅ Pro: Minimal overhead, direct implementation
  - ❌ Con: Hard to extend with Task 3 monitoring/observability
  - ❌ Con: Doesn't demonstrate architectural thinking for interview
  - ❌ Con: Doesn't support "nice to have" features well

- [x] **Option B: Pragmatic CA** (balanced, recommended)
  - Lean-Clean Pragmatic CA structure (entities, use cases, adapters, infrastructure)
  - Clear layer boundaries without full CA overhead
  - ✅ Pro: Demonstrates architectural sophistication
  - ✅ Pro: Easy to add observability/monitoring for Task 3
  - ✅ Pro: Supports evolution (brand compliance, legal checks)
  - ✅ Pro: Shows understanding of Clean Architecture principles
  - ❌ Con: More initial setup than Steel Thread
  - ❌ Con: Need to manage dependencies and layer boundaries

- [ ] **Option C: Full CA** (most complete, highest overhead)
  - Complete Clean Architecture with all layers
  - ✅ Pro: Maximum flexibility and extensibility
  - ❌ Con: Too much overhead for 6-8 hour constraint
  - ❌ Con: Over-engineering for PoC scope
  - ❌ Con: May sacrifice working features for structure

**My recommendation:** Option B (Pragmatic CA)

**Reasoning:**
- Strikes balance between demonstrating architectural thinking and meeting time budget
- Task 3 agentic design requires observability hooks → easier with infrastructure layer
- Multiple stakeholders in requirements → benefits from clear layer boundaries
- Shows understanding of Clean Architecture without over-engineering
- Supports "nice to have" features as time permits

**Your decision:** ✅ **Option B: Pragmatic CA**

**Rationale:**
Agreed - demonstrates architectural sophistication while staying within time budget. Perfect for interview context.

**Impact:** ⚠️ CRITICAL - Determines all folder structure, entity definitions, and implementation approach

**Status:** ✅ RESOLVED

---

## Decision 2: Storage Strategy

**What I did:**
- Not yet started - decision identified during requirements analysis

**Context:**
PDF states: "Storage to save generated or transient assets (Can be Azure, AWS or Dropbox)"
Task 2 requirements: "Accept input assets (can be in a local folder or mock storage)"

Clean Architecture principle: External dependencies should be behind adapters.

**Question for you:**
How should we handle storage in the PoC?

- [ ] **Option A: Local Filesystem Only** (simplest)
  - All storage operations use local folders
  - ✅ Pro: Zero external dependencies, runs anywhere
  - ✅ Pro: Fastest to implement
  - ✅ Pro: Meets "local folder" requirement explicitly
  - ❌ Con: Doesn't demonstrate cloud integration knowledge
  - ❌ Con: Less realistic for production scenario

- [x] **Option B: Local with Adapter Interface** (demonstrates design)
  - Implement local filesystem storage behind `IStorageAdapter` interface
  - Create stub/placeholder for S3StorageAdapter (interface only, no implementation)
  - ✅ Pro: Shows adapter pattern understanding
  - ✅ Pro: Local storage works, cloud is documented extension
  - ✅ Pro: Demonstrates substitutability principle
  - ❌ Con: Slightly more code than Option A
  - ❌ Con: S3 adapter is just a placeholder

- [ ] **Option C: Mock S3 Storage** (cloud simulation)
  - Implement mock S3 that uses local filesystem but simulates cloud API
  - ✅ Pro: Demonstrates cloud storage knowledge
  - ✅ Pro: More realistic production preview
  - ❌ Con: More complexity without real benefit
  - ❌ Con: Adds testing/validation overhead

**My recommendation:** Option B (Local with Adapter Interface)

**Reasoning:**
- Meets PDF requirement for local execution
- Demonstrates architectural thinking (adapter pattern)
- README can document S3 extension as "next step"
- Fits interview context (show design skill without over-engineering)

**Your decision:** ✅ **Option B: Local with Adapter Interface**

**Rationale:**
I already have minio and weaviate dockerfile for us to use. Can easily incorporate with adapter as described.

**Impact:** HIGH - Affects infrastructure layer design and README documentation

**Status:** ✅ RESOLVED

---

## Decision 3: GenAI Image Provider

**What I did:**
- Not yet started - decision identified during requirements analysis

**Context:**
PDF states: "GenAI: Best-fit APIs available for generating hero images, resized and localized variations."

Common options:
- OpenAI DALL-E 3 (via OpenAI API)
- Stable Diffusion (via Stability AI API or local)
- Midjourney (via API, if available)
- Mock/Fake generator (for testing)

User's ideation mentions Claude Vision for brand understanding but doesn't specify image generation.

**Question for you:**
Which GenAI image generation API should we use?

- [ ] **Option A: OpenAI DALL-E 3** (recommended)
  - Use OpenAI API for image generation
  - ✅ Pro: Well-documented, stable API
  - ✅ Pro: Good quality, consistent results
  - ✅ Pro: Fits with Claude integration (same ecosystem thinking)
  - ❌ Con: Requires OpenAI API key
  - ❌ Con: Pay-per-use (interview context acceptable)

- [ ] **Option B: Stable Diffusion** (open source)
  - Use Stability AI API or local Stable Diffusion
  - ✅ Pro: Open source option
  - ✅ Pro: Potentially lower cost
  - ❌ Con: More complex setup for local
  - ❌ Con: API quality/stability varies

- [ ] **Option C: Fake/Mock Generator** (no real GenAI)
  - Create placeholder images or use stock images
  - Return mock results to simulate GenAI
  - ✅ Pro: No API dependencies, no cost
  - ✅ Pro: Fast, deterministic
  - ❌ Con: Doesn't demonstrate actual GenAI integration
  - ❌ Con: Less impressive for interview

- [ ] **Option D: Dual Implementation** (fake + real)
  - Implement both fake adapter (default) and real OpenAI adapter
  - User can switch via config
  - ✅ Pro: Works without API key (fake mode)
  - ✅ Pro: Shows real integration when API available
  - ✅ Pro: Demonstrates adapter pattern perfectly
  - ❌ Con: More implementation work

**My recommendation:** Option D (Dual Implementation with Fake + OpenAI)

**Reasoning:**
- Fake adapter allows demo without API setup
- Real OpenAI adapter shows actual GenAI integration
- Perfect use case for adapter pattern demonstration
- README can show both modes
- Interviewers can run fake mode without API key

**Your decision:** ✅ **Option D: Dual Implementation** (+ multiple AI adapters)

**Rationale:**
I have API keys for OpenAI and Claude.
- OpenAI image API for initial generation and placing text over images
- Claude Vision API for brand summary generation
- Claude for multilingual portion
- Fake adapters for demo without API keys

**Impact:** HIGH - Affects adapter design, README setup instructions, demo capabilities

**Status:** ✅ RESOLVED

---

## Decision 4: Campaign Brief Input Format

**What I did:**
- Not yet started - decision identified during requirements analysis

**Context:**
PDF states: "Accept a campaign brief (in JSON, YAML, or other reasonable format)"

Your ideation suggests YAML for Lean-Clean methodology alignment (YAML-driven development).

**Question for you:**
Which format for campaign brief input?

- [ ] **Option A: YAML** (methodology alignment)
  - Campaign brief as YAML file
  - ✅ Pro: Aligns with Lean-Clean YAML-driven approach
  - ✅ Pro: Human-readable, comment-friendly
  - ✅ Pro: Common in config/pipeline definitions
  - ❌ Con: Requires YAML parser library

- [ ] **Option B: JSON** (simpler parsing)
  - Campaign brief as JSON file
  - ✅ Pro: Native Python support, no extra library
  - ✅ Pro: Widely understood format
  - ❌ Con: Less human-friendly than YAML
  - ❌ Con: No comment support

- [ ] **Option C: Both YAML and JSON** (flexible)
  - Support both formats, auto-detect from file extension
  - ✅ Pro: Maximum flexibility
  - ❌ Con: More code, more testing
  - ❌ Con: Overkill for PoC

**My recommendation:** Option A (YAML)

**Reasoning:**
- Aligns with Lean-Clean methodology YAML-driven development
- More pleasant for hand-crafting example briefs
- PyYAML is standard Python library
- Can show YAML structure in README clearly

**Your decision:** ✅ **Option A: YAML**

**Rationale:**
[Your reasoning here]

**Impact:** MEDIUM - Affects schema design and README examples

**Status:** ✅ RESOLVED

---

## Decision 5: Brand Guidelines Representation

**What I did:**
- Not yet started - decision identified during requirements analysis

**Context:**
Your ideation includes `BrandSummary` entity with properties like `colors`, `description`, `brand_id`.

PDF business goals emphasize: "Ensure brand consistency: Maintain global brand guidelines and voice across all markets and languages."

Options for representing brand:
1. Entity only (in-memory during execution)
2. Entity + persisted file (JSON/YAML brand config)
3. Entity + vector store (Weaviate search, from your ideation)

**Question for you:**
How should we represent brand guidelines in the PoC?

- [ ] **Option A: Brand as YAML Config File** (simple, explicit)
  - `brands/brand-{id}.yaml` with colors, description, guidelines
  - Campaign brief references brand by ID
  - Load brand config at runtime
  - ✅ Pro: Simple, explicit, human-editable
  - ✅ Pro: Easy to version control
  - ✅ Pro: Clear separation of brand from campaign
  - ❌ Con: No "search similar brands" capability
  - ❌ Con: Manual brand ID matching

- [ ] **Option B: Brand Embedded in Brief** (simplest)
  - Brand properties (colors, description) directly in campaign brief
  - No separate brand files
  - ✅ Pro: Minimal file management
  - ✅ Pro: Self-contained campaign brief
  - ❌ Con: Duplication across campaigns
  - ❌ Con: Doesn't demonstrate brand reuse
  - ❌ Con: Misses "brand consistency" emphasis

- [ ] **Option C: Brand Store + Search** (complex, from ideation)
  - Separate brand definitions
  - Use vector store (Weaviate) or simple similarity for search
  - Your `understand_brand()` and `search_similar_brands()` functions
  - ✅ Pro: Demonstrates AI-driven brand matching
  - ✅ Pro: Addresses brand consistency pain point directly
  - ❌ Con: Significant complexity for PoC
  - ❌ Con: Weaviate dependency or substantial search logic

**My recommendation:** Option A (Brand as YAML Config)

**Reasoning:**
- Demonstrates brand reuse and consistency
- Simple enough for 6-8 hour constraint
- README can document vector search as "Task 3 extension"
- Supports multiple campaigns referencing same brand
- Shows good architecture (separation of concerns)

**Your decision:** ✅ **Option A: Brand as YAML Config** (with adapter for Weaviate evolution)

**Rationale:**
A. But access via adapter so that we can hook to local Weaviate easily. Plus to look up the brand after Claude defines a summary from assets, we will need to look up similar brands in vector store. So, we may end up quickly moving beyond the local file approach.

**Impact:** HIGH - Affects entity design, file structure, and brand consistency validation

**Status:** ✅ RESOLVED

---

## Decision 6: Locale Handling

**What I did:**
- Not yet started - decision identified during requirements analysis

**Context:**
Your ideation shows `Product` with potential `locale` property, but questions whether locale belongs in Product or CampaignBrief.

PDF requirements: "Display campaign message on the final campaign posts (English at least, localized is a plus)."

Business goal: "Maximize relevance & personalization: Adapt messaging, offers, and creative to local cultures, trends, and consumer preferences."

**Question for you:**
Where should locale/localization be modeled?

- [ ] **Option A: Locale in CampaignBrief** (campaign-level)
  - `CampaignBrief.target_locales: List[str]` (e.g., ["en-US", "es-MX"])
  - Products are locale-agnostic
  - Campaign generates variants per locale
  - ✅ Pro: Models "one campaign, many locales" naturally
  - ✅ Pro: Product definitions reusable across locales
  - ✅ Pro: Matches business goal (localized campaigns)
  - ❌ Con: Product names might need localization too

- [ ] **Option B: Locale in Product** (product-level)
  - `Product.locale: str`
  - Products are locale-specific
  - ✅ Pro: Product names can be localized
  - ❌ Con: Forces product duplication per locale
  - ❌ Con: Doesn't match "campaign message localization" requirement
  - ❌ Con: Awkward for multi-locale campaigns

- [ ] **Option C: Both (complex)** (dual-level localization)
  - Locale at both campaign and product level
  - ✅ Pro: Maximum flexibility
  - ❌ Con: Over-engineering for PoC
  - ❌ Con: Confusing model

**My recommendation:** Option A (Locale in CampaignBrief)

**Reasoning:**
- Matches PDF requirement: localize campaign message
- Supports "localized campaigns" business goal
- Simpler model: one product, many locale variants
- Can still localize product names in campaign context if needed
- Bonus feature scope: implement English + 1 locale

**Your decision:** ✅ **Option A: Locale in CampaignBrief**

**Rationale:**
Campaign-level localization makes most sense for "one campaign, many locales" model.

**Impact:** HIGH - Affects CampaignBrief schema, entity design, and localization pipeline

**Status:** ✅ RESOLVED

---

## Decision 7: Localization Scope for PoC

**What I did:**
- Not yet started - decision identified during requirements analysis

**Context:**
PDF requirement: "Display campaign message on the final campaign posts (English at least, localized is a plus)."

Your ideation includes multilingual Claude support and `localize_campaign_slogans()` function.

Time budget: 6-8 hours total.

**Question for you:**
What localization scope for the PoC?

- [ ] **Option A: English Only** (meets minimum requirement)
  - Campaign messages only in English
  - ✅ Pro: Meets minimum PDF requirement
  - ✅ Pro: Simpler implementation
  - ✅ Pro: More time for core features
  - ❌ Con: Misses "bonus points" opportunity
  - ❌ Con: Doesn't demonstrate multilingual capability

- [ ] **Option B: English + 1 Locale** (bonus, recommended)
  - Implement English + Spanish (or another locale)
  - Use Claude multilingual API for translation
  - ✅ Pro: Demonstrates multilingual capability (bonus points)
  - ✅ Pro: Shows Claude API integration beyond image gen
  - ✅ Pro: Addresses "localized campaigns" business goal
  - ❌ Con: Additional implementation time
  - ❌ Con: Requires locale parameter handling

- [ ] **Option C: Configurable Multi-Locale** (over-engineering)
  - Support arbitrary number of locales
  - ✅ Pro: Maximum flexibility
  - ❌ Con: Over-engineering for PoC
  - ❌ Con: Testing/validation overhead

**My recommendation:** Option B (English + 1 Locale)

**Reasoning:**
- "Bonus points" opportunity worth pursuing
- Demonstrates AI capability beyond image generation
- Fits interview context (show range of skills)
- Spanish is natural choice (US market + Latin America)
- Can implement incrementally: start English, add locale if time permits

**Your decision:** ✅ **Option B: English + 1 Locale** (English and Spanish US)

**Rationale:**
Bonus points opportunity worth pursuing for interview. Spanish natural choice for US market.

**Impact:** MEDIUM - Affects use case design and implementation time allocation

**Status:** ✅ RESOLVED

---

## Decision 8: Asset Reuse Strategy

**What I did:**
- Not yet started - decision identified during requirements analysis

**Context:**
PDF requirement: "Accept input assets (can be in a local folder or mock storage) and reuse them when available. When assets are missing, generate new ones using a GenAI image model."

Your ideation mentions vector similarity search in Weaviate for finding existing images.

**Question for you:**
How should we detect and reuse existing assets?

- [ ] **Option A: Filename Convention Matching** (simple)
  - Assets named like `{product_name}_{aspect_ratio}.png`
  - Check if file exists before generating
  - ✅ Pro: Simple, fast, deterministic
  - ✅ Pro: No dependencies
  - ✅ Pro: Easy to test and verify
  - ❌ Con: Brittle (typos break matching)
  - ❌ Con: Doesn't demonstrate AI/search capability

- [ ] **Option B: Metadata JSON Matching** (structured)
  - Assets accompanied by `metadata.json` with product, aspect, tags
  - Match based on metadata attributes
  - ✅ Pro: More flexible than filename
  - ✅ Pro: Supports richer matching (tags, attributes)
  - ❌ Con: More file management
  - ❌ Con: Still not intelligent matching

- [ ] **Option C: Vector Similarity Search** (AI-driven, from ideation)
  - Use Weaviate or similar to find visually/semantically similar images
  - ✅ Pro: Demonstrates AI-driven asset management
  - ✅ Pro: Intelligent reuse (finds similar even if not exact match)
  - ❌ Con: Major complexity (Weaviate setup, embeddings)
  - ❌ Con: Likely exceeds 6-8 hour budget

**My recommendation:** Option A (Filename Convention) for PoC, document Option C for Task 3

**Reasoning:**
- Meets PDF requirement ("reuse when available")
- Simple, fast implementation
- README can document vector search as "production enhancement"
- Task 3 agentic design can propose Weaviate integration
- Demonstrates pragmatic scoping for time constraint

**Your decision:** ✅ **Option C: Vector Similarity Search** (deferred to session-02)

**Rationale:**
Stored in structured "out/assets/" dir and in MinIO assets to mimic S3. We can defer implementation details for session-02 since that is the session that will actually implement code.

**Impact:** MEDIUM - Affects asset management logic and file organization

**Status:** ✅ RESOLVED (implementation details deferred)

---

## Decision 9: HITL Approval Workflows in PoC

**What I did:**
- Not yet started - decision identified during requirements analysis

**Context:**
Your ideation includes `accept_brand_choice()` and `accept_campaign_images()` HITL approval functions.

PDF pain point: "Slow approval cycles: Bottlenecks in review/approval with multiple stakeholders in each region and market."

Task 3 explicitly addresses monitoring and alerting but doesn't require implementation in Task 2 PoC.

**Question for you:**
Should the PoC implement interactive HITL approval, or just simulate/document it?

- [ ] **Option A: No HITL in PoC** (pure automation)
  - Generate assets end-to-end without human approval
  - ✅ Pro: Simplest implementation
  - ✅ Pro: Focuses on automation (PDF objective)
  - ❌ Con: Misses approval workflow pain point
  - ❌ Con: Less realistic

- [ ] **Option B: Simulated Approval** (logged checkpoints)
  - Code logs "Approval checkpoint: Brand choice" but continues automatically
  - README documents where approvals would happen
  - ✅ Pro: Shows awareness of approval workflow
  - ✅ Pro: Minimal implementation overhead
  - ✅ Pro: Clear extension point for Task 3
  - ❌ Con: Not interactive

- [ ] **Option C: CLI Interactive Approval** (real HITL)
  - CLI prompts user: "Approve brand? (y/n)" at checkpoints
  - Records approval decisions
  - ✅ Pro: Real HITL workflow demonstration
  - ✅ Pro: Engaging demo experience
  - ❌ Con: More implementation time
  - ❌ Con: Complicates automation testing

**My recommendation:** Option B (Simulated Approval with Logging)

**Reasoning:**
- Acknowledges approval workflow without over-engineering
- Logs create audit trail (good for Task 3 monitoring design)
- README clearly documents HITL extension
- Keeps PoC automatable for demo
- Shows architectural thinking

**Your decision:** ✅ **Option B: Simulated Approval** (logged checkpoints)

**Rationale:**
Time constraints - simulated approval with logging provides foundation for Task 3 without implementation overhead.

**Impact:** MEDIUM - Affects use case design and user experience

**Status:** ✅ RESOLVED

---

## Decision 10: "Nice to Have" Features Priority

**What I did:**
- Not yet started - decision identified during requirements analysis

**Context:**
PDF lists "Nice to Have (optional for bonus points)":
- Brand compliance checks (e.g., presence of logo, use of brand colors)
- Simple legal content checks (e.g., flagging prohibited words)
- Logging or reporting of results

Given 6-8 hour total budget across all 3 tasks, need to prioritize.

**Question for you:**
Which "nice to have" features should we target, if time permits?

- [ ] **Option A: Logging/Reporting Only** (foundation)
  - Implement structured logging throughout pipeline
  - Generate summary report of campaign generation run
  - ✅ Pro: Foundational for Task 3 (monitoring/alerting)
  - ✅ Pro: Useful for debugging during development
  - ✅ Pro: Quick to implement
  - ❌ Con: Doesn't show compliance/validation sophistication

- [ ] **Option B: Legal Checks Only** (simple validation)
  - Implement prohibited words check (simple list matching)
  - Flag campaigns with prohibited words
  - ✅ Pro: Demonstrates validation layer
  - ✅ Pro: Simple to implement (string matching)
  - ✅ Pro: Addresses compliance pain point
  - ❌ Con: Doesn't address brand consistency

- [ ] **Option C: Brand Compliance Only** (sophisticated)
  - Check for logo presence in generated images
  - Validate brand color usage
  - ✅ Pro: Sophisticated feature (image analysis)
  - ✅ Pro: Addresses brand consistency goal directly
  - ❌ Con: Complex (requires image processing)
  - ❌ Con: May exceed time budget

- [x] **Option D: Logging + Legal Checks** (balanced, recommended)
  - Implement both logging and prohibited words check
  - ✅ Pro: Covers Task 3 foundation + validation demonstration
  - ✅ Pro: Both are relatively quick to implement
  - ✅ Pro: Shows breadth (observability + compliance)
  - ❌ Con: Skips brand compliance (most complex feature)

**My recommendation:** Option D (Logging + Legal Checks)

**Reasoning:**
- Logging is essential for Task 3 agentic design
- Legal checks are simple but demonstrate validation thinking
- Both achievable within time budget
- Brand compliance can be documented as "future enhancement"
- Shows pragmatic prioritization skill

**Your decision:** ✅ **Option D: Logging + Legal Checks**

**Rationale:**
Time constraints. Plus we can stub the methods and put them in place in the orchestrator without performing actual implementation.

**Impact:** MEDIUM - Affects time allocation and feature scope

**Status:** ✅ RESOLVED

---

## Decision 11: CLI vs Web UI

**What I did:**
- Not yet started - decision identified during requirements analysis

**Context:**
PDF requirement: "Run locally (command-line tool or simple local app; your choice of language/framework)."

Your ideation mentions Streamlit for UI.

Session-03a reference shows both CLI + UI from day 1 (Lean-Clean principle).

**Question for you:**
Should the PoC have CLI only, or CLI + simple UI?

- [ ] **Option A: CLI Only** (fastest)
  - Command-line tool: `python main.py --brief campaign.yaml`
  - ✅ Pro: Fastest to implement
  - ✅ Pro: Easy to script and automate
  - ✅ Pro: Meets PDF requirement directly
  - ❌ Con: Less impressive demo
  - ❌ Con: Misses Lean-Clean "CLI + UI always" principle

- [ ] **Option B: CLI + Streamlit UI** (Lean-Clean, recommended)
  - CLI for automation, Streamlit for visual demo
  - UI shows generated images, campaign details
  - ✅ Pro: Visual demo more engaging for interview
  - ✅ Pro: Follows Lean-Clean Pragmatic CA pattern
  - ✅ Pro: Shows full-stack thinking
  - ❌ Con: Additional implementation time
  - ❌ Con: More dependencies

- [ ] **Option C: Web UI Only** (UI-first)
  - No CLI, only Streamlit/web interface
  - ✅ Pro: User-friendly demo
  - ❌ Con: Harder to automate
  - ❌ Con: Violates Lean-Clean "always both" principle
  - ❌ Con: PDF says "command-line tool" is an option

**My recommendation:** Option B (CLI + Streamlit UI)

**Reasoning:**
- Aligns with Lean-Clean methodology (drivers layer with both)
- CLI enables demo recording (required deliverable)
- Streamlit UI makes presentation more engaging
- Streamlit is very fast to build (minimal time overhead)
- Shows stakeholder empathy (different interfaces for different users)

**Your decision:** ✅ **Option B: CLI + Streamlit UI**

**Rationale:**
Aligns with Lean-Clean methodology, CLI enables demo recording, Streamlit makes presentation engaging.

**Impact:** HIGH - Affects drivers layer design, demo approach, and time allocation

**Status:** ✅ RESOLVED

---

## Decision 12: Test Strategy for PoC

**What I did:**
- Not yet started - decision identified during requirements analysis

**Context:**
PDF doesn't explicitly require tests but states: "Please ensure that your solution reflects thoughtful design choices and demonstrates a clear understanding of the code."

Lean-Clean methodology emphasizes Outside-In TDD with acceptance tests.

6-8 hour time budget is tight.

**Question for you:**
What test strategy for the PoC?

- [ ] **Option A: No Tests** (time-focused)
  - Focus all time on implementation
  - README documents design choices
  - ✅ Pro: Maximum time for features
  - ❌ Con: No demonstration of testing discipline
  - ❌ Con: Risky for interview (code quality unclear)

- [ ] **Option B: Example Tests Only** (selective)
  - Write 2-3 example tests demonstrating testing approach
  - One unit test (e.g., entity validation)
  - One acceptance test (e.g., end-to-end campaign generation)
  - ✅ Pro: Shows testing capability without full coverage
  - ✅ Pro: Documents testing approach in code
  - ✅ Pro: Quick to implement (examples only)
  - ❌ Con: Not comprehensive

- [ ] **Option C: Core Coverage** (disciplined)
  - Tests for core use cases and entities
  - Focus on business logic, skip adapter tests
  - ✅ Pro: Demonstrates testing discipline
  - ✅ Pro: Makes refactoring safer during development
  - ❌ Con: Significant time investment
  - ❌ Con: May sacrifice features for tests

**My recommendation:** Option B (Example Tests Only)

**Reasoning:**
- Shows testing capability without excessive time
- Demonstrates thoughtful design (PDF criterion)
- README can reference tests as examples
- Fits interview context (prove competence, not production coverage)
- Example acceptance test supports demo

**Your decision:** ✅ **Custom: One Feature-Level Test**

**Rationale:**
We will write one feature-level test similar to that from the blog post. No other testing required - focused on demonstrating testing capability without time overhead.

**Impact:** MEDIUM - Affects time allocation and code confidence

**Status:** ✅ RESOLVED

---

## Decision 13: Project Language & Tooling

**What I did:**
- Not yet started - decision identified during requirements analysis

**Context:**
PDF states: "your choice of language/framework"

Lean-Clean methodology examples use Python with uv for dependency management.

Your ideation uses Python class syntax.

**Question for you:**
Confirm language and tooling choices?

- [ ] **Option A: Python + uv** (Lean-Clean standard)
  - Python 3.11+
  - uv for dependency management
  - ✅ Pro: Aligns with Lean-Clean methodology
  - ✅ Pro: Fast, modern Python tooling
  - ✅ Pro: Matches your ideation code examples
  - ❌ Con: Interviewers may not have uv installed

- [ ] **Option B: Python + pip/requirements.txt** (maximum compatibility)
  - Python 3.11+
  - Standard pip and requirements.txt
  - ✅ Pro: Universal compatibility
  - ✅ Pro: Easier for interviewers to run
  - ❌ Con: Slower dependency resolution than uv
  - ❌ Con: Doesn't match Lean-Clean methodology

- [ ] **Option C: Python + Poetry** (modern alternative)
  - Python 3.11+
  - Poetry for dependency management
  - ✅ Pro: Modern, popular tooling
  - ✅ Pro: Good dependency resolution
  - ❌ Con: Another tool to install
  - ❌ Con: Doesn't match Lean-Clean methodology

**My recommendation:** Option B (Python + pip/requirements.txt)

**Reasoning:**
- Interview context: ease of running > tooling sophistication
- README setup instructions simpler
- Everyone has pip, not everyone has uv
- Can mention uv as "production preference" in README
- Python is clear choice (matches ideation, Lean-Clean examples, AI/ML ecosystem)

**Your decision:** ✅ **Option B: Python + pip/requirements.txt**

**Rationale:**
Maximum compatibility for interview context - easier for interviewers to run without additional tooling.

**Impact:** LOW - Affects setup documentation but not architecture

**Status:** ✅ RESOLVED

---

## Summary of Decisions

| # | Decision | Priority | Status |
|---|----------|----------|--------|
| 1 | PoC Architecture Type | ⚠️ CRITICAL | ✅ RESOLVED |
| 2 | Storage Strategy | HIGH | ✅ RESOLVED |
| 3 | GenAI Image Provider | HIGH | ✅ RESOLVED |
| 4 | Campaign Brief Input Format | MEDIUM | ✅ RESOLVED |
| 5 | Brand Guidelines Representation | HIGH | ✅ RESOLVED |
| 6 | Locale Handling | HIGH | ✅ RESOLVED |
| 7 | Localization Scope for PoC | MEDIUM | ✅ RESOLVED |
| 8 | Asset Reuse Strategy | MEDIUM | ✅ RESOLVED |
| 9 | HITL Approval Workflows in PoC | MEDIUM | ✅ RESOLVED |
| 10 | "Nice to Have" Features Priority | MEDIUM | ✅ RESOLVED |
| 11 | CLI vs Web UI | HIGH | ✅ RESOLVED |
| 12 | Test Strategy for PoC | MEDIUM | ✅ RESOLVED |
| 13 | Project Language & Tooling | LOW | ✅ RESOLVED |

**Priority Levels:**
- ⚠️ **CRITICAL**: Affects fundamental architecture, blocking, or has cascading impact
- **HIGH**: Significant impact on implementation, affects multiple components
- **MEDIUM**: Important but localized impact
- **LOW**: Minor impact, easily changed later

---

## Next Steps

**Completed:**
- ✅ Read and analyzed FDE take-home exercise PDF
- ✅ Created comprehensive understanding document
- ✅ Identified 13 architectural decisions requiring resolution
- ✅ Mapped decisions to Lean-Clean methodology patterns

**All Decisions Resolved (13/13):**
1. ✅ **Decision 1: PoC Architecture Type** → **Pragmatic CA**
2. ✅ **Decision 2: Storage Strategy** → **Local with Adapter Interface** (MinIO + Weaviate dockerfiles available)
3. ✅ **Decision 3: GenAI Image Provider** → **Dual Implementation** (Fake + OpenAI + Claude adapters)
4. ✅ **Decision 4: Campaign Brief Input Format** → **YAML**
5. ✅ **Decision 5: Brand Guidelines Representation** → **YAML Config with Adapter** (evolution to Weaviate)
6. ✅ **Decision 6: Locale Handling** → **Locale in CampaignBrief** (campaign-level)
7. ✅ **Decision 7: Localization Scope for PoC** → **English + Spanish (US)**
8. ✅ **Decision 8: Asset Reuse Strategy** → **Vector Similarity Search** (details deferred to session-02)
9. ✅ **Decision 9: HITL Approval Workflows** → **Simulated Approval with Logging**
10. ✅ **Decision 10: "Nice to Have" Features** → **Logging + Legal Checks** (stub methods in orchestrator)
11. ✅ **Decision 11: CLI vs Web UI** → **CLI + Streamlit UI**
12. ✅ **Decision 12: Test Strategy** → **One Feature-Level Test** (blog post style)
13. ✅ **Decision 13: Language & Tooling** → **Python + pip/requirements.txt**

**Next: Create Deliverables**
1. Create `_scenario-context.md` (clean extraction from PDF)
2. Define entities in `entities.md` (based on Decisions 1, 5, 6)
3. Create `campaign-brief-schema.yaml` (based on Decisions 4, 6)
4. Define use cases and pipeline in `use-cases.md` + `function-composition.md`
5. Create `architecture-diagram.md` (based on Decision 1, 2, 3, 11)
6. Create `roadmap.md` (based on all scope/priority decisions)
7. Write session files: `session-01-architecture-roadmap.md`, `session-02-steel-thread-poc.md`
8. Create `presentation.md` (10-slide outline for 30-min presentation)
9. Commit all planning artifacts

**Status:** ✅ All decisions resolved - ready to create deliverables

---

## Notes

**Decision Discovery Process:**
- All 13 decisions identified upfront during requirements analysis
- Decisions derived from:
  - PDF requirements (Tasks 1-3)  
  - User's initial ideation and pseudocode
  - Lean-Clean methodology principles
  - 6-8 hour time budget constraint
  - Interview context (demonstrate skill vs production completeness)

**Dependencies Between Decisions:**
- Decision 1 (Architecture Type) is CRITICAL and blocks most other implementation decisions
- Decisions 5, 6 depend on Decision 1 (entity design depends on architecture)
- Decision 11 (CLI vs UI) depends on Decision 1 (drivers layer)
- Decision 10 (Nice-to-Have Priority) affects Decisions 7, 12 (scope vs testing trade-offs)

**Related Documents:**
- `_agent-understanding.md` - Complete context and requirements analysis
- `/docs/lean-clean-axioms.md` - Core architectural principles
- `/docs/framework-folder-structures.md` - Pragmatic CA reference structure
- `/plan/sessions/_session-03a.md` - Steel Thread PoC example
- `../lean-clean-blog/drafts/_wip-lean-clean-the-secret-sauce.md` - TestLocalizedCampaignFeature reference
