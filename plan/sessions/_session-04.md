## Claude Instructions - Session 4: Programmatic Application & Tooling

**⚠️ NOTE: This session has been split into focused sub-sessions for better execution:**
- **Session 4a** (`sessions/_session-04a.md`): Review Guide, Agent Playbooks, and Prompt Templates
- **Session 4b** (`sessions/_session-04b.md`): Project Scaffolding & Templates
- **Session 4c** (`sessions/_session-04c.md`): Artifact Generation & Integration

**Use the split sessions for actual work.** This file is kept for reference.

---

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
We now have a complete methodology (phases), framework (structure), and working examples (code). What's missing is the **tooling layer** to make the methodology easy to adopt and scale.

---

### Your Task for This Session

**Create the tooling layer that enables programmatic application of the Lean-Clean Methodology.**

The goal is to build tools that help teams move efficiently through each methodology phase, automating the mechanical parts while preserving the collaborative essence.

#### Deliverables for This Session:

**1. In-Practice Review Guide/Workbook** (Component C)

Create a practical guide that helps teams:
- **Select the right PoC type** for their situation
  - Decision tree or questionnaire format
  - Criteria: timeline, team size, stakeholder complexity, production-readiness goals
  - Examples: "If 1-2 devs, 1-5 days → Steel Thread; If multi-stakeholder, 2-3 weeks → Pragmatic CA"

- **Apply the methodology phase-by-phase**
  - For each phase: Purpose, Key Questions, Activities, Inputs/Outputs, Artifacts
  - Checklists for phase completion
  - Common pitfalls and how to avoid them
  - Example: "Phase 2 - Workshop" checklist: "[ ] Stakeholders identified, [ ] Example scenarios drafted, [ ] Test structure agreed upon"

**Format:** Interactive workbook (Markdown or Notion-style) with:
- Phase-by-phase sections
- Fill-in-the-blank templates
- Decision matrices
- Example artifacts from our creative campaign scenario

**2. Agent Playbooks**

Create playbooks for AI agents (like Claude) to assist at each methodology phase:

- **Phase 1 Playbook** (Discovery/Planning)
  - System prompt for helping identify stakeholders
  - Prompt template for extracting "desirements" from stakeholder interviews
  - Example: "You are helping a team identify underserved needs. Ask about pain points in their current workflow..."

- **Phase 2 Playbook** (Workshop/Specification)
  - System prompt for facilitating multi-stakeholder workshops
  - Prompt template for translating business requirements into Given-When-Then format
  - Example: "You are in a workshop with [Creative Lead, Legal, IT]. Help them write an executable test for..."

- **Phase 3 Playbook** (Implementation)
  - System prompt for code generation following Lean-Clean patterns
  - Prompt template for creating Fakes, Orchestrators, Use Cases
  - Example: "Generate a fake implementation of the BrandValidator use case that..."

- **Phase 4+ Playbooks** (Testing, Refinement, etc.)

**Format:** Each playbook as:
- `playbook_phase_N.md` with system prompt and prompt templates
- Example conversations showing playbook in action
- Integration points: "After Phase 2 playbook, outputs feed into Phase 3 playbook"

**3. Prompt Templates**

Human-facing prompt templates for common tasks:

- **Stakeholder Interview Template**

I'm conducting a discovery interview for a [type] PoC.
Stakeholder: [role]
Current process: [description]
Help me identify: underserved needs, pain points, success criteria

- **Workshop Facilitation Template**
I'm running a multi-stakeholder workshop for [feature].
Stakeholders present: [list with roles]
Help me facilitate writing executable tests that capture requirements from all stakeholders.
Current scenario: [description]


- **Code Generation Template**
Generate a [Orchestrator/Use Case/Fake] for [feature] following Lean-Clean Code Paradigms.
Context: [business requirement]
Stakeholder concerns: [list]
Base Structure: [Steel Thread/Pragmatic CA/Full CA]


**Format:** Prompt library (Markdown file) organized by phase and task type

**4. Python Project Scaffolding**

Create project templates for each PoC type:

- **Steel Thread Template**
  - Minimal folder structure (flat or 2-level)
  - Basic pytest setup
  - One example feature (with fake)
  - README with "5-minute quickstart"
  
- **Pragmatic CA Template**
  - Pragmatic folder structure (our v1 from Session 3)
  - Pytest + markers for test organization
  - DX tooling: `pyproject.toml`, pre-commit hooks, type checking
  - Two example features (orchestrator + use case + fakes)
  - README with "Phase-by-phase guide"

- **Full CA Template**
  - Complete folder structure (entities, use cases, adapters, orchestrators)
  - Comprehensive test suite (acceptance, integration, unit)
  - Production-ready DX: CI/CD configs, deployment scripts
  - Three example features showing different patterns
  - README with "Architecture decision records"

**Format:** Each as a separate directory:

templates/
├── steel-thread/
├── pragmatic-ca/
└── full-ca/

Plus a **template generator script**:
```bash
python scripts/create_project.py --type pragmatic-ca --name my-poc
```
5. Phase-Specific Artifacts Generator
Create a tool that generates artifacts based on phase completion:
Example Flow:

```text
Phase 1 complete → Workbook has: target customers, underserved needs, value prop
                ↓
   Auto-generate: Project setup, stakeholder matrix, initial test structure

Phase 2 complete → Workbook has: workshop outputs, agreed-upon scenarios
                ↓
   Auto-generate: Acceptance test skeletons, fake implementations

Phase 3 complete → Tests passing with fakes
                ↓
   Auto-generate: Integration test templates, adapter interfaces
```

Format: Python script or CLI tool:

```bash
lean-clean generate --phase 2 --workbook team-workbook.yaml
# Outputs: tests/acceptance/test_*.py skeletons
```

## Your Role in This Session
You are my tooling architect and developer experience (DX) designer. I need you to:

1. Think end-to-end: Tools should connect across phases

- Outputs from Phase N workbook → Inputs to Phase N+1 tooling
- Example: Workshop outputs → Test skeletons → Code generation prompts


2. Prioritize adoption: Make it easy for teams to get started

- Templates should work "out of the box"
- Prompts should be copy-paste ready
- Workbook should guide without overwhelming


3. Enable, don't constrain: Tools should suggest patterns, not enforce dogma

- Teams should be able to adapt to their context
- Provide escape hatches for non-standard scenarios


4. Show, don't just tell: Include concrete examples

- Each playbook should have example conversations
- Each template should have working code
- Workbook should reference the creative campaign scenario


5. Think programmatic: How can these tools integrate?

- Can workbook outputs feed into code generation?
- Can agent playbooks chain together?
- What's the API surface for automation?

## What Good Looks Like
By the end of this session, I should have:

✅ Complete Tooling Suite

- [] In-Practice Review Guide/Workbook (Markdown, ready to use)
- [] Agent Playbooks for each methodology phase (system prompts + templates)
- [] Prompt Templates library (human-facing, organized by phase)
- [] Python project scaffolding for all 3 PoC types (runnable templates)
- [] Phase-specific artifact generator (script or tool)

✅ Connected Workflow

- [] Clear flow showing how outputs from one tool feed into the next
- [] Example: "Completed Phase 2 workbook → Run generator → Get test skeletons"
- [] Documentation showing the "golden path" through all tools

✅ Practical Validation

- [] One complete walkthrough using the creative campaign scenario
- [] Shows workbook → playbook → prompt → scaffolding → generator
- [] Demonstrates that tools actually reduce friction

✅ Adoption Readiness

- [] README for each tool explaining purpose and usage
- [] Quick-start guide: "Get started with Lean-Clean in 15 minutes"
- [] Migration guide: "Already have a project? Here's how to adopt LC patterns"

✅ Future-Proofing

- [] Clear extension points for adding new PoC types
- [] Modular design so teams can use parts independently
- [] Documentation on customizing tools for specific domains

## Constraints

Scope for This Session:

- Focus on creating the tools, not polishing them to perfection
- Prioritize Pragmatic CA templates (most common use case)
- Agent playbooks should work with Claude (but be adaptable to other LLMs)
- Keep it practical: tools should save time, not add overhead

Out of Scope:

- Web-based workbook interface (Markdown is fine for v1)
- CI/CD integration (mention it as future work)
- Multi-language support (Python only for now)
- Enterprise deployment considerations (focus on team-level usage)

Quality Bar:

- Tools should be functional, not beautiful
- Documentation should be clear, not comprehensive
- Examples should be realistic, not exhaustive

## Context: Why This Matters
The methodology (Session 1), framework (Session 2), and code examples (Session 3) are intellectually sound, but without tooling, adoption will be slow and painful.
Teams will struggle with:

- "Which PoC type do we need?" → Review Guide answers this
- "How do we run a workshop?" → Agent Playbook guides this
- "What's the folder structure again?" → Scaffolding provides this
- "How do we go from workshop → code?" → Generator automates this

The goal: Make it possible for a team to go from "Let's try Lean-Clean" to "We have a working PoC" in hours, not days.


## Success Metrics
After this session, a new team should be able to:

1. Decide their approach in 15 minutes using the Review Guide
2. Run their first workshop in 90 minutes using Agent Playbook + Prompts
3. Generate project structure in 5 minutes using scaffolding
4. Get to first passing test in 2 hours using code generation
5. Understand the patterns by reading working examples in template

Ultimate test: Could we hand this to a team who's never heard of Lean-Clean and have them successfully deliver a PoC?

## Suggested Approach
I recommend tackling this in order:

1. Start with Review Guide/Workbook (defines the user journey)
2. Create Agent Playbooks (enables facilitation)
3. Build Prompt Templates (supports direct human use)
4. Generate Scaffolding (provides starting point)
5. Add Artifact Generator (connects phases together)

This order ensures each tool builds on the previous, and you're always working with the end-to-end flow in mind.

## Example Output Structure

By end of session, we should have:

```text
lean-clean-methodology/
├── docs/
│   ├── review-guide.md              # In-Practice Review Guide
│   ├── prompt-library.md            # Human-facing prompt templates
│   └── quick-start.md               # 15-minute getting started
├── playbooks/
│   ├── phase-1-discovery.md         # Agent playbook
│   ├── phase-2-workshop.md          # Agent playbook
│   ├── phase-3-implementation.md    # Agent playbook
│   └── examples/                    # Example conversations
├── templates/
│   ├── steel-thread/                # Minimal template
│   ├── pragmatic-ca/                # Standard template (our focus)
│   └── full-ca/                     # Comprehensive template
├── scripts/
│   ├── create_project.py            # Project generator
│   ├── generate_artifacts.py        # Phase-specific generator
│   └── README.md                    # Tool usage docs
└── examples/
    └── creative-campaign-walkthrough/  # Full example using all tools
```