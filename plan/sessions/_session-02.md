## Claude Instructions - Session 2

### Context
In Session 1, we defined the Lean-Clean Methodology phases and resolved divergent architectural questions (see `/plan/_session-01-architectural-decisions.md` for detailed rationale, `/plan/_session-01-summary.md` for overview). Now we need to create the Framework that implements it.

### Your Task for This Session

**Craft the Framework definitions** (Component B from Primary Components)

Specifically:
1. Review the existing README.md (mostly complete)
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

### Anti-Patterns to Avoid

❌ **Don't create overly abstract structures**
   - Avoid deep nesting (>4 levels) that obscures purpose
   - Each directory should have a clear, single responsibility
   - If you need a README to explain the structure, it's too complex

❌ **Don't copy folder structures without understanding trade-offs**
   - Don't blindly replicate Uncle Bob's diagrams
   - Adapt CA principles to PoC constraints (time, team size, scope)
   - Document WHY each layer exists, not just WHAT it contains

❌ **Don't write documentation without examples**
   - Every folder structure needs at least one concrete example
   - Show the Outside-In test flow through actual files
   - Use the creative campaign scenario consistently

❌ **Don't ignore test organization**
   - Test structure is as important as app structure
   - Tests should mirror the architecture (acceptance → orchestrator, integration → use case, unit → entity)
   - Make it obvious where new tests should go

❌ **Don't treat all PoC types the same**
   - Steel Thread should be dramatically simpler than Full CA
   - Pragmatic CA is the sweet spot—focus here first
   - Evolution path should be clear: add layers, don't rewrite

### Suggested Approach

I recommend tackling this in order:

1. **First, review Session 01 outputs** (15-20 minutes)
   - Read `/plan/_session-01-summary.md` for quick overview
   - Read `/plan/_session-01-architectural-decisions.md` for detailed Q&A (especially Q1: PoC types, Q2: cross-cutting concerns, Q4: Feature/Controller mapping)
   - Review methodology phases defined in `README.md` (Phase 5: PoC type selection, Phase 7: evolution decisions)
   - Note any architectural decisions that inform folder structure

2. **Then, review existing README.md** (30-45 minutes)
   - Identify what's already documented and working
   - Note gaps or conflicts with Session 01 decisions
   - Understand the nikolovlazar CA pattern (explicit Infrastructure layer)

3. **Next, define Pragmatic CA folder structure** (60-90 minutes)
   - Start with core layers: Core (entities, use_cases, interfaces) and Adapters
   - Add Orchestrators for acceptance-level coordination
   - Define test structure (acceptance, integration, unit)
   - Map one complete feature through the structure (campaign generation)
   - Show how fakes enable TDD without external dependencies

4. **Then, simplify to Steel Thread** (30-45 minutes)
   - Remove layers: flatten to orchestrator + use_case + fakes
   - Keep the testing pattern, simplify the organization
   - Show this as "Pragmatic CA minus ceremony"

5. **Expand to Full CA** (45-60 minutes)
   - Add production concerns: Controllers, Presenters, Models
   - Add observability hooks, configuration management
   - Show this as "Pragmatic CA plus production requirements"

6. **Finally, document evolution path** (30 minutes)
   - Create migration guide: Steel Thread → Pragmatic CA → Full CA
   - Show what gets added at each step
   - Provide decision criteria for when to evolve

### Why This Matters

Without clear folder architecture, teams will:
- Struggle with "where does this code go?"
- Mix concerns across layers
- Create tests that are hard to maintain
- Debate architecture during implementation (too late)

**The goal:** Make the architecture self-evident from the folder structure. A developer should be able to clone the repo and immediately understand where to add new features.

The Framework bridges the methodology (Session 01) with actual code (Session 03). Getting this right means:
- Tests guide implementation (Outside-In TDD)
- Layers are properly decoupled (Dependency Inversion)
- PoCs can evolve without rewrites
- Stakeholders see value quickly (Steel Thread) while maintaining quality (Pragmatic CA)

**We'll tackle the Python code examples in Session 3.**