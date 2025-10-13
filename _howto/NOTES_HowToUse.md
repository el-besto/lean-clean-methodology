# How to use these assets to deliver the PoC

## 1) repo bootstrap (uv + validator + examples already provided)

1. create a fresh repo and drop in the UV bundle + Adobe examples you downloaded:
    ```text
    repo/
    ├─ pyproject.toml
    ├─ uv.lock                 # regenerate later with `uv lock`
    ├─ Makefile
    ├─ README-validator.md
    ├─ tools/validate.py
    ├─ .github/workflows/validate.yml
    ├─ *.schema.yaml           # playbook / agent_task / use_case schemas
    ├─ adobe-example*/         # expanded + concise zips content
    └─ docs/                   # (optional) paste your deck.md here
    ```

2. install deps (same in dev/CI/prod): 
    ```bash
    uv sync
    ```

3. sanity-check the example artifacts:

    ```bash
    uv run python tools/validate.py playbook adobe-example/playbook.adobe-creative-automation.yaml
    uv run python tools/validate.py use_case adobe-example/use-case.adobe-creative-automation.yaml
    ```

## 2) minimal app skeleton (steel thread)

> add a tiny, clean-architecture-ish structure:

```text
app/
├─ cli.py                 # entrypoint (argparse/typer)
├─ core/                  # domain logic
│  ├─ models.py           # Brief, Product, Brand, etc.
│  └─ use_cases.py        # generate_creatives_for_brief()
├─ adapters/              # ports/adapters
│  ├─ storage_local.py    # read/write assets/out/manifest
│  ├─ imagegen_firefly.py # placeholder client interface
│  └─ compose.py          # pillow/OpenCV text+logo overlay
└─ utils/
   ├─ brief_loader.py     # yaml load/validate
   └─ manifest.py         # write manifest.json
```

**commands:**
```bash
uv run python -m app.cli run --brief ./campaigns/brief.yaml
```

**first pass goals**

- parse brief → validate → iterate products 
- locate existing hero or (stub) generate 
- compose 3 aspect ratios with text & logo 
- write outputs and a manifest.json

> keep all I/O wired to ./assets, ./out, and keep provider code behind an adapter (so "generation" can be a local placeholder at first, then swapped to Firefly).
 
## 3) connect the use-case spec to tests
your `use-case.*.yaml` is executable **acceptance**. write a tiny test harness:
```text
tests/
└─ test_acceptance.py
```

```python
import json, yaml, subprocess, pathlib

def test_generate_creatives_for_brief():
    spec = yaml.safe_load(pathlib.Path("adobe-example-concise/use-case.adobe-creative-automation.concise.yaml").read_text())
    brief = spec["inputs"]["brief_path"]
    cmd = ["uv", "run", "python", "app/cli.py", "--brief", brief]
    subprocess.run(cmd, check=True)
    # assert manifest(s) and output files exist per acceptance_criteria...

```

**run:**
```bash
uv run pytest -q
```

## 4) use the schemas + validator in your inner loop
- when you tweak *playbook/use-case yaml*, run
    ```bash
    make validate-playbook
    make validate-use-case
    ```
- commit early; CI will run the same validation in GH Actions.

## 5) PyCharm run/debug config

- add a **Run Configuration:**
    ```text
    Module name: app.cli
    Parameters: run --brief ./campaigns/brief.yaml
    ```
- set **Working directory** to repo root. 
- enable **pytest** in PyCharm and run `tests/`.

## 6) Cursor IDE: how to get leverage (without chaos)

Cursor shines when you give it **rules, ground truth files**, and **narrow tasks**. We’ll set three things:

- a `.cursorrules` file (scope, constraints, file map)
- a **"project system prompt"** (short, always-on)
- **task prompts** templated from your agent_task blocks and acceptance criteria

then, use the **Composer/Agent** to implement each epic/task in small slices.

### suggested Cursor artifacts

see [`cursor/suggested_artifacts.md`](cursor/suggested_artifacts.md) for a full example. Here’s the gist:
