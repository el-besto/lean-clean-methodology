## Claude Instructions - Session 4c: Artifact Generation & Integration

### What Has Been Done (Sessions 1-4b)

**Session 1-3 Summary:**
- ✅ Methodology phases defined
- ✅ Framework architecture documented
- ✅ Working Pragmatic CA example implemented
- ✅ Lean-Clean Code Paradigms documented

**Session 4a:**
- ✅ In-Practice Review Guide/Workbook created
- ✅ Agent Playbooks for Phases 1-4 created
- ✅ Prompt Template Library created

**Session 4b:**
- ✅ Steel Thread template created
- ✅ Pragmatic CA template created
- ✅ Full CA template created
- ✅ Template generator script created

**Current State:**
Teams have methodology (phases), framework (structure), guidance (review guide, playbooks), and starting points (templates). What's missing is the **automation layer** that connects phases together and generates artifacts programmatically.

---

### Your Task for This Session

**Create tools that automate artifact generation and connect methodology phases together.**

**Context for Claude Code Agents:**
This session builds the "orchestration layer" that connects all methodology components into an integrated workflow. While previous components defined the methodology, framework, guidance, and templates independently, this session creates the automation that makes them work together seamlessly.

**Relationship to Other Components:**
- **Component A** (Methodology): Defines phases and their inputs/outputs
- **Component B** (Framework): Defines what artifacts look like
- **Component C** (Review Guide): Guides humans through phases
- **Component D + Templates**: Provide starting code and patterns
- **This session (Automation)**: Generates artifacts programmatically and connects phases

Think of this as building the "assembly line" that takes outputs from one phase (captured in structured formats) and generates inputs for the next phase (code, tests, configurations). This reduces mechanical work and keeps teams in a productive flow state.

**Key Principle for Artifact Generation:**
Generate structure and boilerplate, NOT business logic. The generator should create well-formed code skeletons with clear TODOs where human decisions are needed. Never generate actual business rules—those come from stakeholder workshops and developer expertise.

This session focuses on building automation that:
1. Generates phase-specific artifacts from structured inputs
2. Connects outputs from one phase to inputs for the next
3. Reduces mechanical work so teams focus on creative problem-solving
4. Creates an end-to-end integrated workflow demonstration

---

### Deliverable 1: Phase-Specific Artifact Generator

**Purpose:** Automate the generation of boilerplate artifacts as teams progress through methodology phases.

#### Core Concept

As teams complete each phase and document decisions in the workbook, the generator creates the next phase's artifacts:

```
Phase 1 complete → Workbook has: target customers, underserved needs, value prop
              ↓
   Auto-generate: Project setup, stakeholder matrix, initial test structure

Phase 2 complete → Workbook has: workshop outputs, agreed-upon scenarios
              ↓
   Auto-generate: Acceptance test skeletons, Given-When-Then files

Phase 3 complete → Tests passing with fakes
              ↓
   Auto-generate: Integration test templates, real adapter interfaces
```

#### Implementation

**File:** `scripts/generate_artifacts.py`

**CLI Interface:**
```bash
# Generate artifacts based on completed phase
lean-clean generate --phase 2 --workbook team-workbook.yaml --output ./my-project

# Specific artifact types
lean-clean generate tests --scenarios scenarios.yaml --output ./tests/acceptance
lean-clean generate adapters --interfaces interfaces.yaml --output ./app/adapters
lean-clean generate use-cases --requirements requirements.yaml --output ./app/core/use_cases
```

**Features:**

1. **Phase 1 → Phase 2 Artifacts**
   - Input: Workbook with stakeholder list, underserved needs, value proposition
   - Output:
     - Stakeholder matrix (CSV/Markdown)
     - Initial test structure (empty test files with TODOs)
     - Workshop agenda template
     - Example Given-When-Then scenarios (based on identified needs)

2. **Phase 2 → Phase 3 Artifacts**
   - Input: Workshop outputs (scenarios in YAML/Gherkin format)
   - Output:
     - Acceptance test skeletons (pytest files)
     - Orchestrator stubs (method signatures with docstrings)
     - Use case interface definitions
     - Fake adapter stubs

3. **Phase 3 → Phase 4 Artifacts**
   - Input: Passing acceptance tests with fakes
   - Output:
     - Integration test templates (use case + real adapter)
     - Real adapter interface definitions
     - Configuration templates for external services
     - Observability hooks (logging, tracing decorators)

**Data Formats:**

Define structured formats for inputs (workbook outputs):

**scenarios.yaml** (Phase 2 output):
```yaml
scenarios:
  - id: campaign_generation
    description: Generate localized campaign images
    given:
      - "User provides campaign brief with target markets"
      - "Brand guidelines are available"
    when:
      - "User requests campaign generation"
    then:
      - "System generates localized images for each market"
      - "Images comply with brand guidelines"
      - "Campaign metadata is stored"
    stakeholders:
      - Creative Lead
      - Brand Manager
      - Legal
```

**interfaces.yaml** (Phase 3 output):
```yaml
adapters:
  - name: IImageGenerator
    description: Interface for AI image generation services
    methods:
      - name: generate
        params:
          - name: prompt
            type: str
          - name: style_params
            type: dict
        returns: Image
      - name: validate_output
        params:
          - name: image
            type: Image
          - name: guidelines
            type: BrandGuidelines
        returns: bool
```

**Code Generation Logic:**

The generator should:
- Parse structured YAML inputs
- Use Jinja2 templates for code generation
- Follow Lean-Clean Code Paradigms (from Session 3)
- Generate appropriate docstrings and type hints
- Insert TODO comments where human decisions needed
- Validate generated code (syntax check, imports)

---

### Deliverable 2: End-to-End Workflow Integration

**Purpose:** Demonstrate how all tools work together in a realistic scenario.

#### The Golden Path Walkthrough

Create a comprehensive example showing a team going from idea to working PoC using all Lean-Clean tools:

**File:** `examples/creative-campaign-walkthrough/README.md`

**Structure:**
```
examples/creative-campaign-walkthrough/
├── README.md                           # Narrative walkthrough
├── phase-1/
│   ├── discovery-notes.md              # Initial stakeholder interviews
│   ├── workbook-phase-1.yaml           # Completed Phase 1 workbook
│   └── generated/                      # Artifacts generated from Phase 1
│       ├── stakeholder-matrix.md
│       └── initial-test-structure/
├── phase-2/
│   ├── workshop-transcript.md          # Example workshop conversation
│   ├── scenarios.yaml                  # Agreed-upon scenarios
│   ├── workbook-phase-2.yaml           # Completed Phase 2 workbook
│   └── generated/                      # Artifacts generated from Phase 2
│       ├── test_campaign_generation.py
│       ├── orchestrator_stubs.py
│       └── use_case_interfaces.py
├── phase-3/
│   ├── implementation-notes.md         # Developer notes
│   ├── workbook-phase-3.yaml           # Completed Phase 3 workbook
│   └── code/                           # Working implementation
│       ├── tests/
│       ├── app/
│       └── pytest-output.txt           # Passing test results
└── phase-4/
    ├── integration-testing.md          # Integration approach
    ├── real-adapter-config.yaml        # Configuration for real services
    └── generated/
        ├── integration_tests.py
        └── firefly_adapter.py
```

**README.md Contents:**

1. **Introduction**
   - The scenario: Creative team needs AI-assisted campaign generation
   - The stakeholders: Creative Lead, Brand Manager, Legal, IT
   - The goal: PoC proving the concept works

2. **Phase 1: Discovery**
   - How the team used the Review Guide to select Pragmatic CA
   - Interview notes with each stakeholder
   - Completed workbook showing underserved needs
   - Running the generator:
     ```bash
     lean-clean generate --phase 1 --workbook phase-1/workbook-phase-1.yaml --output phase-2
     ```
   - What got generated and why

3. **Phase 2: Workshop**
   - How the team used the Agent Playbook to facilitate the workshop
   - Example conversation showing Claude Code helping write tests
   - Scenarios captured in structured format
   - Running the generator:
     ```bash
     lean-clean generate --phase 2 --workbook phase-2/workbook-phase-2.yaml --output phase-3
     ```
   - Generated test skeletons reviewed with stakeholders

4. **Phase 3: Implementation**
   - Developers using TDD to implement features
   - How fakes enabled fast iteration
   - Tests passing with fake implementations
   - Running the generator:
     ```bash
     lean-clean generate --phase 3 --workbook phase-3/workbook-phase-3.yaml --output phase-4
     ```
   - Integration test templates and real adapter stubs generated

5. **Phase 4: Integration**
   - Swapping fakes for real adapters (Adobe Firefly)
   - Integration tests validating real service behavior
   - Observability hooks added for monitoring
   - PoC complete and demo-ready

6. **Lessons Learned**
   - What worked well
   - What was challenging
   - How long each phase took
   - When to use this methodology vs. alternatives

---

### Deliverable 3: Quick-Start Integration Guide

**Purpose:** Show teams the fastest path to value with minimal tool adoption.

**File:** `docs/quick-start.md`

**Contents:**

**15-Minute Quick Start:**
```markdown
# Get Started with Lean-Clean in 15 Minutes

Choose your path:

## Path 1: Just Want a Template? (5 minutes)
1. Generate a project:
   ```bash
   python scripts/create_project.py --type pragmatic-ca --name my-poc
   cd my-poc
   ```
2. Run tests: `pytest -v`
3. Read `README.md` to understand the structure
4. Add your feature following the pattern

→ When you're ready for more: Check out the Review Guide

## Path 2: Need Guidance on Approach? (10 minutes)
1. Read the Review Guide decision tree (5 min)
2. Answer the questionnaire to select PoC type (2 min)
3. Generate your project (1 min)
4. Read the phase guide for your current phase (2 min)

→ When you're ready: Use Agent Playbooks to facilitate workshops

## Path 3: Want Full Methodology Support? (15 minutes)
1. Read Review Guide introduction (3 min)
2. Select PoC type using decision tree (2 min)
3. Review Agent Playbook for Phase 1 (3 min)
4. Use Prompt Template to conduct stakeholder interviews (5 min)
5. Generate project with artifacts (2 min)

→ When you're ready: Follow the Golden Path walkthrough

## The Tools at a Glance

| Tool | Purpose | When to Use |
|------|---------|-------------|
| **Review Guide** | Understand methodology, select PoC type | Start of every project |
| **Agent Playbooks** | Facilitate phases with AI assistance | During workshops and implementation |
| **Prompt Templates** | Quick AI prompts for common tasks | When working with Claude Code |
| **Project Templates** | Bootstrap project structure | At project start |
| **Artifact Generator** | Auto-generate boilerplate | After completing each phase |
| **Golden Path Example** | See it all in action | When learning the methodology |

## Common Workflows

**Workflow 1: Solo Developer Spike (Steel Thread)**
- Generate Steel Thread template (1 min)
- Write acceptance test with stakeholder (30 min)
- Implement with fakes (2 hours)
- Demo and decide next steps (30 min)

**Workflow 2: Team PoC (Pragmatic CA)**
- Review Guide to select approach (15 min)
- Phase 1 workshop with Agent Playbook (90 min)
- Generate project + artifacts (5 min)
- Phase 2 implementation with TDD (1-2 weeks)
- Demo with stakeholders (1 hour)

**Workflow 3: Production Project (Full CA)**
- Complete Phase 1-2 using Pragmatic CA (1 week)
- Migrate to Full CA template (1 day)
- Phase 3-4 with real integrations (2-3 weeks)
- Production hardening and deployment (1 week)
```

---

### Deliverable 4: Tool Integration Map

**Purpose:** Visual and textual representation of how all tools connect.

**File:** `docs/tool-integration-map.md`

**Contents:**

1. **Visual Diagram** (ASCII or Mermaid)
   ```
   Review Guide
        ↓
   Select PoC Type → Generate Template
        ↓                    ↓
   Phase 1 Playbook → Workbook Phase 1
        ↓                    ↓
   Stakeholder Interviews → Generate Artifacts → Test Structure
        ↓                    ↓
   Phase 2 Playbook → Workbook Phase 2
        ↓                    ↓
   Workshop + Tests → Generate Artifacts → Test Skeletons
        ↓                    ↓
   Phase 3 Playbook → Workbook Phase 3
        ↓                    ↓
   TDD Implementation → Generate Artifacts → Integration Tests
        ↓
   Working PoC
   ```

2. **Data Flow**
   - Show what data flows between tools
   - Examples of workbook YAML structures
   - How to manually edit generated artifacts

3. **Extension Points**
   - How to customize templates
   - How to add new artifact generators
   - How to adapt playbooks for different domains

---

### Your Role in This Session

You are my automation architect and integration designer. I need you to:

1. **Automate the mechanical, preserve the creative**: Generate boilerplate, not business logic

2. **Make connections explicit**: Show exactly how outputs become inputs

3. **Validate with the Golden Path**: Every tool should appear in the walkthrough

4. **Design for flexibility**: Teams should be able to use parts independently

5. **Think programmatically**: Clear data formats, parseable structures

6. **Optimize for transparency**: Generated code should be readable and modifiable

---

### What Good Looks Like

By the end of this session, I should have:

✅ **Artifact Generator**
- [ ] CLI tool that generates phase-specific artifacts
- [ ] Supports Phases 1-4 transitions
- [ ] Uses structured YAML inputs (documented schemas)
- [ ] Generates valid Python code (syntax checked)
- [ ] Clear error messages when inputs are malformed

✅ **Golden Path Walkthrough**
- [ ] Complete narrative example (README + artifacts)
- [ ] Shows all phases from discovery to working PoC
- [ ] Demonstrates every tool in the methodology
- [ ] Includes realistic stakeholder conversations
- [ ] Timing estimates for each phase

✅ **Quick-Start Guide**
- [ ] 15-minute fast path clearly documented
- [ ] Multiple workflows for different contexts
- [ ] Tool comparison table
- [ ] Common workflows with time estimates

✅ **Integration Map**
- [ ] Visual diagram showing tool connections
- [ ] Data flow documented
- [ ] Extension points identified
- [ ] Customization guide

---

### Constraints

**Scope for This Session:**
- Focus on Phases 1-4 (Discovery through Testing)
- Generate Python code only
- Use YAML for structured data (documented schemas)
- Keep generated code simple (prefer clarity over cleverness)

**Out of Scope:**
- Web-based workflow orchestration (CLI is fine)
- AI-powered code generation (use templates, not LLMs in the generator itself)
- Version control integration (mention as future work)
- Multi-language support

**Quality Bar:**
- Generated code must be syntactically valid
- Data schemas must be documented
- Golden Path should be completable in <8 hours of active work
- Quick-Start should enable value in <15 minutes

---

### Success Metrics

After this session, teams should be able to:

1. **Generate a complete project in under 10 minutes** (template + Phase 1 artifacts)
2. **Auto-generate test skeletons** after workshop (saves 2+ hours of boilerplate)
3. **Follow the Golden Path** and understand the full methodology
4. **Start using Lean-Clean in under 15 minutes** via Quick-Start
5. **Understand how tools connect** via the Integration Map

Ultimate test: Could a team go from "Let's try Lean-Clean" to "We have a working PoC" in under 8 hours using all the tools together?

---

### Suggested Approach

I recommend tackling this in order:

1. **Define data schemas first** (YAML structures for workbooks, scenarios, interfaces)
2. **Build Phase 2 → 3 generator** (most valuable: scenarios → tests)
3. **Create Golden Path walkthrough** (validates generator and shows integration)
4. **Add Phase 1 → 2 generator** (less critical, but completes the flow)
5. **Write Quick-Start guide** (extract patterns from Golden Path)
6. **Document Integration Map** (summarize how everything connects)

This order ensures the most valuable automation (test generation) is built first and validated through a realistic example.

---

### Context: Why This Matters

We have great methodology, great templates, great guidance—but teams still face friction:
- "How do I get from workshop outputs to code?" → Artifact generator automates this
- "What does the full workflow look like?" → Golden Path demonstrates this
- "I just want to try it quickly" → Quick-Start provides this
- "How do these tools work together?" → Integration Map clarifies this

**Goal:** Make it possible for a team to experience the full methodology end-to-end in a single day, with automation handling mechanical work so they can focus on solving their actual business problem.

The ultimate success: Teams say "This actually saved us time" rather than "This added overhead."
