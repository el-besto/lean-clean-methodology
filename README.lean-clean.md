# Lean-Clean Methodology
_A pragmatic framework for designing, prototyping, and operationalizing AI-driven systems._

Version: **v2.2 (Agentic + Observability + Streamlit + Weaviate + Postgres)**  
Status: **Living Document — update per project context**

---

## 🧭 Overview

**Lean-Clean** blends principles from:
- **Lean Product Development** → iterative learning, minimal viable slice, validated loops  
- **Clean Architecture** → decoupled layers, testable boundaries, replaceable adapters  
- **Agentic Design** → AI-assisted orchestration, observability, and human-in-the-loop workflows  

The goal is to turn *rough requirements* into an **implemented, testable PoC** with traceable artifacts, clear responsibilities, and optional agentic automation.

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

**Note:** The methodology supports three PoC types—**Steel Thread** (1-2 days, minimal layers), **Pragmatic CA** (3-5 days, controllers + presenters), and **Full CA** (production-ready, comprehensive layers). Structure below represents Pragmatic CA. See Session 01 architectural decisions for progression paths.

```
app/
├─ core/          # Domain models & use cases
├─ adapters/      # Infrastructure: storage, imagegen, vector, db
├─ utils/         # Helpers: observability, manifest, brief loader
├─ server.py      # FastAPI (optional micro-API)
├─ models.py      # SQLAlchemy models (Run, Approval, Alert)
└─ db.py          # DB session utilities (Postgres)
tools/
├─ agent_watcher.py   # Experimental automation watcher
├─ init_db.py         # Create tables
└─ validate.py        # Schema validation CLI
streamlit_app.py      # Simple UI dashboard for approvals and status
docker-compose.yaml   # Full stack (poc-app, api, streamlit, weaviate, phoenix, postgres)
Makefile              # Developer commands (up, down, test, db-init, watch)
pyproject.toml        # uv-managed dependencies
tests/                # pytest suite
```

---

## ⚙️ Phases (Lean-Clean v2.2)

| Phase  | Name                                  | Core Question                       | Purpose                                                        | Key Output                        | Key Artifacts                                         |
|:-------|:--------------------------------------|-------------------------------------|:---------------------------------------------------------------|-----------------------------------|:------------------------------------------------------|
| **P0** | Context Framing                       | Why are we solving this?            | Define the "why" and constraints.                              | Problem Statement                 | `problem_statement.md`                                |
| **P1** | Decomposition                         | What are the atomic parts?          | Identify atomic steps, interfaces.                             | Concept Map, Unknowns             | `domain_model.yaml`                                   |
| **P2** | Ideation & Concept Design             | How might we solve it?              | Explore options & tradeoffs.                                   | Solution Options                  | `pros_cons_matrix.md`                                 |
| **P3** | Refinement & Steel Thread Definition  | What’s the smallest testable slice? | Select a "steel thread" MVP path.                              | Steel Thread Definition           | `decision_matrix.md`                                  |
| **P4** | Reconstitution & Roadmapping          | How does this fit into the roadmap? | Translate decisions → epics & dependencies.                    | Epics & Architecture              | `epics_list.yaml`                                     |
| **P5** | Implementation Planning               | How do we build it "Cleanly"?       | Define executable spec (use-case YAML).                        | Stack Decisions, Skeleton         | `use_case_spec.yaml`                                  |
| **P6** | Execution & Iteration                 | Can it work in reality?             | Build & run the PoC.                                           | PoC Output                        | `working_poc`, `manifest.json`                        |
| **P7** | Reflection & Knowledge Capture        | What did we learn?                  | Capture lessons & next steps.                                  | Retrospective, Reusable Artifacts | `retrospective.md`                                    |
| **P8** | Agentic System Design & Communication |                                     | Extend system with agents, monitoring, and stakeholder alerts. |                                   | `agent_task.P8.yaml`, `mcp.yaml`, `alert_template.md` |

    
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

> Decide how to build and test.
>   __Define interfaces, machine-readable use cases, and validation checklists.__

**Goal:** Define the technical execution strategy.

**Activities:**

- **Select PoC type:** Steel Thread (1-2 days, minimal), Pragmatic CA (3-5 days, production-ready subset), or Full CA (production hardening)
- Select framework(s), tools, adapters
- Plan test harnesses, mock data, and deployment environment
- Define interface boundaries (ports/adapters)
- Plan code skeletons aligned to selected PoC type's layering strategy

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

**Outputs:**

- Working PoC 
- Validation report (what worked, what didn’t)
- Recommendations for next iteration or production hardening

### Phase 7: Reflection & Knowledge Capture

> Close the loop.

**Goal:** Institutionalize the learnings for future PoCs.

**Activities:**

- Document decision log and learning summary
- Update reusable templates, boilerplates, and playbooks
- Conduct a retrospective ("what would we do differently?")
- **Decide evolution path:** Stay at current PoC type, evolve to next level, or retire

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

## 📡 Observability Layer

**Goal:** Make agentic actions measurable.

| Component           | Purpose                          | Technology                   |
|---------------------|----------------------------------|------------------------------|
| Phoenix             | Local LLM trace explorer         | `arizephoenix/phoenix`       |
| Arize               | Hosted observability / analytics | `api.arize.com`              |
| Observability Stubs | Minimal HTTP emitters for PoC    | `app/utils/observability.py` |

Enable by setting:
```bash
export OBS_ENABLED=1
export PHOENIX_ENDPOINT=http://localhost:6006/api/traces
export ARIZE_ENDPOINT=https://api.arize.com/v1/log
export ARIZE_API_KEY=xxx
```

---

## 🗃️ Data & Storage

| Layer        | Purpose                           | Tool                 |
|--------------|-----------------------------------|----------------------|
| **Postgres** | Durable approvals, runs, alerts   | SQLAlchemy / psycopg |
| **Weaviate** | Vector metadata search (optional) | weaviate-client      |
| **Local FS** | Manifest & asset storage          | native I/O           |

---

## 💻 User Interfaces

| Interface      | Purpose                                       | Framework             |
|----------------|-----------------------------------------------|-----------------------|
| **CLI**        | Primary automation surface                    | Typer / argparse      |
| **FastAPI**    | Optional REST API for approvals, runs, alerts | FastAPI               |
| **Streamlit**  | Simple dashboard for human-in-loop approvals  | Streamlit             |
| **Phoenix UI** | Observability dashboard                       | http://localhost:6006 |

Streamlit runs at **http://localhost:8501** with tabs for *Runs*, *Approvals*, *Alerts*, and *Upload Brief*.

---

## 🧰 Development & Operations

**Makefile Commands**
```bash
make up         # docker compose up -d
make down       # tear down
make watch      # run watcher locally
make api        # start FastAPI server
make db-init    # create tables in Postgres
make test       # run pytest suite
```

**Local Execution**
```bash
uv sync
uv run python tools/agent_watcher.py   --inbox ./briefs/inbox   --cmd "uv run python -m app.cli run --brief {brief}"   --out-root ./out   --alerts-root ./alerts
```

---

## 🧪 Testing

Pytest suite includes:
- `test_agent_watcher.py` → verifies emit & alert logic
- (future) integration tests for brief->manifest->alert flow

```bash
uv run pytest -q
```

---

## 🧩 Example Stack (Docker Compose)

- **poc-app** — watcher + pipeline  
- **api** — FastAPI microservice  
- **streamlit** — approvals dashboard (port 8501)  
- **phoenix** — local observability (port 6006)  
- **weaviate** — vector DB (port 8080)  
- **postgres** — approvals database (port 5432)

Run everything:
```bash
docker compose up --build -d
```

---

## 🪜 Scaling the Framework

- Replace manual watcher logic with Celery, Prefect, or LangGraph agents.  
- Replace local manifests with structured DB records.  
- Integrate Slack/email webhooks for real-time alerts.  
- Apply Weaviate for RAG-style creative retrieval.  
- Deploy via Kubernetes / ArgoCD for persistent environments.

---

## 🧾 Version History

| Version  | Highlights                                                                                                   |
|----------|--------------------------------------------------------------------------------------------------------------|
| **v2.0** | Added schemas & PoC templates.                                                                               |
| **v2.1** | Added Agentic (Phase 8) spec and experimental watcher.                                                       |
| **v2.2** | Added Observability (Phoenix/Arize), FastAPI, Weaviate, Postgres, Streamlit, pytest suite, and Docker stack. |

---

## 🧠 References

- _Lean Product Playbook_ – Dan Olsen  
- _Clean Architecture_ – Robert C. Martin  
- Arize / Phoenix – ML Observability  
- Weaviate – Vector Search Engine  
- Streamlit – Rapid UI for data apps  

---

> "**Lean-Clean** is not a fixed process; it’s a scaffolding methodology for iteration.  
> Each project begins lean — decomposed, minimal, observable —  
> and grows clean — modular, testable, agent-assisted."
