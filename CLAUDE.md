# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Commands

```bash
# Install dependencies
uv sync

# Run the CLI
uv run second_brain new "My thought"
uv run second_brain list
uv run second_brain show 1

# Run with a dev .env file
uv run --env-file .env second_brain new "Some thought"

# Tests
uv run pytest
uv run pytest --cov
uv run pytest tests/test_notes.py::test_name   # single test

# Lint / format
uv run ruff check src tests
uv run ruff format src tests

# Docs (preview)
uv run python scripts/serve_docs.py
```

## Architecture

The app is a thin CLI wrapper around pure-Python note logic:

- **`cli.py`** — Click entry point. Reads `SECOND_BRAIN_DIR` from env, delegates to `notes.py`. Calls `configure_logging()` at group level.
- **`notes.py`** — All note business logic: `slugify`, `build_note_path` (handles duplicate filenames with `-1`, `-2` suffixes), `create_note`.
- **`app.py`** — Loguru setup only. `configure_logging()` reads `LOG_LEVEL` and `LOG_FILE` from env; console uses compact 3-letter level codes.

## Environment Variables

| Variable | Default | Purpose |
|---|---|---|
| `SECOND_BRAIN_DIR` | `~/second_brain/` | Where notes are stored |
| `LOG_LEVEL` | `INFO` | Console log level |
| `LOG_FILE` | `$SECOND_BRAIN_DIR/app.log` | Log file path |

Copy `.env.example` → `.env` for local dev. Tests use `.env.test` (loaded automatically via `pytest-env`). The `tmp_note_dir` fixture in `conftest.py` redirects `SECOND_BRAIN_DIR` to a temp dir for isolation.
