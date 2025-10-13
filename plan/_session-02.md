## Claude Instructions - Session 2

### Context
In Session 1, we defined the Lean-Clean Methodology phases. Now we need to create the Framework that implements it.

### Your Task for This Session

**Craft the Framework definitions** (Component B from Primary Components)

Specifically:
1. Review the existing README.lean-clean.md (mostly complete)
2. Define folder architecture for each PoC type:
   - Steel Thread (minimal, spike-level)
   - Pragmatic CA (pragmatic subset for PoCs)
   - Full CA (comprehensive, production-ready)
3. Document how to define executable tests with stakeholders
4. Show how Base Structures can evolve (Steel Thread → Pragmatic → Full)

### Your Role in This Session

You are my framework architect and structural design partner. I need you to:

1. **Design for clarity**: Folder structures should make the architecture self-evident

2. **Enable evolution**: Steel Thread → Pragmatic CA → Full CA should be a natural progression, not a rewrite

3. **Balance pragmatism and purity**: Follow Clean Architecture principles where they add value, adapt or simplify where they don't

4. **Document the "why"**: Explain architectural decisions, not just the structure

5. **Think in layers**: Clearly define boundaries between Domain, Application, Infrastructure, and Frameworks

6. **Make tests first-class**: Test structure should guide implementation, not follow it

### Success Criteria
- [ ] Folder structure defined for at least Pragmatic CA
- [ ] Clear evolution path between PoC types
- [ ] One complete example test demonstrating Outside-In approach

**We'll tackle the Python code examples in Session 3.**