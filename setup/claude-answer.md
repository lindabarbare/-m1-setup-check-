`make test` runs a single command, defined in the `Makefile`:

```make
test:
	pytest -q
```

It runs pytest in quiet mode (`-q`) from the repo root. There's no `pytest.ini`, `pyproject.toml` or `setup.cfg`, so pytest uses its default discovery and finds the one test file, `tests/test_smoke.py`. That file has two smoke tests:

1. **`test_openapi_document_can_be_loaded`** opens `docs/openapi.yaml` with `yaml.safe_load` and checks that:
   - the `openapi` field starts with `"3."` (it's an OpenAPI 3.x document)
   - `paths` is not empty

2. **`test_participant_files_are_present`** checks that these files exist:
   - `.claude/settings.json`
   - `.devcontainer/devcontainer.json`
   - `CLAUDE.md`
   - `Makefile`
   - `tracker/CR-2.md`
   - `tracker/README.md`

   If any are missing, it fails with "Trūkst faili: …" ("Missing files: …") and lists them.

**What it doesn't check:**
- **API behaviour.** The tests only confirm the spec loads and has the right basic shape. They don't validate it or check any endpoints.
- **Contract rules.** Those are checked by `make lint-contract`, which runs `tools/lint_contract.py docs/openapi.yaml`. Per `CLAUDE.md`, run it as well whenever you change the contract.
- **Your setup.** That's `make verify-setup`. It checks the Python packages (including Pydantic v2), the `claude` CLI and `setup/claude-answer.md`, then runs the same smoke tests.
