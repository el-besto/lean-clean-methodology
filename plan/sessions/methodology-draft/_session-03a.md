## Claude Instructions - Session 3a: Steel Thread Implementation

### Context
We have methodology phases (Session 1) and framework definitions (Session 2). Now we create the first working implementation.

### Your Task for This Session

**Create Steel Thread implementation** (Component D - Lean-Clean Code Paradigms, Part 1 of 3)

Specifically:
1. Set up the Steel Thread project structure for campaign generation PoC
2. Create the "golden example" - the localized campaign generation test
3. Implement enough code to make the test executable (with fakes)
4. Document the Steel Thread patterns

### Your Role in This Session

You are my implementation architect for the **Steel Thread PoC type**. I need you to:

1. **Make it executable**: Code should run and tests should pass, not just be theoretical examples

2. **Demonstrate Steel Thread patterns clearly**:
   - Minimal layers (use_cases + adapters + fakes + drivers)
   - Flat folder structure (no deep nesting)
   - No orchestrators (drivers call use cases directly)
   - No presenters (use cases return domain objects)
   - Protocols from day 1 (Dependency Inversion even in spikes)

3. **Keep it realistic**: Use the creative campaign scenario throughout—no toy "foo/bar" examples

4. **Show TDD in action**: Write tests first, then minimal implementation to make them pass

5. **Favor clarity over cleverness**: Code should be readable by stakeholders and developers alike

6. **Document through code**: Use meaningful names, clear structure, and targeted comments—the code itself should teach the patterns

### Target Folder Structure (Steel Thread)

**Authoritative Reference:** See [`docs/framework-folder-structures.md` §3.1](../../../docs/framework-folder-structures.md#31-folder-structure) for the complete Steel Thread structure with detailed explanations.

**Project Location:** `examples/01-steel-thread-campaign-generator/`

```
examples/01-steel-thread-campaign-generator/
├─ app/
│  ├─ entities/                        # Domain models (simple dataclasses)
│  │  └─ campaign.py
│  ├─ use_cases/                       # Flat business logic (no DTOs)
│  │  └─ generate_campaign_uc.py       # Single-file use case
│  └─ adapters/                        # Infrastructure (fakes + real)
│     ├─ imagegen/
│     │  ├─ protocol.py                # IImageGenerator (co-located)
│     │  ├─ fake.py                    # FakeImageGenerator
│     │  └─ openai.py                  # Real implementation
│     └─ storage/
│        ├─ protocol.py                # IStorageAdapter (co-located)
│        ├─ fake.py                    # FakeStorageAdapter
│        └─ local.py                   # LocalStorageAdapter
│
├─ drivers/                            # CLI + UI from day 1 (Axiom 9)
│  ├─ cli.py                           # Automation, reproducibility
│  └─ ui/
│     └─ streamlit_app.py              # Stakeholder visual demos
│
├─ tests/
│  ├─ acceptance/                      # Three-layer structure
│  │  └─ features/
│  │     └─ test_campaign_feature.py  # Always fakes
│  ├─ unit/
│  │  └─ use_cases/
│  └─ integration/
│     └─ adapters/
│
├─ .streamlit/                         # Streamlit configuration
│  └─ config.toml
│
├─ pyproject.toml
├─ pytest.ini
└─ README.md
```

### Success Criteria
- [ ] Working project structure that `pytest` can run
- [ ] Steel Thread feature implemented (campaign generation with fakes)
- [ ] Acceptance test passes (<1s, no external dependencies)
- [ ] CLI works (can run from command line)
- [ ] Streamlit UI works (can demo to stakeholders)
- [ ] Patterns documented in README.md
- [ ] All code follows Lean-Clean Python Style Guide

### Key Patterns to Demonstrate

1. **Protocols from Day 1**
   - Use `Protocol` for interfaces (not ABC)
   - Co-located `protocol.py` with implementations
   - Enables Dependency Inversion even in minimal spike

2. **Fakes in Production Code**
   - `app/adapters/imagegen/fake.py` (not `tests/fakes/`)
   - Realistic behavior (deterministic, latency simulation)
   - Used in acceptance tests, local dev, CI/CD

3. **Flat Structure**
   - `use_cases/generate_campaign_uc.py` (single file)
   - `entities/campaign.py` (simple dataclass)
   - No orchestrators, no presenters, no DTOs

4. **Drivers from Day 1**
   - CLI for automation/reproducibility (ALWAYS)
   - Streamlit UI for stakeholder demos (ALWAYS)
   - FastAPI only when needed (NOT in Steel Thread)

5. **Three-Layer Testing**
   - Acceptance: Always fakes, <100ms, business logic
   - Unit: Isolated use case tests
   - Integration: Real adapters (OpenAI, local storage)

### Anti-Patterns to Avoid

❌ **Don't write implementation before tests**
   - Always write the acceptance test first
   - Let test failures guide what to implement
   - This is Outside-In TDD, not "test after"

❌ **Don't use real external dependencies in initial implementation**
   - Start with fakes for all adapters (image gen, storage, etc.)
   - Fakes enable fast iteration and testing
   - Real adapters come later (parallel development)

❌ **Don't use toy examples (foo/bar/baz)**
   - Always use the creative campaign scenario
   - Realistic domain language helps stakeholders understand
   - Code should read like the business domain

❌ **Don't over-implement**
   - Make tests pass with minimal code
   - Resist the urge to add "nice to have" features
   - YAGNI (You Aren't Gonna Need It) is your friend

❌ **Don't add orchestrators or presenters**
   - Steel Thread: Drivers call use cases directly
   - No orchestration layer yet (comes in Pragmatic CA)
   - No presenters yet (comes in Pragmatic CA)

❌ **Don't skip type hints**
   - Use type hints for all function signatures
   - They serve as inline documentation
   - Help catch errors early

❌ **Don't use mocks**
   - Use fakes (realistic implementations), not mocks
   - No `mocker.patch()` or `unittest.mock`
   - Fakes are production code

### Suggested Approach

I recommend tackling this in order:

1. **First, set up the project structure** (15-20 minutes)
   - Create the Steel Thread folder structure at `examples/01-steel-thread-campaign-generator/`
   - Add `pyproject.toml` with pytest dependency
   - Add `pytest.ini` with test markers
   - Add `__init__.py` files to make packages importable
   - Verify `pytest --collect-only` works

2. **Then, write the acceptance test** (30-45 minutes)
   - Start in `tests/acceptance/features/test_campaign_feature.py`
   - Write a test that captures the user workflow:
     - User provides campaign brief
     - System generates localized images
     - System stores results
   - Use Given-When-Then style comments to structure
   - This test WILL fail—that's expected

3. **Next, implement the Use Case** (45-60 minutes)
   - Create `app/use_cases/generate_campaign_uc.py`
   - Implement just enough business logic to coordinate entities
   - Use dependency injection for adapters
   - Make the acceptance test pass (with fakes)

4. **Then, create Fakes** (30-45 minutes)
   - Create `app/adapters/imagegen/fake.py`
   - Create `app/adapters/storage/fake.py`
   - Implement simple in-memory versions
   - Make tests fast (<100ms for full suite)

5. **Next, define Entities and Interfaces** (30 minutes)
   - Create `app/entities/campaign.py` (domain models)
   - Create `app/adapters/imagegen/protocol.py` (adapter contracts)
   - Use dataclasses for entities

6. **Then, implement Drivers** (45-60 minutes)
   - Create `drivers/cli.py` with Typer
   - Create `drivers/ui/streamlit_app.py`
   - Wire up use cases with manual DI
   - Test both CLI and UI work

7. **Finally, document patterns** (30 minutes)
   - Add README.md explaining the code structure
   - Add docstrings with pattern explanations
   - Document:
     - Steel Thread patterns
     - Why no orchestrators/presenters
     - Evolution path to Pragmatic CA

### Why This Matters

This session produces the **Steel Thread golden example** that:
- Proves the minimal viable architecture works
- Shows how Outside-In TDD works with real code
- Provides starting point for Pragmatic CA evolution (Session 03b)
- Validates that stakeholders can read and understand the tests
- Demonstrates Enterprise PoC Reality (CLI + UI from day 1)

**The goal:** Create the fastest possible path to a working PoC that stakeholders can validate, while maintaining just enough architecture to evolve to Pragmatic CA without rewrites.

Without this executable Steel Thread, we can't demonstrate the evolution path to Pragmatic CA and Full CA.

---

## Inputs You'll Provide

When you're ready to run this session, you'll provide:
1. **Scenario PDF** - The creative campaign generation scenario with full context
2. **Any additional constraints** - Budget limits, stakeholder priorities, edge cases

**Note:** Project will be created at `examples/01-steel-thread-campaign-generator/`

---

## Outputs This Session Delivers

At the end of this session, you'll have:
1. ✅ `examples/01-steel-thread-campaign-generator/` - Fully executable project
2. ✅ Passing acceptance test (<1s execution)
3. ✅ Working CLI (`python -m drivers.cli generate`)
4. ✅ Working Streamlit UI (`streamlit run drivers/ui/streamlit_app.py`)
5. ✅ README.md documenting Steel Thread patterns
6. ✅ Ready to evolve to Pragmatic CA (Session 03b)

### Project Documentation Structure

```
examples/01-steel-thread-campaign-generator/
├─ README.md                       # Steel Thread patterns and usage
├─ app/                            # Application code
├─ drivers/                        # Entry points
└─ tests/                          # Three-layer test structure
```

**README.md should cover:**
- What is Steel Thread PoC type
- When to use it
- Key patterns demonstrated (protocols, fakes, flat structure)
- How to run tests and drivers
- Evolution path to Pragmatic CA

---

**Session Status:** 📋 Defined - Ready to Execute

**Prerequisite:** Session 02 complete (framework structures defined)

**Next Session:** 03b - Pragmatic CA Implementation (evolve from Steel Thread)
