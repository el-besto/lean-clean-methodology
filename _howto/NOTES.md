## Tips 'n Tricks
That will emit `>-` automatically for multiline strings (instead of quoted with backslashes).

```python
yaml.safe_dump(obj, sort_keys=False, allow_unicode=True, default_flow_style=False)
```

## 🧠 Common Syntax Conventions for Agent Compatibility
| Layer                          | Syntax Type                                         | Example                                                 | Why It Helps                                                                         |
|--------------------------------|-----------------------------------------------------|---------------------------------------------------------|--------------------------------------------------------------------------------------|
| **Inline variables**           | `{{variable_name}}`                                 | `{{project_name}}`, `{{timebox_hours}}`                 | Easy token substitution; supports templating and programmatic filling (Jinja-style). |
| **Structured data blocks**     | fenced YAML/JSON                                    | `yaml<br>inputs:<br>  brief_path: ./brief.yaml<br>`     | Fully machine-readable; agents can parse or inject into runtime contexts.            |
| **Phase delimiters**           | markdown headers (`##`, `###`) or explicit comments | `## Phase 1 – Decomposition`                            | Allows chunk-level parsing and indexing (phase = node).                              |
| **Semantic tags**              | key–value pairs or minimal front-matter             | `phase_id: 1`, `status: draft`                          | Useful for versioning and cross-referencing.                                         |
| **Checklist syntax**           | `- [ ] Task description`                            | `- [ ] Validate manifest`                               | Uniform pattern for Boolean extraction and progress tracking.                        |
| **Fenced code for commands**   | triple backticks with language hint                 | `bash<br>python -m app.cli run --brief ./campaign.yaml` | Maintains clarity; lets agents simulate command-line behavior.                       |
| **File sections / boundaries** | comment tokens or fences                            | `# >>> BEGIN: use_case_spec` / `# <<< END`              | Prevents scope bleed when an agent is reading multi-section prompts.                 |
| **Metadata front matter**      | YAML block at top of file                           | `---\nversion: v2\nowner: user\n---`                    | Standard for doc parsing, version control, and pipeline ingestion.                   |

## 🔧 How It Works in the Current Template

In your **Lean-Clean PoC Playbook**, these appear as:

- `{{placeholders}}` → human-editable _and_ template variables for AI agents or automation scripts. 
- Fenced blocks (`yaml`, `mermaid`, `text`) → machine-parseable and executable contexts. 
- Markdown headings (`## 1. Decomposition`) → semantic boundaries agents can chunk by section. 
- Checklists (`- [ ]`) → agents can count/completion-track progress. 
- Tables (pipe-delimited) → parseable by CSV-aware tools or LLM table parsers.


## Explaining `02_lean-clean-agent-validator-bundle-uv`

| Artifact                                                                              | Purpose                                                             | Currently Generic or Adobe-Specific? | Notes                                                                                                                                                                                                                       |
|---------------------------------------------------------------------------------------|---------------------------------------------------------------------|--------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **`Lean-Clean-PoC-Playbook-v2.1-agent-ready.md`**                                     | Markdown playbook template (agent-compatible)                       | **Generic**                          | All placeholders (`{{project_name}}`, `{{goal_1}}`, etc.) are *not yet* filled with Adobe-specific data.                                                                                                                    |
| **Example YAMLs (`lean-clean-playbook.example.yaml`, `use-case.example.yaml`, etc.)** | Demonstrate schema structure                                        | **Partially Adobe-like**             | They reference "social creatives," "hero image," "aspect ratios" — language *inspired* by the Adobe FDE exercise, but not explicitly tied to Adobe or its brands. These examples can therefore serve as **base scaffolds**. |
| **Scenario PDF (`_scenario-and-deliverables.pdf`)**                                   | Source of truth for business context, deliverables, and constraints | **Not yet fully parsed or injected** | The earlier DOCX playbook included brief excerpts from it ("Global CPG brand launching localized social ads…"), but the YAML + markdown examples are **not yet populated** from the PDF.                                    |
| **Schemas & Validator bundle**                                                        | Tooling / CI integration                                            | **Generic**                          | No scenario content; purely structural.                                                                                                                                                                                     |


## Explaining `03_adobe-creative-automation-poc-expanded`
| File                                      | Description                                                                  |
|-------------------------------------------|------------------------------------------------------------------------------|
| `playbook.adobe-creative-automation.yaml` | Full Lean-Clean PoC playbook (phases 0–7, decisions, metrics, tasks).        |
| `use-case.adobe-creative-automation.yaml` | Executable spec (Firefly integration, 3 aspect ratios, manifest validation). |
| `README.adobe-poc.md`                     | Plain-English summary & quickstart for the PoC.                              |

## Explaining `04_adobe-creative-automation-poc-concise`
| File                                              | Description                                                              |
|---------------------------------------------------|--------------------------------------------------------------------------|
| `playbook.adobe-creative-automation.concise.yaml` | Minimal Lean-Clean playbook (only critical phases & deliverables).       |
| `use-case.adobe-creative-automation.concise.yaml` | Executable spec for the PoC steel thread (brief → creatives → manifest). |
| `README.adobe-poc.concise.md`                     | Simple overview + quickstart instructions for running the PoC.           |

## Explaining `lean-clean-framework-v2.2-agentic` & `adobe-creative-automation-poc-with-agentic`
- Generic v2.2 with Agentic + Observability:
  - 📦 lean-clean-framework-v2.2-agentic.zip 
- Adobe scenario with Agentic + Observability:
  - 📦 adobe-creative-automation-poc-with-agentic.zip

**What changed**
1) New code: `app/utils/observability.py`
   - Lightweight HTTP emitters (safe stubs):
     - send_to_phoenix(trace: dict) -> bool 
     - send_to_arize(record: dict) -> bool 
   - Disabled by default; enable with env var OBS_ENABLED=1. 
   - Uses env vars:
     - PHOENIX_ENDPOINT (e.g., http://localhost:6006/api/traces)
     - ARIZE_ENDPOINT (e.g., https://api.arize.com/v1/log)
     - ARIZE_API_KEY

2) Watcher emits traces
   - `tools/agent_watcher.py` now calls the emitters on detection, success, failure, and alert creation. 
   - Continues to generate alert markdowns when < 3 variants are found.

3) Updated docs
   - `README-agentic.md` (generic) and `README-agentic.adobe.md` document:
     - How to enable observability and set env vars 
     - How to run the watcher with uv

4) Dependencies updated

- Generic `pyproject.toml` now includes:
  - watchdog (file watching)
  - requests (HTTP emission)
  - PyYAML, jsonschema (unchanged)

Quickstart (observability on)
```bash
uv sync
export OBS_ENABLED=1
export PHOENIX_ENDPOINT=http://localhost:6006/api/traces       # placeholder example
export ARIZE_ENDPOINT=https://api.arize.com/v1/log             # placeholder example
export ARIZE_API_KEY=xxx
uv run python tools/agent_watcher.py \
  --inbox ./briefs/inbox \
  --cmd "uv run python -m app.cli run --brief {brief}" \
  --out-root ./out \
  --alerts-root ./alerts
```

## "Defined App Skeleton"
> (from the "Functional PoC execution plan" stage):

```text
app/
├─ cli.py                 # entrypoint (argparse/typer)
├─ core/                  # domain logic
│  ├─ models.py           # Brief, Product, Brand, etc.
│  └─ use_cases.py        # generate_creatives_for_brief()
├─ adapters/              # ports/adapters
│  ├─ storage_local.py    # read/write assets/out/manifest
│  ├─ imagegen_firefly.py # placeholder client interface
│  └─ compose.py          # Pillow/OpenCV text+logo overlay
└─ utils/
   ├─ brief_loader.py     # YAML load/validate
   └─ manifest.py         # write manifest.json
```

### Dockerfile from UI
```dockerfile
# syntax=docker/dockerfile:1.6
FROM ghcr.io/astral-sh/uv:python3.11-bookworm AS base
WORKDIR /app
COPY pyproject.toml ./
COPY app ./app
COPY tools ./tools
RUN uv sync --frozen

CMD ["uv", "run", "python", "tools/agent_watcher.py", "--inbox", "./briefs/inbox",
     "--cmd", "uv run python -m app.cli run --brief {brief}",
     "--out-root", "./out", "--alerts-root", "./alerts"]
```