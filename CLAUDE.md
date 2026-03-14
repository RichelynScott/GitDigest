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

## Installing the GitDigest Skill into Claude Code

The `/gitdigest` skill lives in `.claude/skills/gitdigest/` in this repo. To install it as a **personal skill** available across all your projects:

### Quick install (copy)

```bash
# Clone this repo (if you haven't already)
git clone https://github.com/RichelynScott/GitDigest.git

# Copy the skill into your personal Claude Code skills directory
cp -r GitDigest/.claude/skills/gitdigest ~/.claude/skills/
```

### One-liner (no clone needed)

```bash
mkdir -p ~/.claude/skills/gitdigest && \
curl -sL https://raw.githubusercontent.com/RichelynScott/GitDigest/main/.claude/skills/gitdigest/SKILL.md \
  -o ~/.claude/skills/gitdigest/SKILL.md
```

### Verify installation

```bash
cat ~/.claude/skills/gitdigest/SKILL.md
```

You should see the YAML frontmatter with `name: gitdigest` at the top.

### Install the gitingest Python package

The skill will auto-install this on first run, but you can pre-install it:

```bash
# From PyPI
pip install gitingest

# Or from a local clone of this repo (editable mode)
pip install -e /path/to/GitDigest
```

### How it works once installed

- **Personal skills** in `~/.claude/skills/` are available in every Claude Code session on your machine.
- Invoke it with `/gitdigest` in any Claude Code conversation.
- Claude will also auto-trigger it when you ask to "digest a repo", "ingest a codebase", etc.

### Skill scope reference

| Location | Path | Scope |
|----------|------|-------|
| Personal (recommended) | `~/.claude/skills/gitdigest/SKILL.md` | All your projects |
| Project-local | `<project>/.claude/skills/gitdigest/SKILL.md` | That project only |

### Updating the skill

To pull the latest version of the skill after updates:

```bash
# If you cloned the repo
cd /path/to/GitDigest && git pull
cp -r .claude/skills/gitdigest ~/.claude/skills/

# Or re-run the one-liner above
```
