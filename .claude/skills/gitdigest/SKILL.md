---
name: gitdigest
description: >
  Analyze a Git repository or local directory and produce a prompt-friendly text digest.
  Use when the user wants to ingest, summarize, or create a text dump of a codebase for LLM consumption.
  TRIGGER when: user asks to "digest", "ingest", "summarize a repo", "dump a codebase", or wants
  repository contents as text context.
argument-hint: "[source_url_or_path] [options]"
allowed-tools: Bash(pip install *), Bash(pip show *), Bash(python *), Bash(gitingest *), Read, Write
---

# GitDigest Skill

Generate a prompt-friendly text digest from any Git repository URL or local directory path.

## How to use

Parse the user's arguments from `$ARGUMENTS`. Expected formats:

```
/gitdigest                                          # digest current directory
/gitdigest /path/to/local/dir                       # digest a local directory
/gitdigest https://github.com/user/repo             # digest a remote repo
/gitdigest https://github.com/user/repo -b main     # specific branch
/gitdigest . -e "*.test.*" -i "src/**"              # with filters
```

## Steps

1. **Ensure gitingest is installed.** Run:
   ```
   pip show gitingest
   ```
   If not installed, install it:
   ```
   pip install -e /home/user/GitDigest
   ```
   If we are not in the GitDigest repo, install from PyPI:
   ```
   pip install gitingest
   ```

2. **Parse arguments.** Extract from `$ARGUMENTS`:
   - `source` (positional) — URL or local path. Defaults to `.` (current directory) if not provided.
   - `-o` / `--output` — output file path (optional)
   - `-s` / `--max-size` — max file size in bytes (optional)
   - `-e` / `--exclude-pattern` — patterns to exclude (repeatable)
   - `-i` / `--include-pattern` — patterns to include (repeatable)
   - `-b` / `--branch` — branch to analyze (optional)

3. **Run the digest.** Use the Python API for best integration with Claude Code:

   ```python
   python3 -c "
   from gitingest import ingest
   summary, tree, content = ingest('<source>')
   print(summary)
   print()
   print(tree)
   print()
   # Only print content if it's not too large (< 200KB)
   if len(content) < 200_000:
       print(content)
   else:
       print(f'[Content too large to display inline: {len(content):,} chars]')
       print('Writing full output to digest.txt instead...')
       with open('digest.txt', 'w') as f:
           f.write(tree + chr(10) + content)
       print('Done. Full digest written to digest.txt')
   "
   ```

   For more complex invocations with multiple options, use the CLI:
   ```
   gitingest <source> [options]
   ```

4. **Present results.** Show the user:
   - The summary (file count, token estimate, size)
   - The tree structure
   - The content (if small enough) or a note about the output file location

## Important notes

- For remote repos, `git` must be installed and accessible.
- The tool automatically applies smart default ignore patterns (node_modules, __pycache__, .git, etc.).
- Token counts are estimated using tiktoken (cl100k_base encoding).
- Max file size default is 10 MB. Files larger are skipped.
- For very large repos, consider using include/exclude patterns to narrow scope.
