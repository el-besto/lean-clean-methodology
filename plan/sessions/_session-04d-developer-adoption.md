# Session 04: Developer Adoption & Implementation Workflows

**Status:** 🚧 DRAFT - Awaiting Session 04 tooling completion
**Dependencies:** Session 04a (Playbooks), Session 04b (Scaffolding), Session 04c (Artifact Generation)
**Purpose:** Define developer workflows once framework tooling is finalized

---

## What Has Been Done (Previous Sessions)

**Summary of Prior Work:**
- ✅ Session 01: Methodology definition with 8 phases (P0-P8)
- ✅ Session 02: Framework folder structures finalized (Steel Thread, Pragmatic CA, Full CA)
- ✅ Session 03: Working code examples and paradigms documented
- ✅ Session 04a-c: Tooling layer (Review guides, scaffolding, generators) - IN PROGRESS

**Current State:**
We have finalized methodology and framework structures, but developer adoption workflows depend on tooling decisions being made in Session 04 (scaffolding, generators, validation tools). This session will document the "day-to-day" developer experience once those tools exist.

---

## Your Role in This Session

You are my **developer experience (DX) architect**. I need you to:

1. **Document practical workflows**: Create clear, step-by-step workflows for common developer tasks
2. **Provide runnable examples**: Every workflow should have concrete commands and expected outputs
3. **Connect to tooling**: Reference Session 04 tools (scaffolding, generators, validation)
4. **Cover the full lifecycle**: From project setup → testing → validation → CI/CD integration
5. **Think adoption**: Make it easy for developers to get started and stay productive
6. **Plan for evolution**: Show how workflows change as PoC types evolve (Steel Thread → Pragmatic CA → Full CA)

---

## Context

**Why This Session Exists:**

The methodology defines *what* to build (phases, patterns, structures), but developers need to know *how* to build it day-to-day. This includes:
- Setting up a new project
- Running tests and validation
- Using CLI tools
- Integrating with CI/CD
- Working with agentic/observability features

**Important Guidance for This Session:**

⚠️ **This content is currently DRAFT** because it depends on Session 04 tooling decisions:
- Project scaffolding approach (Session 04b)
- Artifact generation tools (Session 04c)
- Validation schemas and tools (Session 04a)

Once Session 04 completes, this session should be updated with finalized commands, tools, and workflows.

**Relationship to Other Components:**
- **Session 02 Framework**: Provides the folder structures developers work within
- **Session 04b Scaffolding**: Provides the `create_project.py` tool referenced in setup workflows
- **Session 04c Generators**: Provides the artifact generation tools referenced in workflows
- **Session 04a Playbooks**: AI-assisted development workflows that complement manual workflows

**Mental Model:** This session is the "developer's handbook" - the practical guide to building PoCs using Lean-Clean patterns.

---

## Your Task for This Session

**Document comprehensive developer workflows for implementing Lean-Clean PoCs.**

Specifically:

### 1. Project Setup Workflows

- **New project creation** using scaffolding tools
  - Command: `python scripts/create_project.py --type pragmatic-ca --name my-poc`
  - What gets generated
  - Initial configuration steps

- **Dependency management** with uv
  - `uv sync` for installing dependencies
  - Managing pyproject.toml
  - Virtual environment setup

- **IDE setup recommendations**
  - VSCode/PyCharm configuration
  - Linting and type checking
  - Test runner integration

### 2. Development Workflows

- **Implementing a new feature** (Outside-In TDD approach)
  - Write acceptance test with stakeholders
  - Implement with fakes first
  - Add real adapter implementations
  - Run test suite progression

- **Adding new adapters**
  - Define protocol/interface
  - Create fake implementation
  - Implement real adapter
  - Add integration tests

- **Working with orchestrators**
  - Coordinate multiple use cases
  - Format responses with presenters
  - Handle errors and edge cases

### 3. Testing Workflows

- **Running test suites**
  - `uv run pytest -q` - full suite
  - `uv run pytest tests/acceptance/` - acceptance tests only
  - `uv run pytest tests/unit/` - unit tests
  - `uv run pytest tests/integration/` - integration tests (real services)

- **Test markers and filtering**
  - `pytest -m "not slow"` - skip slow tests
  - `pytest -m "acceptance"` - acceptance tests only
  - Custom markers for different test categories

- **Test coverage and reporting**
  - Coverage tools
  - Test result visualization
  - CI integration

### 4. Validation Workflows

- **Schema validation** (when schemas exist)
  - `uv run python tools/validate.py playbook <playbook.yaml>`
  - `uv run python tools/validate.py use_case <use-case.yaml>`
  - Makefile shortcuts: `make validate-playbook`, `make validate-use-case`

- **Pre-commit validation**
  - Pre-commit hooks setup
  - Automated validation on commit
  - Linting and type checking

- **CI/CD validation**
  - GitHub Actions workflow
  - Validation on pull requests
  - Automated test runs

### 5. CLI Usage Patterns

- **Running PoC scenarios**
  - `uv run python -m app.cli run --brief ./campaigns/brief.yaml`
  - `uv run python -m app.cli validate --brief ./campaigns/brief.yaml`

- **Development commands**
  - Database initialization: `python tools/init_db.py`
  - Artifact generation: `lean-clean generate --phase 2 --workbook team-workbook.yaml`

- **Debugging and introspection**
  - Verbose output flags
  - Debug mode
  - Log inspection

### 6. Phase 8: Agentic & Observability Workflows

- **Agentic watcher setup** (when implemented)
  ```bash
  export OBS_ENABLED=1
  export PHOENIX_ENDPOINT=http://localhost:6006/api/traces
  export ARIZE_ENDPOINT=https://api.arize.com/v1/log
  export ARIZE_API_KEY=xxx

  uv run python tools/agent_watcher.py \
    --inbox ./briefs/inbox \
    --cmd "uv run python -m app.cli run --brief {brief}" \
    --out-root ./out \
    --alerts-root ./alerts
  ```

- **Observability integration**
  - Phoenix local trace explorer (port 6006)
  - Arize hosted analytics
  - Telemetry configuration

- **Docker stack workflows** (when docker-compose.yaml exists)
  ```bash
  make up            # docker compose up -d
  make down          # tear down
  make db-init       # create tables in Postgres
  make watch         # run watcher locally
  make api           # start FastAPI server
  ```

### 7. PoC Evolution Workflows

- **Steel Thread → Pragmatic CA migration**
  - Folder restructuring checklist
  - Test migration steps
  - Dependency updates
  - Estimated time: 1-2 days

- **Pragmatic CA → Full CA migration**
  - Complete layer separation
  - Production-ready patterns
  - CI/CD setup
  - Estimated time: 2-4 weeks

---

## Constraints

**Scope for This Session:**
- Focus on Python-based PoCs (uv, pytest, FastAPI)
- Document workflows for all 3 PoC types (Steel Thread, Pragmatic CA, Full CA)
- Provide runnable commands where tooling is finalized
- Reference Session 04 tools (scaffolding, generators)

**Out of Scope:**
- Multi-language support (focus on Python)
- Enterprise deployment details (Kubernetes, cloud providers)
- Advanced CI/CD patterns (deployment strategies, blue-green, canary)
- Security and compliance workflows (defer to production hardening)

**Quality Bar:**
- Commands should be copy-paste ready
- Examples should be concrete and realistic
- Workflows should reference real tools from Session 04
- Each section should have "Prerequisites" and "Expected Output"

**Current Status:**
- ⚠️ DRAFT: Many commands reference tools that will be created in Session 04
- ⚠️ PLACEHOLDER: Specific tool names, flags, and outputs need validation once tools exist
- ⚠️ INCOMPLETE: CI/CD integration needs real GitHub Actions workflow examples

---

## Success Criteria

**By the end of this session, you should have:**

- [ ] Complete setup workflow for each PoC type (Steel Thread, Pragmatic CA, Full CA)
- [ ] Step-by-step testing workflows with example commands and outputs
- [ ] Validation workflows with schema tools and CI integration
- [ ] CLI usage patterns with common scenarios
- [ ] Phase 8 (Agentic/Observability) workflows when implemented
- [ ] PoC evolution workflows with migration checklists
- [ ] Quick reference guide: "Common Commands Cheat Sheet"
- [ ] Troubleshooting section: "Common Issues and Solutions"

---

## Anti-Patterns to Avoid

❌ **Don't document workflows without concrete commands**
   - Why: Developers need runnable examples, not abstract descriptions
   - Instead: Provide exact bash commands with expected output
   - Example: Not "run the tests" but `uv run pytest -q` with sample output

❌ **Don't assume tooling exists that hasn't been created yet**
   - Why: Session 04 is defining the tooling layer
   - Instead: Mark speculative commands as "DRAFT" or "WHEN IMPLEMENTED"
   - Example: Prefix with "⚠️ DRAFT: Once Session 04b scaffolding is complete..."

❌ **Don't create workflows that skip the Outside-In TDD approach**
   - Why: This is core to the Lean-Clean methodology
   - Instead: Always show acceptance test → fakes → real implementation progression
   - Example: Show complete TDD cycle, not just "implement the feature"

❌ **Don't forget the "Why" context**
   - Why: Developers need to understand the reasoning behind workflow steps
   - Instead: Include brief context for each major workflow step
   - Example: "We run acceptance tests first to validate stakeholder requirements"

---

## Suggested Approach

I recommend tackling this in order:

1. **First, document Setup Workflows** (1 hour)
   - Project creation with scaffolding
   - Dependency management
   - Initial configuration
   - Expected outcome: Developer can create new project in 5 minutes

2. **Then, document Development & Testing Workflows** (2 hours)
   - Feature implementation (Outside-In TDD)
   - Test suite usage
   - Common development tasks
   - Expected outcome: Developer can implement first feature following TDD

3. **Next, document Validation & CI Workflows** (1 hour)
   - Schema validation tools
   - Pre-commit hooks
   - CI/CD integration
   - Expected outcome: Developer understands quality gates

4. **Finally, document Advanced Workflows** (1 hour)
   - CLI patterns
   - Phase 8 Agentic/Observability
   - PoC evolution paths
   - Expected outcome: Developer can scale from Steel Thread → Full CA

**Total estimated time:** 4-5 hours (once Session 04 tooling is finalized)

---

## What Good Looks Like

By the end of this session, I should have:

✅ **Complete Developer Handbook**
- [ ] Setup guide for each PoC type
- [ ] Testing workflows with concrete commands
- [ ] Validation and CI integration guide
- [ ] CLI usage patterns
- [ ] Agentic/Observability workflows
- [ ] Evolution/migration guides

✅ **Practical Validation**
- [ ] Every workflow has been tested with real commands
- [ ] Commands produce expected outputs
- [ ] Error scenarios are documented with solutions
- [ ] Integration with Session 04 tools is clear

✅ **Adoption Readiness**
- [ ] Quick start guide: "First PoC in 30 minutes"
- [ ] Common commands cheat sheet
- [ ] Troubleshooting guide
- [ ] Links to relevant Session 02 and Session 04 documentation

✅ **Quality Assurance**
- [ ] CI/CD validation workflows documented
- [ ] Schema validation integrated
- [ ] Pre-commit hooks configured
- [ ] GitHub Actions examples provided

---

## Success Metrics

After this session, a new developer should be able to:

1. **Set up a new project in 5 minutes** - Using scaffolding tools from Session 04b
2. **Implement first feature in 2 hours** - Following Outside-In TDD workflow
3. **Run full test suite in 1 command** - `uv run pytest -q` with clear output
4. **Validate artifacts before commit** - Using validation tools and pre-commit hooks
5. **Understand PoC evolution path** - Know when and how to migrate between PoC types

**Ultimate test:** Could we hand this guide to a Python developer unfamiliar with Lean-Clean and have them successfully deliver a working PoC following these workflows?

---

## Why This Matters

Without clear developer workflows, teams will:
- Waste time figuring out basic setup and commands
- Miss critical validation steps leading to broken artifacts
- Implement features without following Outside-In TDD (losing stakeholder alignment)
- Struggle with PoC evolution (Steel Thread → Pragmatic CA → Full CA)

**The goal:** Make the day-to-day developer experience smooth and productive, so teams focus on building features, not fighting tooling.

With clear workflows, developers can:
- Get productive immediately (no setup friction)
- Follow best practices naturally (TDD, validation, CI/CD)
- Evolve PoCs confidently (clear migration paths)
- Leverage agentic features when ready (observability, automation)

---

## Content from CLAUDE.md (To Be Organized)

### Typical Development Workflow (DRAFT - Needs Session 04 Validation)

When implementing a PoC using this methodology:

#### 1. Setup
```bash
uv sync                          # Install dependencies (when pyproject.toml exists)
```

#### 2. Validation (when schemas exist)
```bash
uv run python tools/validate.py playbook <playbook.yaml>
uv run python tools/validate.py use_case <use-case.yaml>
make validate-playbook           # If Makefile exists
make validate-use-case
```

#### 3. CLI Execution (typical pattern)
```bash
uv run python -m app.cli run --brief ./campaigns/brief.yaml
uv run python -m app.cli validate --brief ./campaigns/brief.yaml
```

#### 4. Testing
```bash
uv run pytest -q                 # Run test suite
```

#### 5. Agentic Watcher (when implemented)
```bash
# With observability enabled
export OBS_ENABLED=1
export PHOENIX_ENDPOINT=http://localhost:6006/api/traces
export ARIZE_ENDPOINT=https://api.arize.com/v1/log
export ARIZE_API_KEY=xxx

uv run python tools/agent_watcher.py \
  --inbox ./briefs/inbox \
  --cmd "uv run python -m app.cli run --brief {brief}" \
  --out-root ./out \
  --alerts-root ./alerts
```

#### 6. Docker Stack (when docker-compose.yaml exists)
```bash
make up            # docker compose up -d
make down          # tear down
make db-init       # create tables in Postgres
make watch         # run watcher locally
make api           # start FastAPI server
```

**Note:** These workflows are proposed patterns documented in the methodology. Actual implementation commands will be finalized through PoC development.

---

### Validation and CI (DRAFT - Needs Session 04 Validation)

**Validation Tools:**
- Schemas validate playbooks, use-cases, and agent tasks
- Validation CLI tool: `tools/validate.py`
- Make targets for common validations

**CI Integration:**
- GitHub Actions workflow (`.github/workflows/validate.yml`) runs validation on CI
- Pre-commit hooks for local validation
- Always validate before committing changes to YAML specs

**CI/CD Pipeline Stages:**
1. **Validation**: Schema validation, linting, type checking
2. **Testing**: Unit, integration, acceptance test suites
3. **Build**: Docker image creation (if applicable)
4. **Deploy**: Deployment to staging/production (Full CA only)

---

## Next Steps

**After Session 04 Completes:**
1. Update all "DRAFT" commands with actual tool names and flags
2. Test every workflow command and document actual outputs
3. Add screenshots or example outputs for key workflows
4. Create "Common Commands Cheat Sheet" reference card
5. Write troubleshooting guide with real error scenarios
6. Validate with a new developer (dogfooding)

**Integration Points:**
- **Session 02 Framework**: Reference folder structures developers navigate
- **Session 04a Playbooks**: Link AI-assisted workflows alongside manual ones
- **Session 04b Scaffolding**: Document the `create_project.py` tool usage
- **Session 04c Generators**: Document artifact generation commands

---

**Note:** This session is marked as DRAFT until Session 04 tooling is finalized. Once tools exist, this should be promoted to a comprehensive developer guide in `/docs/` alongside the framework documentation.
