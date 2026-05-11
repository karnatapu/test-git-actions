# AGENTS.md

## Project at a glance
- Small FastAPI service that exposes the contents of `data.json`.
- `main.py` loads the JSON file at import time, so the app must be started from the project root (or any CWD that still resolves `data.json`).
- Two read-only routes exist:
  - `GET /` returns the full JSON array.
  - `GET /{guid}` scans the loaded list and returns the matching record or raises `HTTPException(404)`.

## Core data shape
- `data.json` is a static array of school records.
- Each record uses the same fields in the app and tests: `guid`, `school`, `mascot`, `nickname`, `location`, `latlong`, `ncaa`, `conference`.
- Tests assume the first array element is a valid lookup target, so preserve ordering if you edit the sample data.

## Where to look first
- `main.py` for API behavior and response shape.
- `main_test.py` for the expected contract; it compares responses against the exact JSON data loaded from disk.
- `Makefile` and `python-ci-workflow.yml` for the supported local/CI commands.
- `README.md` for the existing CI/JUnit reporting notes.

## Developer workflow
- Install dependencies: `make setup`
- Run tests: `make test` or `python -m pytest --verbose --junit-xml=junit.xml`
- Run the API locally: `make run` (starts `uvicorn main:app --host 0.0.0.0 --port 3000 --reload`)
- Lint/format checks in this repo are Makefile-driven:
  - `make fmt` uses `black --target-version=py311 --check --diff *.py || true`
  - `make lint` uses `flake8 --max-line-length=88 --ignore=E203,W503`

## CI behavior
- GitHub Actions workflow is `python-ci-workflow.yml`.
- CI runs on pushes/PRs to `main`, uses Python 3.10, installs `flake8` + `pytest` plus `requirements.txt`, and publishes `junit.xml` with `mikepenz/action-junit-report@v3`.
- The workflow sets `checks: write`, so keep JUnit output enabled if you change the test command.

## Conventions to preserve
- Keep the API simple and synchronous in structure; the current app is just file-backed JSON read at startup.
- If you change the data file path or startup behavior, update `main_test.py` and the README/Makefile instructions together.
- Prefer edits that keep the exact response payloads stable; the tests use full equality on JSON bodies.

