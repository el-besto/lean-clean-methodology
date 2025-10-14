## Claude Instructions - Session 4a: Review Guide, Playbooks, and Prompts

### What Has Been Done (Sessions 1-3)

**Session 1: Methodology Definition**
- ✅ Defined 3-5 phases of the Lean-Clean Methodology
- ✅ Resolved divergent architectural questions (CA adoption level, testing approach, etc.)
- ✅ Established core principles: Outside-In TDD, multi-stakeholder workshops, executable specifications
- ✅ Aligned on vocabulary and conceptual foundation

**Session 2: Framework Definitions**
- ✅ Documented folder architecture for PoC types (Steel Thread, Pragmatic CA, Full CA)
- ✅ Defined how executable tests are written with stakeholders
- ✅ Showed evolution path between Base Structures
- ✅ Created Framework documentation (README.md)

**Session 3: Working Code Examples**
- ✅ Generated v1 folder structure for Pragmatic CA PoC
- ✅ Implemented "golden example" - localized campaign generation with fakes
- ✅ Documented Lean-Clean Code Paradigms (Orchestrators, Use Cases, Fakes, Adapters)
- ✅ Created runnable pytest suite demonstrating Outside-In approach

**Current State:**
We now have a complete methodology (phases), framework (structure), and working examples (code). What's missing is the **human-facing guidance layer** to make the methodology accessible and actionable.

---

### Your Task for This Session

**Create the human-facing documentation layer that enables teams to adopt and apply the Lean-Clean Methodology.**

This session focuses on documentation, guidance, and prompts—the materials that help teams understand when and how to use the methodology.

#### Component C: In-Practice Review Guide/Workbook

**Context for Claude Code Agents:**
Component C is the "application layer" of the methodology—it bridges the abstract methodology (Component A) and concrete framework (Component B) with practical, actionable guidance. Think of it as the user manual that helps teams apply the methodology in real-world scenarios.

This component directly addresses the question: "I understand the phases and the architecture, but what do I actually DO in each phase?"

**Relationship to Other Components:**
- **Component A** (Methodology phases): Defines WHAT the phases are and WHY they exist
- **Component B** (Framework): Defines HOW to structure code and tests
- **Component C** (Review Guide): Defines WHEN to use each approach and WHO does what
- **Component D** (Code Paradigms): Provides executable EXAMPLES of the patterns

Create a practical guide that helps teams:

**1. Select the right PoC type** for their situation
- Decision tree or questionnaire format
- Criteria: timeline, team size, stakeholder complexity, production-readiness goals
- Examples: "If 1-2 devs, 1-5 days → Steel Thread; If multi-stakeholder, 2-3 weeks → Pragmatic CA"
- Clear comparison table: Steel Thread vs. Pragmatic CA vs. Full CA

**2. Apply the methodology phase-by-phase**
- For each phase: Purpose, Key Questions, Activities, Inputs/Outputs, Artifacts
- Checklists for phase completion
- Common pitfalls and how to avoid them
- Example: "Phase 2 - Workshop" checklist:
  - [ ] Stakeholders identified
  - [ ] Example scenarios drafted
  - [ ] Test structure agreed upon
  - [ ] First executable test written together

**Format:** Interactive workbook (Markdown) with:
- Phase-by-phase sections
- Fill-in-the-blank templates for capturing decisions
- Decision matrices (When to use X vs. Y)
- Example artifacts from the creative campaign scenario
- "Anti-patterns" section showing what NOT to do

**File:** `docs/review-guide.md`

---

#### Supporting Tools: Agent Playbooks

Create playbooks for AI agents (like Claude Code) to assist at each methodology phase. These are system prompts that help Claude Code act as a facilitator/assistant at different stages.

**Phase 1 Playbook: Discovery & Planning**
- System prompt for helping identify stakeholders and extract requirements
- Prompt template for transforming "desirements" into testable scenarios
- Example conversation showing the playbook in action
- Output: List of stakeholders, underserved needs, initial scenarios

**Phase 2 Playbook: Workshop Facilitation**
- System prompt for facilitating multi-stakeholder workshops
- Prompt template for translating business requirements into Given-When-Then format
- Guidance on resolving conflicts between stakeholder needs
- Example conversation showing collaborative test writing
- Output: Acceptance test specifications, agreed-upon scenarios

**Phase 3 Playbook: Implementation Support**
- System prompt for code generation following Lean-Clean patterns
- Prompt template for creating Fakes, Orchestrators, Use Cases
- Guidance on maintaining test-first discipline
- Example conversation showing TDD workflow
- Output: Executable tests with fake implementations

**Phase 4+ Playbooks: Testing, Refinement, Production Readiness**
- System prompts for integration testing, adapter implementation, production hardening
- Guidance on evolving from fakes to real implementations
- Example conversations showing the transition

**Format:** Each playbook as:
- `playbooks/phase-N-[name].md` with:
  - Clear role definition for Claude Code
  - System prompt (copy-paste ready for Claude Code)
  - Task-specific prompt templates
  - 2-3 example conversations showing effectiveness
  - Integration points: "Outputs from this phase feed into..."

---

#### Supporting Tools: Prompt Templates

Create a library of human-facing prompt templates for common tasks. These are for humans to use when working with Claude Code (or other AI assistants).

**Stakeholder Interview Template**
```
I'm conducting a discovery interview for a [PoC type] using the Lean-Clean Methodology.

Stakeholder: [role]
Current process: [description]
Pain points mentioned: [list]

Help me identify:
- Underserved needs
- Success criteria
- Potential testable scenarios
- Stakeholder concerns that should be captured in tests
```

**Workshop Facilitation Template**
```
I'm running a multi-stakeholder workshop for [feature] using Lean-Clean Methodology.

Stakeholders present: [list with roles]
Current scenario under discussion: [description]

Help me:
- Facilitate writing an executable test that captures all stakeholder concerns
- Translate this into Given-When-Then format
- Identify edge cases or missing perspectives
- Suggest how to structure the test for clarity
```

**Code Generation Template**
```
Generate a [Orchestrator/Use Case/Fake] for [feature] following Lean-Clean Code Paradigms.

Context: [business requirement]
Stakeholder concerns: [list]
Base Structure: [Steel Thread/Pragmatic CA/Full CA]
Test that needs to pass: [reference to test file]

Please generate:
- The implementation that makes the test pass
- Docstrings explaining business logic
- Any additional tests for edge cases
```

**Migration/Adoption Template**
```
I have an existing [type of project] and want to adopt Lean-Clean patterns.

Current state: [description]
Goal: [desired state]
PoC type selected: [Steel Thread/Pragmatic CA/Full CA]

Help me:
- Identify what to keep vs. refactor
- Plan migration steps
- Write tests for existing functionality
- Establish the new folder structure
```

**Format:**
- `docs/prompt-library.md` organized by:
  - Phase (Discovery, Workshop, Implementation, etc.)
  - Task type (Interview, Facilitation, Code Gen, Debugging, etc.)
  - Each template includes:
    - Context/when to use it
    - Fill-in-the-blank template
    - Example filled-in version
    - Expected output from Claude

---

### Your Role in This Session

You are my documentation architect and facilitation designer. I need you to:

1. **Think from the user's perspective**: What questions will teams have? What will confuse them?

2. **Make it actionable**: Every section should have clear next steps. Avoid abstract principles without concrete guidance.

3. **Show, don't just tell**: Include concrete examples from the creative campaign scenario throughout.

4. **Enable self-service**: Teams should be able to use these materials without needing to ask the methodology creator for clarification.

5. **Design for Claude Code**: Agent playbooks should leverage Claude Code's strengths (reading files, writing code, running tests, analyzing architectures).

6. **Connect the dots**: Show how outputs from the Review Guide inform Agent Playbooks, which reference Prompt Templates.

---

### What Good Looks Like

By the end of this session, I should have:

✅ **Complete Review Guide/Workbook**
- [ ] PoC type selection guide with decision tree
- [ ] Phase-by-phase application guide
- [ ] Checklists for each phase
- [ ] Anti-patterns and pitfalls section
- [ ] Example walkthrough using creative campaign

✅ **Agent Playbooks (Phases 1-4)**
- [ ] Phase 1: Discovery & Planning playbook
- [ ] Phase 2: Workshop Facilitation playbook
- [ ] Phase 3: Implementation Support playbook
- [ ] Phase 4+: Testing & Refinement playbook
- [ ] Example conversations for each playbook

✅ **Prompt Template Library**
- [ ] Templates for all major tasks (Interview, Workshop, Code Gen, Migration)
- [ ] Organized by phase and task type
- [ ] Each template includes example usage
- [ ] Clear guidance on when to use each template

✅ **Integration Story**
- [ ] Clear documentation showing how these tools work together
- [ ] "Golden path" example: Start to finish using all materials
- [ ] Connection points between Review Guide → Playbooks → Templates

---

### Constraints

**Scope for This Session:**
- Focus on Phases 1-4 (Discovery through Testing)
- Prioritize Pragmatic CA examples (most common use case)
- Playbooks designed for Claude Code but adaptable to other LLMs
- Keep it practical: documentation should enable action, not overwhelm

**Out of Scope:**
- Python code/scaffolding (Session 4b)
- Automation tooling (Session 4c)
- Web-based interfaces (Markdown is fine)
- Multi-language support (Python-focused examples)

**Quality Bar:**
- Documentation should be clear and scannable
- Examples should be realistic and complete
- Playbooks should be immediately usable
- Templates should be copy-paste ready

---

### Success Metrics

After this session, a new team should be able to:

1. **Decide their approach in 15 minutes** using the Review Guide
2. **Understand the phases** and know what to do in each one
3. **Run their first workshop in 90 minutes** using Agent Playbook + Prompts
4. **Generate clear specifications** that developers can implement
5. **Know when they've completed a phase** using the checklists

Ultimate test: Could a team who's never heard of Lean-Clean read these docs and successfully run a workshop?

---

### Suggested Approach

I recommend tackling this in order:

1. **Start with Review Guide structure** (defines the user journey)
2. **Create Phase 2 Agent Playbook first** (workshop is the core differentiator)
3. **Build Prompt Templates in parallel** (extract common patterns from playbooks)
4. **Add Phase 1, 3, 4 Playbooks** (building on the Phase 2 pattern)
5. **Create integration example** (show how it all connects)

This order ensures you're always designing from the user's perspective and the materials connect naturally.

---

### Context: Why This Matters

The methodology is intellectually sound, but without clear guidance, teams will struggle with:
- "Which PoC type do we need?" → Review Guide answers this
- "How do we run a workshop?" → Agent Playbook + Prompts guide this
- "What do we do next?" → Phase checklists provide direction
- "Are we done with this phase?" → Completion criteria give confidence

**Goal:** Make it possible for a team to go from "Let's try Lean-Clean" to "We successfully ran our first workshop" without external help.
