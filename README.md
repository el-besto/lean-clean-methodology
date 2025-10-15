<div style="text-align: center;">
<pre>
    __                           ________               
   / /   ___  ____ _____        / ____/ /__  ____ _____ 
  / /   / _ \/ __ `/ __ \______/ /   / / _ \/ __ `/ __ \
 / /___/  __/ /_/ / / / /_____/ /___/ /  __/ /_/ / / / /
/_____/\___/\__,_/_/ /_/      \____/_/\___/\__,_/_/ /_/ 
                                                        
                                                        
</pre>
</div>

# Lean-Clean Methodology

_A pragmatic framework for designing, prototyping, and operationalizing Enterprise PoCs._

Status: **Living Document — update per project context**

---

## 📑 Table of Contents

- [Overview](#-overview)
- [Core Principles](#-core-principles)
- [Framework Structure](#️-framework-structure)
- [Phases](#️-phases)
  - [P0: Context Framing](#phase-0-context-framing)
  - [P1: Decomposition](#phase-1-decomposition)
  - [P2: Ideation & Concept Design](#phase-2-ideation--concept-design)
  - [P3: Refinement & Steel Thread](#phase-3-refinement--steel-thread-definition)
  - [P4: Reconstitution & Roadmapping](#phase-4-reconstitution--roadmapping)
  - [P5: Implementation Planning](#phase-5-implementation-planning)
  - [P6: Execution & Iteration](#phase-6-execution--iteration)
  - [P7: Reflection & Knowledge Capture](#phase-7-reflection--knowledge-capture)
  - [P8: Agentic System Design](#phase-8-wip)
- [Agentic Extension](#-agentic-extension-phase-8)
- [Observability Layer](#-observability-layer)
- [References](#-references)

---

## 🧭 Overview

**Lean-Clean** blends principles from:
- **Lean Product Development** → iterative learning, minimal viable slice, validated loops  
- **Clean Architecture** → decoupled layers, testable boundaries, replaceable adapters  
- **Agentic Design** → AI-assisted orchestration, observability, and human-in-the-loop workflows

The goal is to turn *rough requirements* into an **implemented, testable PoC** with traceable artifacts, clear responsibilities, and optional agentic automation.

<div style="text-align: center;">
<pre>
┏━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┓
┃  LEAN-CLEAN METHODOLOGY                ┃
┃  ────────────────────────              ┃
┃  Write Tests WITH Stakeholders         ┃
┃  Validate BEFORE Building              ┃
┃  Evolve WITHOUT Rewrites               ┃
┗━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┛
</pre>
</div>

---

## 🌿 Core Principles

| Principle                      | Description                                                             |
|--------------------------------|-------------------------------------------------------------------------|
| **Decompose before you build** | Break problems into minimal atomic steps (inputs, transforms, outputs). |
| **Design for substitution**    | Interfaces and adapters are replaceable; don’t entangle concerns.       |
| **Build a steel thread**       | Deliver a minimal end-to-end path proving technical viability.          |
| **Instrument everything**      | Specs, manifests, validation, and telemetry are first-class.            |
| **Automate learning**          | Agents monitor, test, and report; humans approve and refine.            |
| **Document as you go**         | YAML, Markdown, and schema serve as the living spec.                    |

---

## 🏗️ Framework Structure

**Note:** The methodology supports three PoC types—**Steel Thread** (1-2 days, minimal layers), **Pragmatic CA** (3-5 days, orchestrators + presenters), and **Full CA** (production-ready, comprehensive layers). Structure below represents Pragmatic CA.

**For complete folder structures, code examples, and evolution paths, see:**
- [`/docs/framework-folder-structures.md`](docs/framework-folder-structures.md) - Authoritative framework structures for all 3 PoC types
- [`/docs/references/implementations.md`](docs/references/implementations.md) - Analysis of reference implementations

### Pragmatic CA Structure (Clean Architecture)

```
campaign-generator/
├─ app/
│  ├─ entities/                        # Domain models
│  │  └─ campaign.py
│  ├─ use_cases/                       # Business logic
│  │  └─ generate_campaign_uc.py
│  ├─ interface_adapters/              # Orchestrators & Presenters
│  │  ├─ orchestrators/
│  │  │  └─ campaign_orchestrator.py
│  │  └─ presenters/
│  │     └─ campaign_presenter.py
│  ├─ adapters/                        # External services (OpenAI, S3)
│  │  ├─ imagegen/
│  │  │  ├─ protocol.py
│  │  │  ├─ fake.py
│  │  │  └─ openai.py
│  │  └─ storage/
│  │     ├─ protocol.py
│  │     ├─ fake.py
│  │     └─ s3.py
│  └─ infrastructure/                  # Persistence (Postgres, MongoDB)
│     └─ repositories/
│        └─ campaign/
│           ├─ protocol.py
│           ├─ in_memory.py
│           └─ postgres.py
│
├─ drivers/                            # Entry points (CLI + UI always)
│  ├─ cli/
│  │  └─ commands.py
│  ├─ rest/                            # When enterprise integration needed
│  │  ├─ main.py
│  │  └─ schemas/
│  └─ ui/
│     └─ streamlit/
│        └─ app.py
│
├─ tests/                              # Three-layer test structure
│  ├─ acceptance/                      # Stakeholder contracts (always fakes)
│  ├─ unit/                            # Isolated logic tests
│  └─ integration/                     # Real service tests
│
├─ tools/                              # Development utilities
│  ├─ validate.py
│  └─ init_db.py
│
├─ pyproject.toml
├─ Makefile
└─ docker-compose.yaml
```

### Phase 8 Extensions (Agentic + Observability)

> [!WARNING]
> **Under Construction:** This section is actively being developed.

When implementing Phase 8 (Agentic System Design), extend the structure:

```
tools/
├─ agent_watcher.py              # Automation watcher
├─ validate.py
└─ init_db.py

drivers/
└─ observability/                # Observability adapters
   ├─ phoenix.py
   └─ arize.py

docker-compose.yaml              # Full stack (app, phoenix, weaviate, postgres)
```

**Key Patterns:**
- **Drivers layer** - CLI + UI always (Enterprise PoC Reality)
- **Fakes in production code** - `adapters/*/fake.py` and `infrastructure/repositories/*/in_memory.py`
- **Orchestrators not Controllers** - Stakeholder-friendly terminology
- **Outside-In TDD** - Acceptance tests with stakeholders → fakes → real adapters

---

## ⚙️ Phases

**The 8-phase journey from rough requirements to production-ready PoC:**

| Phase  | Name                           | Core Question & Purpose                                                           | Key Output                | Details                                           |
|:-------|:-------------------------------|:----------------------------------------------------------------------------------|:--------------------------|:--------------------------------------------------|
| **P0** | Context Framing                | **Why are we solving this?**<br>Define the "why" and constraints.                 | Problem Statement         | [↓](#phase-0-context-framing)                     |
| **P1** | Decomposition                  | **What are the atomic parts?**<br>Identify atomic steps, interfaces.              | Concept Map, Unknowns     | [↓](#phase-1-decomposition)                       |
| **P2** | Ideation & Concept Design      | **How might we solve it?**<br>Explore options & tradeoffs.                        | Solution Options          | [↓](#phase-2-ideation--concept-design)            |
| **P3** | Refinement & Steel Thread      | **What's the smallest testable slice?**<br>Select a "steel thread" MVP path.      | Steel Thread Definition   | [↓](#phase-3-refinement--steel-thread-definition) |
| **P4** | Reconstitution & Roadmapping   | **How does this fit the roadmap?**<br>Translate decisions → epics & dependencies. | Epics & Architecture      | [↓](#phase-4-reconstitution--roadmapping)         |
| **P5** | Implementation Planning        | **How do we build it "Cleanly"?**<br>Define executable spec (use-case YAML).      | Stack Decisions, Skeleton | [↓](#phase-5-implementation-planning)             |
| **P6** | Execution & Iteration          | **Can it work in reality?**<br>Build & run the PoC.                               | PoC Output                | [↓](#phase-6-execution--iteration)                |
| **P7** | Reflection & Knowledge Capture | **What did we learn?**<br>Capture lessons & next steps.                           | Retrospective, Artifacts  | [↓](#phase-7-reflection--knowledge-capture)       |
| **P8** | Agentic System Design          | **How do we automate & monitor?**<br>Extend with agents, monitoring, alerts.      | Agent Tasks, MCP, Alerts  | [↓](#phase-8-wip)                                 |

> **Quick navigation:** Click ↓ to jump to the detailed phase descriptions below.

---

### Phase 0: Context Framing

> Anchor before diving in.

**Goal:** Ensure you understand the why and constraints before breaking things down.

**Outputs:** Problem statement, success criteria, assumptions.

**Activities:**

- Clarify desired outcome ("What job are we solving?")
- Identify constraints (time, tech stack, data access, security, etc.)
- Define measurable success metrics or proxies 
- Draft a simple "north star hypothesis"

### Phase 1: Decomposition

> Break the ambiguous into the atomic.

**Goal:** Understand the system’s smallest meaningful components.

**Activities:**

- Break down high-level problem into sub-problems and primitives 
- Identify entities, events, and relationships (domain modeling mindset)
- Map "inputs → process → outputs" for each component 
- Identify what’s known vs. unknown (and tag unknowns as experiments)

**Outputs:**

- Concept map or domain model 
- List of atomic problems / unknowns
- Initial backlog of experiments

### Phase 2: Ideation & Concept Design

> Go wide, then start focusing.

**Goal:** Generate and explore solution pathways.

**Activities:**

- Brainstorm potential architectures / workflows / use cases 
- Sketch flows (data, user, system)
- Consider analogies or reference architectures 
- Rapidly evaluate technical feasibility and novelty

**Outputs:**

- Candidate solution sketches 
- Pros/cons table 
- Early architectural sketches (e.g., C4 level 1–2)
- Draft of the "steel thread" candidate(s)

### Phase 3: Refinement & Steel Thread Definition

> Cut through to the smallest valuable slice.

**Goal:** Converge on a single minimal viable flow that proves feasibility.

**Activities:**

- Evaluate ideation options using cost–impact or risk–learning matrix 
- Define the "steel thread" (end-to-end flow that touches all critical layers once)
- Define success test(s) for PoC validation 
- Trim scope mercilessly

**Outputs:**

- Chosen steel thread definition 
- Validation plan 
- Acceptance criteria for PoC success

### Phase 4: Reconstitution & Roadmapping

> Zoom out and reconnect to the bigger picture.

**Goal:** Integrate insights into an executable plan.

**Activities:**

- Translate the steel thread into epics, stories, and dependencies 
- Create an implementation roadmap (technical + validation)
- Revisit architecture boundaries (domain vs. infrastructure)
- Identify reusability and scalability opportunities (Clean Architecture lens)

**Outputs:**

- Epic breakdown / roadmap 
- Layered architecture outline 
- Updated backlog for future phases

### Phase 5: Implementation Planning

> Decide how to build and test. Define interfaces, machine-readable use cases, and validation checklists.

**Goal:** Define the technical execution strategy.

**Activities:**

- **Select PoC type:** Steel Thread (1-2 days, minimal), Pragmatic CA (3-5 days, production-ready subset), or Full CA (production hardening)
- Select framework(s), tools, adapters
- Plan test harnesses, mock data, and deployment environment
- Define interface boundaries (ports/adapters)
- Plan code skeletons aligned to selected PoC type's layering strategy

**PoC Type Selection:**

| PoC Type         | Timeline   | Use Case                          | Evolution Trigger                           |
|------------------|------------|-----------------------------------|---------------------------------------------|
| **Steel Thread** | 1-2 days   | Technical feasibility spike       | Stakeholders approved, need production path |
| **Pragmatic CA** | 3-5 days   | Multi-stakeholder workshop output | Scaling to multi-team or complex domain     |
| **Full CA**      | Production | Production-grade system           | Already in production                       |

> **📖 Deep dive:** See [Framework Structure](#️-framework-structure) above or [`/docs/framework-folder-structures.md`](docs/framework-folder-structures.md) for complete folder structures and migration paths.

**Outputs:**

- **PoC type decision** with rationale (speed vs. rigor trade-off)
- Technical stack decision log
- PoC skeleton (repo initialized)
- Development plan with milestones

### Phase 6: Execution & Iteration

> Build → Measure → Learn → Iterate.

**Goal:** Execute the PoC, learn fast, and adapt.

**Activities:**

- Build incrementally following steel thread
- Test against success metrics
- Capture learnings, blockers, architectural insights
- Iterate or pivot

**Testing Approach:**

For enterprise PoCs with multiple stakeholders, use **Outside-In TDD**:
1. Write acceptance tests with stakeholders (executable specifications)
2. Implement with fakes (fast feedback, no API costs)
3. Implement real adapters (parallel development)

> **📖 Deep dive:** See [Core Principles](#-core-principles) above or [`/docs/framework-folder-structures.md` Section 2](docs/framework-folder-structures.md#2-complete-feature-example-localized-campaign-generation) for complete Outside-In workflow and test examples.

**Outputs:**

- Working PoC
- Validation report (what worked, what didn't)
- Recommendations for next iteration or production hardening

### Phase 7: Reflection & Knowledge Capture

> Close the loop.

**Goal:** Institutionalize the learnings for future PoCs.

**Activities:**

- Document decision log and learning summary
- Update reusable templates, boilerplates, and playbooks
- Conduct a retrospective ("what would we do differently?")
- **Decide evolution path:** Stay at current PoC type, evolve to next level, or retire

**Evolution Decision Criteria:**

**Steel Thread → Pragmatic CA:** When stakeholders approved and need production readiness
**Pragmatic CA → Full CA:** When scaling to multi-team, production load, or complex domain

**Typical Evolution Time:**
- Steel Thread → Pragmatic CA: 1-2 days
- Pragmatic CA → Full CA: 2-4 weeks

> **📖 Deep dive:** See [`/docs/framework-folder-structures.md` Section 5](docs/framework-folder-structures.md#5-evolution-path-how-poc-types-relate) for complete migration checklists and bash commands.

**Outputs:**

- Retrospective doc
- Updated reusable artifacts (schemas, diagrams, templates)
- **Evolution decision:** Steel Thread → Pragmatic CA, Pragmatic CA → Full CA, or retire
- Go/no-go decision for productionization

### Phase 8: WIP

> .

**Goal:** Extend PoC into an autonomous system that monitors briefs, triggers generation, validates results, and communicates with stakeholders.

**Agents:**

- MonitorAgent → TriggerAgent → QAAgent → ReporterAgent 
 
**Triggers:**

- New brief in ./briefs/inbox/ or failed validation in manifest

**Outputs:**
Alerts (YAML/Email), Logs, Status Dashboard

[↑ Back to Phases table](#️-phases) | [↑ Back to top](#lean-clean-methodology)

---

## 🤖 Agentic Extension (Phase 8)

### Agent Roles
| Agent             | Function                                    |
|-------------------|---------------------------------------------|
| **MonitorAgent**  | Watch incoming briefs, detect new jobs.     |
| **TriggerAgent**  | Execute PoC run when triggered.             |
| **QAAgent**       | Validate manifest outputs and completeness. |
| **ReporterAgent** | Send alerts, logs, or summaries to humans.  |

### Model Context Protocol (MCP)
A lightweight schema describing what context an LLM or alert system sees:

```yaml
mcp:
  brief_metadata:
    id: "{{brief_id}}"
    products: ["{{p1}}", "{{p2}}"]
  manifest_summary:
    missing_variants: ["{{p2}}: 16:9"]
  issues:
    - type: INSUFFICIENT_VARIANTS
      product: "{{p2}}"
      have: 2
      need: 3
```

### Stakeholder Communication Template
```markdown
# Stakeholder Alert – Creative Automation Status

**Brief:** {{brief_id}}  
**Summary:** {{summary_sentence}}

## Issues
- {{issue_1}}
- {{issue_2}}

_This alert was generated by the Agentic Monitor._
```

---

## 🚀 Future Work & Tooling

> [!NOTE]
> **Planned:** Comprehensive tooling layer to support adoption (workbooks, agent playbooks, scaffolding templates, artifact generators).
>
> See [`/plan/sessions/methodology-draft/_session-04-tooling-overview.md`](plan/sessions/methodology-draft/_session-04-tooling-overview.md) for the draft roadmap.

**Planned tooling includes:**
- **In-practice review guides and workbooks** - Interactive guides for selecting PoC types and applying methodology phase-by-phase
- **AI agent playbooks for each methodology phase** - System prompts and templates for AI-assisted facilitation
- **Project scaffolding templates** - Ready-to-use templates for Steel Thread, Pragmatic CA, and Full CA
- **Artifact generators** - Tools connecting phase outputs to phase inputs (e.g., workshop outputs → test skeletons)

**Implementation-specific tooling:**
- Observability layer integration (Phoenix, Arize)
- Data storage patterns (Postgres, Weaviate, local filesystem)
- User interface examples (CLI, Streamlit, FastAPI)
- Development & operations tooling (Makefile commands, Docker Compose stacks)
- Production scaling patterns (Celery, LangGraph agents, K8s deployment)

---

## 🧠 References

- _Lean Product Playbook_ – Dan Olsen  
- _Clean Architecture_ – Robert C. Martin
- Arize / Phoenix – ML Observability  
- Weaviate – Vector Search Engine  
- Streamlit – Rapid UI for data apps  

---

<div style="text-align: center;">
<pre>
┌────────────────────────────────────────┐
│  LEAN-CLEAN METHODOLOGY                │
├────────────────────────────────────────┤
│  Given: Workshop WITH stakeholders     │
│  When:  Tests define the spec          │
│  Then:  Code validates expectations    │
│                                        │
│  Outside-In TDD for Enterprise PoCs    │
└────────────────────────────────────────┘
</pre>
</div>

> "**Lean-Clean** is not a fixed process; it’s a scaffolding methodology for iteration.  
> Each project begins lean — decomposed, minimal, observable —  
> and grows clean — modular, testable, agent-assisted."
