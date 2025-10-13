# Lean-Clean Methodology: Core Axioms

**Purpose:** These axioms guide all methodology decisions, framework design, and code examples. When in doubt, return to these principles.

**Last Updated:** 2025-10-13 (Session 02)

---

## Axiom 1: Multi-Stakeholder Specification by Example

**Principle:** Tests are written **WITH all stakeholders present**, not by developers FOR stakeholders.

**Why:** Enterprise PoCs have competing concerns (Creative, Legal, Ad Ops, IT). Traditional sequential handoffs create costly ping-pong. This methodology enables parallel specification in a single workshop.

**Implications:**

- Test names must use stakeholder language, not developer jargon
- Docstrings must capture who cares about each assertion
- Tests are the shared contract before any expensive infrastructure
- Workshop outputs ARE the specification (executable, not a Word doc)

**Example:**

```python
# ✅ Good - stakeholders can read and validate
async def test_generate_localized_summer_sale_campaign(...)

# ❌ Bad - developer-centric naming
async def test_orchestrator_batch_processing(...)
```

---

## Axiom 2: Orchestrator-Centric Architecture

**Principle:** The **Orchestrator** layer coordinates multiple use cases for a feature and captures cross-cutting stakeholder concerns.

**Why:**

- "Controller" is overloaded in web dev (FastAPI request handlers)
- Stakeholders understand "orchestration" (coordinate creative → validate → emit events)
- This layer is where multi-stakeholder requirements converge

**Terminology Decision:**

- **Methodology docs:** "Orchestrator" (stakeholder language)
- **Code:** `orchestrator` variable names, `orchestrators/` folder, `*Orchestrator` class names
- **Tests:** "Feature" at acceptance level maps to "Orchestrator" at implementation level

**Implications:**

- `app/interface_adapters/orchestrators/` (NOT `controllers/`)
- `LocalizedCampaignOrchestrator` (NOT `LocalizedCampaignController`)
- FastAPI may still have `routers/` or `api_controllers/` for HTTP concerns—that's fine, different layer

**Example:**

```python
# ✅ Good - clear orchestration role
class LocalizedCampaignOrchestrator:
    """Coordinates generate → validate → emit events workflow"""

# ❌ Bad - conflicts with FastAPI controllers
class LocalizedCampaignController:
    """Unclear: HTTP controller or orchestration?"""
```

---

## Axiom 3: Fakes are Production Code, Not Test Doubles

**Principle:** Fakes are **first-class implementations** living in `app/adapters/`, not `tests/`.

**Why:**

- Enable Outside-In TDD: write tests before real adapters exist
- Used in workshops, local dev, CI/CD, stakeholder demos
- Realistic behavior (deterministic, with latency simulation)
- NOT mocks (no mocking frameworks, no magic)

**Implications:**

- `app/adapters/imagegen/fake_imagegen.py` (production code)
- Fakes implement the same interface as real adapters
- Acceptance tests ALWAYS use fakes (fast, deterministic)
- Integration tests use real adapters (verify contract correctness)

**Example:**

```python
# ✅ Good - fake in adapters/, realistic behavior
# app/adapters/imagegen/fake_imagegen.py
class FakeImageGenerator:
    cost_per_image = 0.02  # Match real pricing

    async def generate(self, prompt: str, size: str) -> bytes:
        await asyncio.sleep(0.1)  # Simulate latency
        return self._create_deterministic_image(prompt)

# ❌ Bad - mock in tests/, unrealistic
# tests/test_campaign.py
def test_campaign(mocker):
    mock_imagegen = mocker.patch('openai.generate')
    mock_imagegen.return_value = b"fake"  # No latency, no cost tracking
```

---

## Axiom 4: Outside-In TDD Workflow

**Principle:**

1. Write **acceptance tests** with stakeholders (fakes)
2. Implement **orchestrators and use cases** (business logic)
3. Add **real adapters** in parallel (contract already defined)

**Why:** Validates you're building the right thing before expensive integrations.

**Implications:**

- **Day 1:** Workshop → executable tests with fakes
- **Day 2-3:** Stakeholders see working PoC (fakes), iterate on behavior
- **Day 4-5:** Implement real adapters (tests still pass)
- Cost: $200 vs. $5,000 traditional
- Time: 5 days vs. 6-8 weeks traditional

**Three-Layer Testing Strategy:**

1. **Acceptance** (Layer 1): Always fakes, <100ms, business logic
2. **Integration** (Layer 2): Real adapters, run nightly, verify contracts
3. **E2E** (Layer 3): Full system, run before deploy, smoke tests

**Example Workflow:**

```bash
# Day 1: Workshop
$ pytest tests/acceptance/  # ✅ All pass with fakes, <2s, $0

# Day 3: Add real OpenAI adapter
$ pytest tests/acceptance/  # ✅ Still pass (use fakes)
$ pytest tests/integration/adapters/test_openai_adapter.py  # ✅ Real API, ~$0.50

# Day 5: Production readiness
$ pytest tests/e2e/  # ✅ Full system, ~$2
```

---

## Axiom 5: Terminology Aligns with Stakeholders

**Principle:** Since stakeholders are involved in test definition from the outset, method names and test suites are **front-stage** (visible to non-developers).

**Why:** Stakeholders must be able to read and validate tests in workshops. Jargon creates barriers.

**Implications:**

- Use domain language: `LocalizedCampaignRequest`, not `CampaignDTO`
- Use BDD mindset: Given/When/Then docstrings, not technical specs
- Test markers: `@pytest.mark.feature("localized_campaign_generation")`
- Assertion messages: "Creative Lead requirement: All assets must be brand compliant"

**Example:**

```python
# ✅ Good - stakeholder can validate
async def test_generate_summer_sale_campaign_for_five_markets(...)
    """
    Given: Global summer sale campaign targeting EU and LATAM
    When: Generating localized variants for Instagram and Facebook
    Then: All stakeholder requirements met (brand compliant, <3s, filtered)
    """

# ❌ Bad - developer jargon
async def test_batch_processing_with_concurrent_futures(...)
    """Tests parallel execution of generation tasks"""
```

---

## Axiom 6: Hybrid Methodology (Not Pure CA)

**Principle:** This methodology is **not constrained to Clean Architecture**. We draw from best-of-all-worlds for enterprise PoCs.

**Influences:**

- **Clean Architecture** - Layers, dependency inversion, testable boundaries
- **Hexagonal Architecture** - Ports & Adapters (may fit better than CA's interface adapters)
- **Domain-Driven Design** - Ubiquitous language, domain modeling
- **Behavior-Driven Development** - Stakeholder-readable tests, Given/When/Then
- **Test-Driven Development** - Write tests first, red-green-refactor
- **Lean Product Playbook** - Customer focus, MVP, validated learning

**Implications:**

- We can deviate from CA when it serves PoC velocity
- We can mix Ports & Adapters terminology with CA layers
- We can simplify or extend patterns based on PoC type
- Consistency matters more than purity

**Example Deviations from Pure CA:**

- ✅ Pragmatic CA: Entities can be ORM models (acceptable for PoCs)
- ✅ Pragmatic CA: Presenters handle telemetry (not AOP/middleware)
- ✅ Full CA: Separate entities from ORM (production scale)
- ❌ Never: Violate dependency inversion (use cases depend on interfaces, not adapters)

---

## Axiom 7: Progressive Refinement (Not Rewrites)

**Principle:** Steel Thread → Pragmatic CA → Full CA is **progressive refinement**, not revolution. Tests written early remain valid throughout.

**Why:** Most PoCs die after validation. Investing in Full CA upfront is waste. Evolution path allows right-sizing architecture to needs.

**Implications:**

- **Same testing philosophy** across all PoC types (Outside-In with fakes)
- **Stakeholder contract is stable** (acceptance tests don't change)
- **You're refining implementation**, not changing behavior
- Steel Thread → Pragmatic CA: **Add layers** (orchestrators, presenters)
- Pragmatic CA → Full CA: **Refine layers** (entities from ORM, events, CQRS)

**Example:**

```python
# Steel Thread (Day 1)
async def generate_campaign(input):
    imagegen = FakeImageGenerator()  # Hardcoded
    return await imagegen.generate(...)

# Pragmatic CA (Day 3) - add orchestration + DI
class LocalizedCampaignOrchestrator:
    def __init__(self, generate_uc, validate_uc):  # DI
        self.generate_uc = generate_uc

# Full CA (Month 3) - add events + CQRS
class LocalizedCampaignOrchestrator:
    def __init__(self, generate_uc, validate_uc, event_bus):
        self.event_bus = event_bus  # Domain events

# ✅ Key: Acceptance tests pass at ALL three levels
```

---

## Axiom 8: Edge Cases are Business Decisions

**Principle:** Edge cases are **business risk management** questions, not technical puzzles. Stakeholders are domain experts on which failures matter.

**Why:** Engineers over-engineer edge case handling without knowing stakeholder priorities. This methodology asks stakeholders in the workshop.

**Implications:**

- Ask: "What happens if the AI service is down?"
- Get stakeholder decision: "Partial success acceptable" vs. "Fail entire campaign"
- Write edge case tests **in the workshop** with stakeholders
- Fakes make edge case testing instant (configure failures on the fly)

**Example:**

```python
# Discovered in workshop: Legal has zero tolerance for content filtering failures
async def test_content_filter_failure_blocks_entire_campaign(...)
    """
    Legal: "If content filtering fails, NOTHING goes live. Zero risk tolerance."
    """
    fake_content_filter.configure_failure(error_type="service_unavailable")

    with pytest.raises(ContentFilterUnavailableError):
        await orchestrator.generate_campaign(request)

# Discovered in workshop: Ad Ops prefers partial success over delayed launch
async def test_partial_market_failure_allows_partial_success(...)
    """
    Ad Ops: "If 2 of 5 markets fail, deliver the 3 that succeeded"
    """
    fake_ai_service.configure_failures(markets=["MX", "BR"])

    response = await orchestrator.generate_campaign(request)
    assert response.status == "partial_success"
    assert len(response.creative_sets) == 3  # UK, DE, FR
```

---

## Axiom 9: Speed Over Perfection for PoCs

**Principle:** PoC velocity beats architectural purity. 80% of PoCs die after validation—optimize for speed to validation, not production scale.

**Why:** Enterprise PoCs need to prove value fast. Over-engineering is waste if stakeholders reject the concept.

**Implications:**

- **Steel Thread** (1-2 days): Minimal layers, prove feasibility
- **Pragmatic CA** (3-5 days): Production-ready subset, multi-stakeholder workshops
- **Full CA** (weeks): Only when scaling demands it (multi-team, complex domain)
- Default to **Pragmatic CA** for enterprise PoCs with workshops

**Trade-offs:**

- Pragmatic CA: Accept coupling (entities = ORM) for velocity
- Pragmatic CA: Explicit presenter methods (telemetry) vs. AOP/middleware
- Full CA: Separate entities from ORM when domain complexity justifies it

---

## Axiom 10: Consistency Enables Evolution

**Principle:** Terminology and patterns must be **consistent across methodology docs, code examples, and test suites** to enable evolution without rewrites.

**Why:** Inconsistency creates confusion and forces rewrites during evolution. Consistency means Steel Thread code can evolve to Full CA by **adding**, not **replacing**.

**Implications:**

- **Orchestrator** terminology everywhere (not Controller in some places, Orchestrator in others)
- **Fakes** in `adapters/` everywhere (not `tests/fakes/` in some projects)
- **Three-layer testing** everywhere (acceptance → integration → e2e)
- **Interface definitions** in `core/interfaces/` everywhere (Dependency Inversion)

**Example:**

```python
# ✅ Consistent across all PoC types
# Steel Thread
async def generate_campaign(input):
    orchestrator = CampaignOrchestrator(...)  # ← "orchestrator"

# Pragmatic CA
class LocalizedCampaignOrchestrator:  # ← "Orchestrator"

# Full CA
class LocalizedCampaignOrchestrator:  # ← Same name, refined implementation

# ❌ Inconsistent (forces rewrites)
# Steel Thread: generate_campaign function
# Pragmatic CA: CampaignController class  # ← Changed terminology
# Full CA: CampaignService class  # ← Changed again
```

---

## Anti-Patterns to Avoid

These violate one or more axioms:

### ❌ Anti-Pattern 1: Developer-Centric Test Names

- **Violates:** Axiom 1 (Multi-stakeholder specification), Axiom 5 (Stakeholder terminology)
- **Example:** `test_async_batch_processing_with_retries()`
- **Fix:** `test_generate_campaign_for_five_markets_with_partial_failure()`

### ❌ Anti-Pattern 2: Using "Controller" for Orchestration

- **Violates:** Axiom 2 (Orchestrator-centric), Axiom 5 (Stakeholder terminology)
- **Example:** `class CampaignController` in `interface_adapters/controllers/`
- **Fix:** `class CampaignOrchestrator` in `interface_adapters/orchestrators/`

### ❌ Anti-Pattern 3: Mocks Instead of Fakes

- **Violates:** Axiom 3 (Fakes are production code), Axiom 4 (Outside-In TDD)
- **Example:** `mocker.patch('openai.generate').return_value = b"fake"`
- **Fix:** `FakeImageGenerator` in `app/adapters/imagegen/fake_imagegen.py`

### ❌ Anti-Pattern 4: Testing Real Services in Acceptance Tests

- **Violates:** Axiom 4 (Outside-In TDD), Axiom 9 (Speed over perfection)
- **Example:** Acceptance test that calls real OpenAI API
- **Fix:** Acceptance tests ALWAYS use fakes; integration tests use real services

### ❌ Anti-Pattern 5: Engineer Guessing at Edge Cases

- **Violates:** Axiom 8 (Edge cases are business decisions)
- **Example:** Implementing elaborate retry logic without asking stakeholders
- **Fix:** Ask in workshop: "What happens if...?" → write test with stakeholder

### ❌ Anti-Pattern 6: Pure CA Dogma

- **Violates:** Axiom 6 (Hybrid methodology)
- **Example:** Insisting on separate entities from ORM in Steel Thread
- **Fix:** Accept coupling in Steel Thread, refine in Full CA when needed

### ❌ Anti-Pattern 7: Big Bang Rewrite During Evolution

- **Violates:** Axiom 7 (Progressive refinement)
- **Example:** Throwing away Steel Thread code to start over with Full CA
- **Fix:** Add layers incrementally, tests remain valid

### ❌ Anti-Pattern 8: Terminology Inconsistency

- **Violates:** Axiom 10 (Consistency enables evolution)
- **Example:** Using "Controller" in docs, "Orchestrator" in code, "Service" in tests
- **Fix:** Pick one term (Orchestrator), use everywhere

### ❌ Anti-Pattern 9: Over-Engineering Steel Thread

- **Violates:** Axiom 9 (Speed over perfection)
- **Example:** Implementing CQRS + event sourcing in 1-day feasibility spike
- **Fix:** Steel Thread is minimal—prove feasibility, defer architecture

### ❌ Anti-Pattern 10: Skipping Stakeholder Workshops

- **Violates:** Axiom 1 (Multi-stakeholder specification)
- **Example:** Developer writes tests alone, presents to stakeholders later
- **Fix:** Write tests WITH stakeholders in 90-minute workshop

---

## Decision-Making Framework

When facing a design choice, ask:

1. **Does this align with stakeholders?** (Axiom 1, 5)
   - Can non-developers read this?
   - Would this make sense in a workshop?

2. **Does this enable Outside-In TDD?** (Axiom 4)
   - Can we test this with fakes?
   - Can stakeholders see it working before real adapters?

3. **Is this consistent with our terminology?** (Axiom 2, 10)
   - Are we using "Orchestrator" everywhere?
   - Are fakes in `adapters/`?

4. **Does this support evolution?** (Axiom 7)
   - Will this code work in Steel Thread AND Full CA?
   - Are we adding layers, not replacing?

5. **Is this the right PoC type?** (Axiom 9)
   - Steel Thread: 1-2 day spike?
   - Pragmatic CA: Multi-stakeholder workshop?
   - Full CA: Production scale?

6. **Are we being pragmatic?** (Axiom 6)
   - Is pure CA overkill here?
   - Would Ports & Adapters be clearer?

---

## Summary: The Non-Negotiables

These axioms are **non-negotiable** for Lean-Clean Methodology:

1. ✅ **Tests written WITH stakeholders** (not for them)
2. ✅ **"Orchestrator" terminology** (not "Controller")
3. ✅ **Fakes in `adapters/`** (not `tests/`)
4. ✅ **Outside-In workflow** (acceptance → fakes → real adapters)
5. ✅ **Stakeholder-readable tests** (domain language, BDD mindset)
6. ✅ **Hybrid methodology** (best-of-all-worlds, not pure CA)
7. ✅ **Progressive refinement** (add layers, don't rewrite)
8. ✅ **Edge cases decided by stakeholders** (in workshops)
9. ✅ **Speed over perfection for PoCs** (right-size architecture)
10. ✅ **Consistency everywhere** (enables evolution)

When in doubt, **return to the blog post** (`_wip-lean-clean-the-secret-sauce.md`) for the rationale behind these axioms.

---

**Document Status:** ✅ Complete

**Last Updated:** 2025-10-13 (Session 02)

**Next Review:** When adding new PoC types, patterns, or making architectural decisions
