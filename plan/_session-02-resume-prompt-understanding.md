# Session 02 Resume Prompt - Claude's Understanding

**Date**: 2025-10-13
**Context**: Preparing to resolve 5 remaining architectural decisions for Framework folder structures
**Mode**: Human-in-the-Loop collaborative decision-making

---

## Task Summary

Resolve 5 critical architectural decisions (Decisions 8-12) that impact Framework folder structures for three PoC types: Steel Thread, Pragmatic CA, Full CA.

**What's Already Decided** (Session 02, Decisions 1-7):
- ✅ Terminology: "Orchestrator" (not Controller)
- ✅ Folder depth: Progressive (flat → moderate → deep)
- ✅ Document scope: All three PoC types in one comprehensive doc
- ✅ Example scenario: Localized Campaign Generation
- ✅ Code detail: Detailed but incomplete (shows patterns)
- ✅ Interface style: Protocol (not ABC)
- ✅ Test structure: acceptance/unit/integration layers

**What's Been Decided** (Session 02, Decisions 8-10):
- ✅ Decision 8: Fakes in adapters/ (external services) + infrastructure/repositories/ (persistence)
- ✅ Decision 9: Drivers layer with CLI + UI always, FastAPI when needed
- ✅ Decision 10: Split adapters/ + infrastructure/repositories/, two-step evolution for ports

**✅ ALL DECISIONS RESOLVED:**
1. Decision 11: DTO Layer Strategy ✅ (Minimal DTOs with progressive evolution)
2. Decision 12: Ports Location/Naming ✅ (Resolved by Decision 10's two-step evolution)

**Status:** All 12 architectural decisions completed. Framework fully defined.

---

## Critical Constraints

### 1. Progressive Evolution with Minimal Refactoring

**Core Principle**: Folder structures must support **additive growth**, not transformative refactoring.

**Good Evolution** (Additive):
```
Steel Thread:    app/adapters/fake_imagegen.py
Pragmatic CA:    app/adapters/imagegen/fake_imagegen.py  # Added nesting
Full CA:         app/adapters/imagegen/fake.py           # Renamed within same location
```

**Bad Evolution** (Transformative):
```
Steel Thread:    tests/fakes/fake_imagegen.py            # Wrong layer
Pragmatic CA:    app/adapters/imagegen/fake_imagegen.py  # REWORK: moved to different layer
Full CA:         app/infrastructure/external/imagegen/   # REWORK: moved again
```

**Implications**:
- Files should stay in same conceptual location across all three PoC types
- Can nest deeper, but shouldn't move between layers
- Imports paths should be additive (add depth) not transformative (change root)
- Mental model must be consistent (same concepts, progressive detail)

### 2. Multi-Stakeholder Workshop Context

**Key Insight from Blog Post**: Tests and code are written WITH stakeholders present in 90-minute workshops.

**Stakeholder Visibility**:
- Creative Lead (brand consistency, visual quality)
- Ad Operations Manager (performance tracking, A/B testing)
- IT Architect (SLAs, cost control, integrations)
- Legal/Compliance Officer (content filtering, regulatory checks)

**Code Structure Implications**:
- Folder names must make sense to **non-technical stakeholders**
- Test assertions labeled by stakeholder: `# Creative Lead:`, `# IT:`
- Business domain language preferred over technical jargon
- Examples: `fake_brand_validator`, `fake_dam_integration` (not `FakeBrandValidatorAdapter`)

### 3. Fakes as Production Code for Demos

**From Axiom 3**: "Fakes are Production Code" - used in workshops, local dev, CI/CD, not just tests.

**Usage Pattern**:
```python
async def test_generate_campaign(
    orchestrator,
    fake_brand_validator,    # Injected in workshop demo
    fake_dam_integration,
    fake_content_filter
):
```

**Implications**:
- Fakes must be easy to discover and import
- Not "test utilities" - they're **demo tools** for stakeholder validation
- Must be configurable for workshop scenarios (e.g., simulate failures)
- Location affects discoverability and mental model

### 4. Three-Layer Test Strategy

**From Blog Post**:
```
Layer 1: Acceptance Tests (Always fakes, fast, run on every commit)
Layer 2: Adapter Integration Tests (Real services, focused, run nightly)
Layer 3: E2E Smoke Tests (Full integration, minimal, run before deploy)
```

**Test Folder Structure from Blog**:
```
tests/
  acceptance/
    test_localized_campaign_feature.py     # With fakes
  integration/
    adapters/
      test_openai_adapter_integration.py   # Real OpenAI
      test_dam_adapter_integration.py      # Real DAM
  e2e/
    test_production_readiness.py           # Full system
```

**Implications**:
- "adapters" terminology consistently used in blog
- Integration tests organized by adapter type
- Acceptance tests never use real services

### 5. Orchestrator as Primary Entry Point

**From Blog Post**:
```python
async def test_generate_localized_summer_sale_campaign(
    orchestrator,  # ← Injected, not individual use cases
    fake_brand_validator,
    ...
):
    response = await orchestrator.generate_campaign(request)
```

**Implications**:
- Tests call orchestrators, not use cases directly
- If we add drivers/, they call orchestrators (not use cases)
- Orchestrator coordinates workflow across use cases
- Matches Axiom 2: "Orchestrator-Centric Architecture"

### 6. Rich Domain DTOs

**From Blog Post**:
```python
request = LocalizedCampaignRequest(
    global_message="Summer Sale: Up to 50% Off",
    product_category="outdoor_lifestyle",
    markets=["UK", "DE", "FR", "MX", "BR"],
    brand_guidelines={
        "primary_colors": ["#FF6B35", "#004E89"],
        "logo_placement": "bottom_right",
        "tone": "energetic_but_premium"
    }
)
```

**Implications**:
- DTOs use business domain language (not technical)
- Rich nested structures acceptable
- Visible to stakeholders in workshop tests
- Must be easy to construct in test scenarios

---

## Decision Evaluation Criteria

For each of the 5 decisions, I will evaluate options against these criteria:

### A. Progressive Evolution
- ✅ Can Steel Thread use simplified version?
- ✅ Can Pragmatic CA add depth without moving files?
- ✅ Can Full CA formalize without breaking imports?
- ✅ Is mental model consistent across all three?

### B. Stakeholder Workshop Clarity
- ✅ Do folder/file names make sense to non-technical stakeholders?
- ✅ Is business domain language preferred over technical jargon?
- ✅ Can stakeholders see their concerns mapped to code structure?
- ✅ Does test code read naturally in workshop context?

### C. Fakes as Demo Tools
- ✅ Are fakes easy to discover and import?
- ✅ Is location consistent with "production code" philosophy?
- ✅ Can stakeholders understand where fakes live?
- ✅ Does location enable workshop demo workflows?

### D. Consistency with Established Patterns
- ✅ Aligns with Axiom 2 (Orchestrator-Centric)?
- ✅ Aligns with Axiom 3 (Fakes are Production Code)?
- ✅ Matches blog post terminology?
- ✅ Consistent with nikolovlazar CA diagram?

### E. Pragmatic vs. Dogmatic
- ✅ Does this serve workshops, or architectural purity?
- ✅ Supports Axiom 6 (Hybrid Methodology - not pure CA)?
- ✅ Optimizes for enterprise PoC velocity?
- ✅ Balances Clean Architecture principles with practicality?

---

## Context Documents to Read

Before analyzing decisions, I need to read:

1. **`/docs/lean-clean-axioms.md`** - Core non-negotiable principles
2. **`/plan/_session-02-framework-folder-structures.md`** - Current framework definition
3. **`/lean-clean-code/CLEAN_ARCHITECTURE_ANALYSIS.md`** - Architectural analysis
4. **`/plan/_session-02-decisions-needed.md`** - Full decision details with options

---

## Already Read and Understood

1. ✅ **`/plan/templates/hitl-decision-template.md`** - HITL process
2. ✅ **`/plan/templates/hitl-decision-template.README.md`** - When to use HITL
3. ✅ **`/plan/_session-02-resume-prompt.md`** - Task context and decision summaries
4. ✅ **`/images/IMAGE_ANALYSIS.md`** - Visual references and CA diagram comparison
5. ✅ **`/lean-clean-blog/drafts/_wip-lean-clean-the-secret-sauce.md`** - Workshop dynamics

---

## Next Steps

1. ✅ Document this understanding (current file)
2. ⏳ Read detailed context documents (axioms, framework, decisions)
3. ⏳ Analyze Decision 8 (Fakes Location) with HITL approach:
   - Present all options with trade-offs
   - Evaluate against 5 criteria above
   - Provide recommendation with reasoning
   - Wait for your choice
4. ⏳ Repeat for Decisions 9-12
5. ⏳ Update framework folder structures based on your decisions

---

## Key Insights That Will Guide Analysis

### Insight 1: Location Signals Intent
Where fakes live signals whether they're "test utilities" vs "production demo tools". This affects discoverability and mental model.

### Insight 2: Terminology Affects Stakeholder Comprehension
Blog post uses "adapters" consistently. Introducing "gateways" or "repositories/services" split may confuse non-technical stakeholders unless there's clear benefit.

### Insight 3: Drivers Layer - Enterprise PoC Reality (RESOLVED)
**Resolution:** drivers/ folder from Steel Thread with CLI + UI always, FastAPI when needed

**Critical Enterprise Reality:**
- ✅ **CLI ALWAYS** - Automation, reproducibility, CI/CD
- ✅ **UI ALWAYS** - Stakeholder demos (Creative Lead, Ad Ops, Legal need visual feedback)
- ⚠️ **FastAPI WHEN NEEDED** - Enterprise integrations, webhooks, external access

**Key Insight:** Even Steel Thread needs drivers/ because enterprise PoCs require BOTH automation (CLI) AND visual feedback (Streamlit UI) from day 1. This is non-negotiable for multi-stakeholder workshops.

### Insight 4: DTOs Should Scale with PoC Type
Steel Thread might not need explicit DTOs (use domain objects directly). Full CA benefits from DTO layer (prevents domain leakage). Must support this evolution.

### Insight 5: Ports Location Affects Import Ergonomics
Whether interfaces live in `core/interfaces/`, `application/ports/`, or split into `repositories/` + `services/` affects how readable import statements are in workshop tests.

---

## Ready to Proceed

I understand:
- ✅ Progressive evolution constraint (additive, not transformative)
- ✅ Multi-stakeholder workshop context (non-technical visibility)
- ✅ Fakes as production demo tools (not test-only)
- ✅ Three-layer test strategy (acceptance/integration/e2e)
- ✅ Orchestrator-centric pattern (tests call orchestrators)
- ✅ Rich domain DTOs (business language)

I will now read the detailed context documents and begin HITL analysis of Decision 8.
