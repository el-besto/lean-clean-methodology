# Session 02: Decision 9 (Drivers Layer) - Resolution Context

**Date:** 2025-10-13
**Status:** ✅ RESOLVED
**Impact:** HIGH - Affects project structure, entry points, DI strategy

---

## Decision Summary

**Question:** Should we have an explicit `drivers/` layer?

**Resolution:** ✅ **Progressive Evolution with Enterprise Reality** - drivers/ folder from Steel Thread with CLI + UI always, FastAPI when needed

---

## Critical Enterprise PoC Reality

From workshop context analysis, we discovered:
> "Tests and code are written WITH stakeholders present in 90-minute workshops."

**Key Insight:** Even Steel Thread needs `drivers/` folder because enterprise AI PoCs require BOTH automation (CLI) AND visual feedback (Streamlit UI) from day 1.

### Stakeholder Entry Point Requirements

- ✅ **CLI (ALWAYS)** - Automation, reproducibility, CI/CD integration
- ✅ **Streamlit UI (ALWAYS)** - Visual feedback for non-technical stakeholders
  - Creative Lead: Review campaign images
  - Legal/Compliance: Approve/reject content
  - Ad Operations: Monitor performance metrics
  - IT Architect: System health dashboard
- ⚠️ **FastAPI (WHEN NEEDED)** - Enterprise system integrations, webhooks, external access

**Why this matters:** Traditional PoCs start with "just a CLI." Enterprise multi-stakeholder PoCs need UI from day 1 for stakeholder engagement in workshops.

---

## Progressive Evolution Structures

### **Steel Thread (1-2 days): Minimal drivers/ with CLI + UI**

```
project/
├─ app/
│  ├─ entities/
│  │  └─ campaign.py                  # Domain model
│  ├─ use_cases/
│  │  └─ generate_campaign_uc.py      # Business logic
│  └─ adapters/
│     ├─ imagegen/
│     │  ├─ protocol.py               # IImageGenerator interface
│     │  ├─ fake.py                   # Fast fake for workshops
│     │  └─ openai.py                 # Real implementation
│     └─ storage/
│        ├─ protocol.py               # IStorageAdapter interface
│        ├─ fake.py                   # In-memory
│        └─ local.py                  # Filesystem
│
├─ drivers/                            # ← YES, even Steel Thread
│  ├─ cli.py                           # Automation, reproducibility
│  └─ ui/
│     └─ streamlit_app.py              # Stakeholder visual demos
│
└─ .streamlit/
   └─ config.toml                      # Streamlit configuration
```

**Why drivers/ from the start:**
- CLI + UI = 2 entry points → folder organization warranted
- Stakeholders need visual feedback (not just CLI logs)
- Clear mental model: "drivers call use cases"
- No refactoring when adding FastAPI later

**Code Examples:**

```python
# drivers/cli.py - Direct DI
import typer
from app.use_cases.generate_campaign_uc import GenerateCampaignUseCase
from app.adapters.imagegen.fake import FakeImageGenerator

app = typer.Typer()

@app.command()
def run(brief_path: str):
    uc = GenerateCampaignUseCase(
        image_gen=FakeImageGenerator(),  # Fake for speed
        storage=LocalStorage()
    )
    result = uc.execute(load_brief(brief_path))
    typer.echo(f"✓ Generated {len(result.campaigns)} campaigns")
```

```python
# drivers/ui/streamlit_app.py - Stakeholder Demo
import streamlit as st
from app.use_cases.generate_campaign_uc import GenerateCampaignUseCase
from app.adapters.imagegen.fake import FakeImageGenerator

st.title("🎨 Localized Campaign Generator")

# Creative Lead inputs
brand_colors = st.color_picker("Primary Brand Color", "#FF6B35")
tone = st.selectbox("Tone", ["energetic", "premium", "playful"])

if st.button("Generate Summer Sale Campaign"):
    uc = GenerateCampaignUseCase(
        image_gen=FakeImageGenerator(),  # Fast for workshop
        storage=LocalStorage()
    )

    with st.spinner("Generating campaigns..."):
        result = uc.execute(sample_brief)

    # Show to Creative Lead
    for campaign in result.campaigns:
        st.image(campaign.image_url)
        st.caption(f"Market: {campaign.market}")
```

---

### **Pragmatic CA (3-5 days): Organized drivers/ with orchestrators**

```
project/
├─ app/
│  ├─ entities/                        # Domain models
│  ├─ use_cases/                       # Business logic
│  ├─ interface_adapters/
│  │  ├─ orchestrators/
│  │  │  └─ campaign_orchestrator.py  # Coordinates use cases
│  │  └─ presenters/
│  │     └─ campaign_presenter.py     # Format outputs
│  ├─ adapters/                        # External services
│  │  ├─ imagegen/
│  │  ├─ storage/
│  │  └─ events/
│  └─ infrastructure/                  # Persistence (if needed)
│     └─ repositories/
│        └─ campaign/
│
├─ drivers/
│  ├─ cli/
│  │  ├─ __main__.py                   # Entry point: python -m drivers.cli
│  │  ├─ commands.py                   # Typer commands organized
│  │  └─ dependencies.py               # DI container for CLI
│  │
│  ├─ rest/                             # ← Added when integrating with enterprise
│  │  ├─ main.py                       # FastAPI app
│  │  ├─ routers/
│  │  │  └─ campaigns.py               # Campaign endpoints
│  │  ├─ schemas/                      # Pydantic request/response
│  │  │  ├─ campaign_request.py
│  │  │  └─ campaign_response.py
│  │  └─ dependencies.py               # FastAPI DI
│  │
│  └─ ui/
│     └─ streamlit/
│        ├─ app.py                     # Main Streamlit entry
│        ├─ pages/                     # Multi-page app
│        │  ├─ 1_generate.py          # Creative Lead: Generate campaigns
│        │  └─ 2_approve.py           # Legal: Approve content
│        └─ dependencies.py            # Streamlit DI
│
└─ .streamlit/
   └─ config.toml
```

**When to add FastAPI:**
- Integration with enterprise DAM system
- External stakeholders need API access
- Webhook receivers for approval workflows
- Async job processing

**Code Examples:**

```python
# drivers/rest/routers/campaigns.py
from fastapi import APIRouter, Depends
from drivers.rest.schemas.campaign_request import CampaignRequest
from drivers.rest.dependencies import get_orchestrator

router = APIRouter()

@router.post("/campaigns")
async def create_campaign(
    request: CampaignRequest,
    orchestrator = Depends(get_orchestrator)  # Calls orchestrator, not use case
):
    result = await orchestrator.generate(request)
    return {"campaign_id": result.id, "status": "processing"}
```

```python
# drivers/ui/streamlit/pages/1_generate.py
import streamlit as st
from drivers.ui.streamlit.dependencies import get_orchestrator

st.title("🎨 Generate Campaigns")

# Creative Lead inputs
brand_colors = st.color_picker("Primary Brand Color")
tone = st.selectbox("Tone", ["energetic", "premium", "playful"])

if st.button("Generate"):
    orchestrator = get_orchestrator()

    # Show progress to stakeholders
    with st.status("Generating campaigns...") as status:
        st.write("🎨 Generating images...")
        st.write("✅ 5 markets created")

        result = orchestrator.generate(request)
        status.update(label="Complete!", state="complete")

    # Creative Lead reviews
    st.subheader("Review Campaigns")
    for campaign in result.campaigns:
        col1, col2 = st.columns(2)
        with col1:
            st.image(campaign.image)
        with col2:
            st.write(f"**Market:** {campaign.market}")
            st.button("✓ Approve", key=campaign.id)  # Legal reviews
```

---

### **Full CA (Production): Complete drivers/ layer**

```
drivers/
├─ cli/
│  ├─ __main__.py                      # Entry point
│  ├─ commands/                        # Organized commands
│  │  ├─ generate.py                   # Campaign generation
│  │  ├─ validate.py                   # Brief validation
│  │  └─ approve.py                    # Approval workflow
│  ├─ formatters/                      # Output formatters
│  │  ├─ table.py                      # Rich table output
│  │  └─ json.py                       # JSON output
│  └─ dependencies.py                  # DI container
│
├─ rest/
│  ├─ main.py                          # FastAPI app
│  ├─ routers/
│  │  ├─ campaigns.py                  # Campaign endpoints
│  │  ├─ approvals.py                  # Approval endpoints
│  │  └─ webhooks.py                   # External webhooks
│  ├─ schemas/                         # Pydantic models
│  ├─ middleware/                      # Cross-cutting concerns
│  │  ├─ auth.py                       # Enterprise SSO
│  │  ├─ logging.py                    # Request logging
│  │  └─ telemetry.py                  # Phoenix/Arize instrumentation
│  ├─ exception_handlers.py            # Error handling
│  └─ dependencies.py                  # FastAPI DI
│
├─ ui/
│  └─ streamlit/
│     ├─ app.py                        # Main entry
│     ├─ pages/                        # Multi-stakeholder pages
│     │  ├─ 1_generate.py             # Creative Lead: Generate
│     │  ├─ 2_approve.py              # Legal: Approve
│     │  ├─ 3_monitor.py              # IT: Dashboard
│     │  └─ 4_compliance.py           # Legal: Compliance review
│     ├─ components/                   # Reusable UI components
│     │  ├─ campaign_card.py
│     │  └─ approval_widget.py
│     └─ dependencies.py               # Streamlit DI
│
└─ graphql/                             # Optional: If enterprise needs GraphQL
   ├─ schema.py
   ├─ resolvers/
   └─ main.py
```

---

## Migration Paths

### **Steel Thread → Pragmatic CA**

**When:** Stakeholder feedback needs more structure, or integrating with other systems

**Changes:**
1. Add orchestrators (`app/interface_adapters/orchestrators/`)
2. Organize CLI into commands (`drivers/cli/commands/`)
3. Add FastAPI if needed (`drivers/rest/`)
4. Expand Streamlit to multi-page (`drivers/ui/streamlit/pages/`)
5. Add DI containers (`dependencies.py` in each driver)

**Migration Steps:**
```bash
# 1. Create orchestrator layer
mkdir -p app/interface_adapters/orchestrators

# 2. Organize CLI
mkdir drivers/cli/commands
mv drivers/cli.py drivers/cli/__main__.py

# 3. Add FastAPI (if needed)
mkdir -p drivers/rest/{routers,schemas}

# 4. Expand Streamlit
mkdir drivers/ui/streamlit/pages
mv drivers/ui/streamlit_app.py drivers/ui/streamlit/app.py
```

**Key:** No file moves between layers - just organizational nesting.

---

### **Pragmatic CA → Full CA**

**When:** Production deployment, enterprise security requirements, complex workflows

**Changes:**
1. Add middleware (`drivers/rest/middleware/`)
2. Add exception handlers (`drivers/rest/exception_handlers.py`)
3. Expand CLI with formatters (`drivers/cli/formatters/`)
4. Add Streamlit components (`drivers/ui/streamlit/components/`)
5. Add additional driver types (GraphQL) if needed

**Migration Steps:**
```bash
# 1. Add middleware
mkdir drivers/rest/middleware

# 2. Add CLI formatters
mkdir drivers/cli/formatters

# 3. Add Streamlit components
mkdir drivers/ui/streamlit/components

# 4. Add additional drivers (if needed)
mkdir drivers/graphql
```

---

## Dependency Injection Evolution

### **Steel Thread: Direct Instantiation**
```python
# drivers/cli.py
uc = GenerateCampaignUseCase(
    image_gen=FakeImageGenerator(),
    storage=LocalStorage()
)
```

### **Pragmatic CA: Dependencies Module**
```python
# drivers/cli/dependencies.py
def build_orchestrator() -> CampaignOrchestrator:
    config = load_config()

    # Adapters (fake or real based on config)
    image_gen = (
        FakeImageGenerator() if config.use_fakes
        else OpenAIImageGenerator()
    )

    # Use Cases
    generate_uc = GenerateCampaignUseCase(image_gen=image_gen)

    # Orchestrator
    return CampaignOrchestrator(generate_uc=generate_uc)

# drivers/cli/commands.py
@app.command()
def run(brief: str):
    orchestrator = build_orchestrator()
    result = orchestrator.generate(...)
```

### **Full CA: Dependency Injection Container**
```python
# drivers/rest/dependencies.py
from dependency_injector import containers, providers

class Container(containers.DeclarativeContainer):
    config = providers.Configuration()

    # Adapters
    image_gen = providers.Singleton(
        OpenAIImageGenerator,
        api_key=config.openai_api_key
    )

    # Use Cases
    generate_uc = providers.Factory(
        GenerateCampaignUseCase,
        image_gen=image_gen
    )

    # Orchestrators
    campaign_orchestrator = providers.Factory(
        CampaignOrchestrator,
        generate_uc=generate_uc
    )
```

---

## Evaluation Against Criteria

### ✅ **Progressive Evolution**
- Steel Thread: `drivers/` with CLI + UI (flat)
- Pragmatic CA: `drivers/cli/`, `drivers/rest/`, `drivers/ui/` (organized)
- Full CA: Complete drivers with middleware, components (production-ready)

### ✅ **Stakeholder Workshop Clarity**
- CLI for reproducibility ("run this command to regenerate")
- Streamlit UI for visual feedback (Creative Lead sees images)
- FastAPI when needed (integration with enterprise systems)

### ✅ **Consistency with Axioms**
- Axiom 2 (Orchestrator-Centric): All drivers call orchestrators from Pragmatic CA+
- Drivers are outermost layer (matches Clean Architecture)

### ✅ **Pragmatic vs. Dogmatic**
- Recognizes enterprise reality: PoCs need UI from day 1
- FastAPI optional until needed (not every PoC needs REST API)
- Streamlit chosen for speed (not React/Vue complexity)

---

## Documents Updated

1. **`/plan/_session-02-decisions-needed.md`**
   - Added comprehensive Decision 9 resolution (428 lines)
   - Updated status to "10 of 12 decisions resolved"

2. **`/plan/_session-02-resume-prompt-understanding.md`**
   - Updated "What's Been Decided" with Decisions 8-10
   - Added Decision 9 insight about enterprise PoC reality

3. **`/lean-clean-blog/drafts/_wip-lean-clean-the-secret-sauce.md`**
   - Updated architecture diagram to show Drivers layer
   - Added CLI + Streamlit UI + FastAPI explanation
   - Clarified stakeholder visual feedback needs

---

## Key Takeaways for Implementation

1. **Always start with drivers/** - Even Steel Thread needs it for CLI + UI
2. **Streamlit UI is non-negotiable** - Stakeholders need visual feedback
3. **FastAPI is optional** - Add only when integrating with enterprise systems
4. **Progressive nesting** - Files don't move between layers, just nest deeper
5. **DI evolves with complexity** - Direct → module → container

---

## Next Steps

**Remaining Decisions:**
- Decision 11: DTO Layer Strategy
- Decision 12: Ports Location/Naming (partially addressed by Decision 10)

**After Decisions Complete:**
- Update framework document with resolved structures
- Ensure consistency across all three PoC types
- Commit all Session 02 work
