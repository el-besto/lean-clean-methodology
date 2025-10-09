# How to use day of demo

## recommended day-of workflow

1. sync & validate
    ```bash
    uv sync
    make validate-use-case
    ```

2. drive with Cursor (one task at a time using the prompts above).
   - generate code → run locally → refine 
   - keep an eye on .cursorrules compliance

3. run steel thread
    ```bash
    uv run python -m app.cli run --brief ./campaigns/brief.yaml
    ```
   
4. run acceptance test
    ```bash
    uv run pytest -q 
    ```

5. record demo (5–7 min): show brief → command → outputs → manifest.

6. ensure CI passes (push to GitHub) and include the deck + concise README.