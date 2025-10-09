# Suggested Cursor artifacts


#### A) `.cursorrules` (drop at repo root)
  
how to use in Cursor: open .cursorrules; Cursor will auto-apply guidance during generations.

#### B) "Project System Prompt" (pin in a doc or Cursor notes)

```text
You are a meticulous engineer implementing ONLY the steel thread for an Adobe Creative Automation PoC.
Honor the .cursorrules constraints and acceptance criteria in the use-case YAML.
Prefer small, composable functions. Write minimal code to pass the acceptance test and CLI run.
Never invent missing requirements—defer or stub behind adapters. Update README if tradeoffs occur.
```

#### C) MCP / tool assumptions (optional)

If you’re using Cursor with MCP servers (filesystem, shell, http):

- **filesystem:** read/write precise paths only under ./assets, ./out, ./campaigns, and ./brand. 
- **shell:** allow `uv` commands and `pytest`. 
- **http:** if you wire Firefly later, gate keys via env and keep all http calls inside `adapters/imagegen_firefly.py`.

You can keep an MCP “policy” snippet for the agent (paste into a note):

```yaml
mcp_policy:
  allow_shell:
    - "uv sync"
    - "uv run *"
    - "pytest *"
  deny_shell:
    - "rm -rf /"
    - "curl | bash"
  fs_roots:
    - "./assets"
    - "./out"
    - "./campaigns"
    - "./brand"
    - "./app"
  http_domains_allowlist:
    - "api.firefly.adobe.com"
```

#### D) task prompt templates (copy/paste into Cursor when coding)

##### 1) implement brief loader + schema check
```md
Task: Implement brief loader and validator.
Inputs: ./campaigns/brief.yaml (YAML), ./use-case-spec.schema.yaml (fields reference).
Definition of Done:
- Function load_and_validate_brief(path) returns a structured object.
- Reject invalid briefs with clear error messages.
- Unit test covers happy path + invalid keys.
Run:
- uv run python -m app.cli validate --brief ./campaigns/brief.yaml
- uv run pytest -q
```

##### 2) implement local asset scanner + image generation stub
```md
Task: Implement asset scanner and image generation adapter (stub).
Goal: If hero exists under ./assets/{product}.*, use it; else generate placeholder via adapter.
Definition of Done:
- scan_hero_image(product) returns a path or None.
- imagegen.generate_hero(product, brief, seed) returns path to ./cache/*.jpg (stub ok).
- Update README: how to replace stub with Firefly.
```

##### 3) implement compositor (text + logo + ratios)
```md
Task: Compose final creatives for 1:1, 9:16, 16:9 with text overlay + logo.
Constraints: Use Pillow; ensure text contrast and logo placement are legible.
Definition of Done:
- compose(product, hero, brief, brand) returns dict{ratio: output_path}
- Layout works for both wide and tall hero images.
- Add small heuristic: if text contrast low, draw a translucent panel behind text.
```

##### 4) implement manifest writer + CLI glue
```md
Task: Write manifest.json and wire CLI `run`.
Definition of Done:
- manifest captures inputs, outputs, timestamps.
- CLI command: `uv run python -m app.cli run --brief ./campaigns/brief.yaml`
- Acceptance criteria pass for the concise use-case YAML.
```

#### E) acceptance checklist prompt (paste before final run)
```md
Acceptance Checklist (must be true):
- [ ] 3 creatives per product exist (1:1, 9:16, 16:9)
- [ ] Logo and brand color applied
- [ ] manifest.json present with inputs/outputs/timestamps
- [ ] CLI command succeeds without manual intervention
- [ ] README quickstart is accurate
```