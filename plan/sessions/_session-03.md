## Claude Instructions - Session 3

### Context
We have methodology phases (Session 1) and framework definitions (Session 2). Now we need working code.

### Your Task for This Session

**Create coherent Python examples** (Component D - Lean-Clean Code Paradigms)

Specifically:
1. Generate a v1 folder structure for Pragmatic CA PoC type
2. Create the "golden example" - the localized campaign generation test
3. Implement enough code to make the test executable (with fakes)
4. Document the coding patterns used

### Your Role in This Session

You are my implementation architect and code pattern designer. I need you to:

1. **Make it executable**: Code should run and tests should pass, not just be theoretical examples

2. **Demonstrate patterns clearly**: Each code file should be a teaching example of Lean-Clean principles

3. **Keep it realistic**: Use the creative campaign scenario throughout—no toy "foo/bar" examples

4. **Show TDD in action**: Write tests first, then minimal implementation to make them pass

5. **Favor clarity over cleverness**: Code should be readable by stakeholders and developers alike

6. **Document through code**: Use meaningful names, clear structure, and targeted comments—the code itself should teach the patterns

### Success Criteria
- [ ] Working project structure that `pytest` can run
- [ ] One complete feature implemented (campaign generation)
- [ ] Patterns documented for: Orchestrators, Use Cases, Fakes, Adapters

### Anti-Patterns to Avoid

❌ **Don't write implementation before tests**
   - Always write the acceptance test first
   - Let test failures guide what to implement
   - This is Outside-In TDD, not "test after"

❌ **Don't use real external dependencies in initial implementation**
   - Start with fakes for all adapters (image gen, storage, etc.)
   - Fakes enable fast iteration and testing
   - Real adapters come later (Session 04b or beyond)

❌ **Don't use toy examples (foo/bar/baz)**
   - Always use the creative campaign scenario
   - Realistic domain language helps stakeholders understand
   - Code should read like the business domain

❌ **Don't over-implement**
   - Make tests pass with minimal code
   - Resist the urge to add "nice to have" features
   - YAGNI (You Aren't Gonna Need It) is your friend

❌ **Don't skip the documentation of patterns**
   - Docstrings should explain the "why," not the "what"
   - Add comments where the pattern isn't obvious
   - This code is teaching material, not just working code

❌ **Don't ignore type hints**
   - Use type hints for all function signatures
   - They serve as inline documentation
   - Help catch errors early

### Suggested Approach

I recommend tackling this in order:

1. **First, set up the project structure** (15-20 minutes)
   - Create the Pragmatic CA folder structure from Session 02
   - Add `pyproject.toml` with pytest dependency
   - Add `pytest.ini` with test markers
   - Add `__init__.py` files to make packages importable
   - Verify `pytest --collect-only` works

2. **Then, write the acceptance test** (30-45 minutes)
   - Start in `tests/acceptance/test_campaign_generation.py`
   - Write a test that captures the full user workflow:
     - User provides campaign brief
     - System generates localized images
     - System validates against brand guidelines
     - System stores results
   - Use Given-When-Then style comments to structure
   - This test WILL fail—that's expected

3. **Next, implement the Orchestrator** (45-60 minutes)
   - Create `app/orchestrators/campaign_orchestrator.py`
   - Implement just enough to coordinate use cases
   - Use dependency injection for adapters
   - Make the acceptance test pass (with fakes)

4. **Then, implement Use Cases** (45-60 minutes)
   - Create `app/core/use_cases/generate_campaign.py`
   - Create `app/core/use_cases/validate_brand.py`
   - Implement business logic (rules, validations)
   - Write integration tests in `tests/integration/`

5. **Next, create Fakes** (30-45 minutes)
   - Create `app/adapters/fakes/fake_image_generator.py`
   - Create `app/adapters/fakes/fake_storage.py`
   - Implement simple in-memory versions
   - Make tests fast (<100ms for full suite)

6. **Then, define Entities and Interfaces** (30 minutes)
   - Create `app/core/entities/campaign.py` (domain models)
   - Create `app/core/interfaces/image_generator.py` (adapter contracts)
   - Use dataclasses or Pydantic models for entities

7. **Finally, document patterns** (30 minutes)
   - Add README.md explaining the code structure
   - Add docstrings with pattern explanations
   - Create PATTERNS.md documenting:
     - Orchestrator pattern
     - Use Case pattern
     - Fake Adapter pattern
     - Dependency Injection approach

### Why This Matters

This session produces the "golden example" that demonstrates Lean-Clean patterns in action. It's the reference implementation that:
- Proves the Framework (Session 02) is viable
- Shows how Outside-In TDD works with real code
- Provides templates for Session 04b (scaffolding)
- Validates that stakeholders can read and understand the tests

**The goal:** Create code that's so clear, a stakeholder could review the acceptance test and say "yes, that's what we want" and a developer could read the implementation and say "I understand how to add another feature."

Without working code, the methodology remains theoretical. This session makes it concrete and actionable.

**We'll tackle the Review Guide/Workbook in Session 4.**