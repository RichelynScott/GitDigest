# GitDigest (Gitingest)

Turn any Git repository into a prompt-friendly text digest for LLMs.

## Project Overview

GitDigest (package name: `gitingest`) analyzes codebases and produces structured text output containing:
- A summary with file/directory statistics and token count estimates
- A tree-like representation of the file structure
- The concatenated contents of all matched files

It supports Git repository URLs (GitHub, GitLab, Bitbucket, etc.) and local directory paths.

## Project Structure

- `src/gitingest/` - Core Python package
  - `entrypoint.py` - Main API: `ingest()` and `ingest_async()`
  - `cli.py` - CLI entry point (Click-based)
  - `ingestion.py` - Core file traversal and ingestion logic
  - `query_parsing.py` - URL/path parsing into `IngestionQuery`
  - `cloning.py` - Git clone operations
  - `output_formatters.py` - Summary, tree, and content formatting
  - `config.py` - Constants (MAX_FILE_SIZE, MAX_FILES, etc.)
  - `filesystem_schema.py` - FileSystemNode dataclass
  - `utils/` - Ignore patterns, text detection, notebook support
- `src/server/` - FastAPI web application (gitingest.com)
- `tests/` - Pytest test suite

## Development

```bash
# Install in editable mode
pip install -e .

# Install dev dependencies
pip install -r requirements-dev.txt

# Run tests
pytest

# Lint
pylint src/
black --check src/
isort --check src/
```

## Key APIs

```python
from gitingest import ingest

# Returns (summary, tree, content)
summary, tree, content = ingest("https://github.com/user/repo")
summary, tree, content = ingest("/path/to/local/dir")
```

## CLI

```bash
gitingest <source> [-o output] [-s max-size] [-e exclude] [-i include] [-b branch]
```

## Configuration

- Max line length: 119 (black, pylint, isort)
- Test runner: pytest with asyncio auto mode
- Python path for tests: `src/`
- Package uses setuptools with `pyproject.toml`
