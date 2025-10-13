# Session 01: Architectural Decisions

**Date:** 2025-10-13
**Purpose:** Resolve divergent architectural questions blocking methodology definition
**Status:** Completed

---

## Context

The Lean-Clean Methodology blends Lean Product Development, Clean Architecture, and Agentic Design to accelerate enterprise PoCs. Before finalizing methodology phases, we needed to resolve fundamental architectural questions about how much CA to adopt, where cross-cutting concerns live, and how different PoC types evolve.

---

## Divergent Questions Resolved

### Q1: How much Clean Architecture should PoCs adopt?

**Answer:** Define three evolutionary PoC types with clear progression paths

**Three PoC Types:**

1. **Steel Thread** (1-2 days, single happy path)
   - Minimal layering: `use_cases/` + `adapters/` only
   - No controllers, no presenters, no DTOs
   - Tests with fakes prove end-to-end flow
   - **Goal:** Validate technical feasibility fast

2. **Pragmatic CA** (3-5 days, production-ready subset)
   - Add: `controllers/` (orchestration), `presenters/` (formatting)
   - Keep: Simple DTOs, Protocol-based ports
   - Tests: Acceptance (fakes) + adapter integration
   - **Goal:** Multi-stakeholder workshop output

3. **Full CA** (production hardening)
   - Add: Separate domain entities from ORM models
   - Add: Comprehensive DTO layer
   - Add: Event sourcing, CQRS if needed
   - **Goal:** Production scalability

**Rationale:**
- Enterprise PoCs need speed first, rigor later
- The "secret sauce" (Outside-In with stakeholders) works at all three levels
- Evolution path prevents over-engineering while leaving door open to production
- 80% of PoCs die after validation—Steel Thread prevents wasted Full CA investment

**Trade-offs:**
- ❌ Risk: Teams might ship Steel Thread to production (mitigate with methodology phase gates)
- ✅ Benefit: Faster validation, lower cost, stakeholder feedback in days not weeks

---

### Q2: Where do cross-cutting concerns (events, metrics, observability) live?

**Answer:** Depends on PoC type, but presenters are the sweet spot for Pragmatic CA

**Placement by PoC Type:**

| Concern | Steel Thread | Pragmatic CA | Full CA |
|---------|-------------|--------------|---------|
| **Telemetry/Metrics** | Inline in use cases (pragmatic) | Presenters (via `attach_telemetry()`) | Middleware/Decorators |
| **Events** | N/A (too complex for Steel Thread) | Presenters emit domain events | Event Bus + Domain Events |
| **Logging** | Python logging inline | Presenters + structured logs | AOP/Decorators |

**Pragmatic CA Pattern** (methodology sweet spot):

```python
# interface_adapters/presenters/campaign_presenter.py
class CampaignPresenter:
    @staticmethod
    def to_response(output: GenerateCampaignOutput) -> CampaignResponse:
        """Format domain output for API/UI consumption"""
        return CampaignResponse(...)

    @staticmethod
    def attach_telemetry(span, cmd: GenerateCampaignCommand, result: GenerateCampaignOutput):
        """Telemetry is a presentation concern - formatting domain data for observability"""
        for k, v in (*cmd_attrs(cmd), *result_attrs(result)):
            span.set_attribute(k, v)

    @staticmethod
    def emit_events(tracker, result: GenerateCampaignOutput):
        """Events represent 'what happened' to stakeholders/systems"""
        tracker.emit("campaign_generated", {
            "campaign_id": result.campaign_id,
            "market_count": len(result.creative_sets)
        })
```

**Rationale:**
- Presenters already format output—telemetry is "output for observability systems"
- Keeps use cases clean of infrastructure concerns
- Aligns with "Outside-In" philosophy: presenters are the boundary
- Explicit methods are testable and stakeholder-visible

**Trade-offs:**
- ❌ Some purists argue telemetry should be middleware (correct for Full CA)
- ✅ For PoC velocity, explicit presenter methods win

---

### Q3: What's the right balance between pytest patterns and BDD/Gherkin?

**Answer:** Use pytest with BDD-style naming, reserve Gherkin for stakeholder-facing acceptance tests only

**Recommended Pattern:**

```python
# Layer 1: Acceptance tests (stakeholder contract) - BDD docstrings
@pytest.mark.feature("localized_campaign_generation")
class TestLocalizedCampaignFeature:
    """
    Feature: Generate compliant, localized creative variants at scale

    Stakeholders:
    - Creative Lead: Brand compliance, visual quality
    - Ad Operations: Performance tracking, A/B testing
    - IT: <3s latency, <$2 cost
    - Legal: Content filtering, regulatory compliance
    """

    async def test_generate_summer_sale_campaign_for_five_markets(self, ...):
        """
        Given: Global summer sale campaign targeting EU and LATAM
        When: Generating localized variants for Instagram and Facebook
        Then: All stakeholder requirements met (brand compliant, <3s, filtered)
        """
        # Arrange - use domain language
        request = LocalizedCampaignRequest(
            global_message="Summer Sale: Up to 50% Off",
            markets=["UK", "DE", "FR", "MX", "BR"],
            ...
        )

        # Act
        response = await orchestrator.generate_campaign(request)

        # Assert - stakeholder success criteria
        assert len(response.creative_sets) == 5
        assert all(set.brand_compliant for set in response.creative_sets.values())
```

**Avoid:** Full Gherkin `.feature` files (behave, pytest-bdd) for enterprise PoCs

**Rationale:**
- BDD docstrings + domain language are readable by technical stakeholders
- Gherkin adds ceremony without value for technical stakeholders (Ad Ops, IT, Legal)
- BDD is a *mindset* (Given/When/Then thinking), not a *syntax* requirement
- pytest is Python-native, better IDE support, easier debugging

**Trade-offs:**
- ❌ Non-technical stakeholders can't *write* tests (but can they ever, realistically?)
- ✅ Technical stakeholders can read and validate tests in workshops

---

### Q4: Should we have a concept above "Orchestrator" (like "Feature")?

**Answer:** Yes, but only for Pragmatic CA and above. Call it "Feature" at test level, map to "Controller" at implementation level.

**Test Organization:**
```
tests/
├── acceptance/           # Layer 1: Stakeholder contracts (always fakes)
│   └── features/         # ← "Feature" level
│       ├── test_localized_campaign_feature.py
│       ├── test_brand_compliance_feature.py
│       └── test_approval_workflow_feature.py
├── unit/
│   ├── use_cases/        # Business logic tests
│   └── controllers/      # Orchestration tests
└── integration/
    └── adapters/         # Real service tests
```

**Implementation Mapping:**

| Test Layer | Code Layer | Purpose |
|------------|------------|---------|
| **Feature Test** | Controller | Multi-use-case orchestration |
| **Use Case Test** | Use Case | Single business operation |
| **Adapter Test** | Adapter | External service integration |

**Example:**

```python
# tests/acceptance/features/test_localized_campaign_feature.py
@pytest.mark.feature("localized_campaign_generation")
class TestLocalizedCampaignFeature:
    """Maps to: LocalizedCampaignController"""

# src/interface_adapters/controllers/localized_campaign_controller.py
class LocalizedCampaignController:
    """
    Orchestrates the full localized campaign generation feature.

    Coordinates:
    - GenerateCampaignUseCase (creates creatives)
    - ValidateBrandComplianceUseCase (checks brand rules)
    - EmitCampaignEventsUseCase (tracks analytics)
    """
    def __init__(
        self,
        generate_campaign_uc: GenerateCampaignUseCase,
        validate_brand_uc: ValidateBrandComplianceUseCase,
        emit_events_uc: EmitCampaignEventsUseCase
    ): ...

    async def handle(self, request: LocalizedCampaignRequest) -> LocalizedCampaignResponse:
        # 1. Orchestrate multiple use cases
        # 2. Handle cross-cutting concerns
        # 3. Format via presenter
```

**Rationale:**
- "Feature" is stakeholder language (from BDD)
- "Controller" is implementation language (from CA)
- One-to-one mapping makes codebase navigable
- Avoids bikeshedding over "Orchestrator" vs "Service" vs "Facade"

**Trade-offs:**
- ❌ Adds a layer vs. calling use cases directly from routers
- ✅ Necessary for multi-stakeholder PoCs where features span multiple use cases

---

### Q5: How do Steel Thread, Pragmatic CA, and Full CA relate/evolve?

**Answer:** They're progressive refinements, not different paradigms

**Evolution Path:**

```text
Steel Thread              Pragmatic CA              Full CA
(1-2 days)               (3-5 days)                (production)
─────────────────────────────────────────────────────────────

use_cases/      ──────►  use_cases/       ──────►  use_cases/
  ├─ generate.py           ├─ generate.py             ├─ generate/
                                                      │   ├─ command.py
                                                      │   ├─ use_case.py
                                                      │   └─ dtos.py

adapters/       ──────►  adapters/        ──────►  adapters/
  ├─ openai.py             ├─ openai.py               ├─ openai.py
  ├─ fake_ai.py            ├─ fake_ai.py              ├─ fake_ai.py

                         controllers/      ──────►  controllers/
                           ├─ campaign.py             ├─ campaign/

                         presenters/       ──────►  presenters/
                           ├─ campaign.py             ├─ campaign.py

tests/          ──────►  tests/           ──────►  tests/
  └─ test_end_to_end.py    ├─ acceptance/             ├─ acceptance/
                           ├─ unit/                   ├─ unit/
                           └─ integration/            ├─ integration/
                                                      └─ e2e/
```

**Migration Checklist:**

**Steel Thread → Pragmatic CA:**
- [ ] Add controllers to orchestrate multi-step workflows
- [ ] Add presenters for response formatting + telemetry
- [ ] Split tests into acceptance (fakes) + integration (real)
- [ ] Define Input/Output DTOs for use cases
- **Trigger:** Stakeholders approved Steel Thread, need production readiness

**Pragmatic CA → Full CA:**
- [ ] Separate domain entities from ORM models
- [ ] Add comprehensive DTO layer with validation
- [ ] Implement event sourcing if needed
- [ ] Add CQRS if read/write patterns diverge
- [ ] Extract cross-cutting concerns to middleware/decorators
- **Trigger:** Scaling to multi-team, production load, or complex domain

**Rationale:**
- All three use the **same testing philosophy** (Outside-In with fakes)
- Stakeholder tests written in Steel Thread **still pass** in Full CA
- You're **refining implementation**, not changing stakeholder contract
- Most PoCs never reach Full CA—that's a feature, not a bug

**Key Insight:**
Make the simplification explicit in the methodology: "Start simple, evolve as needed." The CLEAN_ARCHITECTURE_ANALYSIS.md correctly identified that lean-clean is "simplified CA for PoC speed"—now we're making that a deliberate strategy, not an apology.

---

## Summary Table

| Question | Answer | Key Trade-off | Methodology Impact |
|----------|--------|---------------|-------------------|
| **Q1: How much CA?** | Three types: Steel Thread, Pragmatic CA, Full CA | Speed vs. rigor | Phases must specify which PoC type |
| **Q2: Cross-cutting concerns?** | Presenters (Pragmatic CA), Middleware (Full CA) | Pragmatism vs. purity | Phase 5 implementation planning |
| **Q3: pytest vs Gherkin?** | pytest with BDD docstrings | Readability vs. formalism | Testing guidance in Framework |
| **Q4: "Feature" concept?** | Yes, maps to Controllers | Extra layer vs. clarity | Acceptance tests structure |
| **Q5: Evolution path?** | Progressive refinement, same philosophy | Consistency across types | Phase gates define transitions |

---

## Implications for Methodology Phases

These architectural decisions inform:

1. **Phase 3 (Steel Thread Definition):** Must clarify this is "Steel Thread PoC type"
2. **Phase 5 (Implementation Planning):** Must specify which PoC type to build
3. **Phase 6 (Execution):** Testing strategy depends on PoC type
4. **Phase 7 (Reflection):** Should include "evolution decision" (stay, upgrade, or retire)

---

## Next Steps (Session 02)

With architectural questions resolved, Session 02 will focus on:
- Defining concrete folder structures for each PoC type
- Creating Python code examples for the social media campaign scenario
- Documenting the "Lean-Clean Code Paradigms" (Component D)
- Framework definition (Component B) with specific patterns for each PoC type

**Key principle:** These examples will use the multi-agent chat app helping creatives generate social media campaign images as the running example throughout.
