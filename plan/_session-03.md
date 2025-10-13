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

**We'll tackle the Review Guide/Workbook in Session 4.**