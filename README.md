# Agentic Coding Skills

Coding skills for agentic engineering workflows. A collection of specialized tools and configurations for AI-assisted software development.

## Overview

This repository contains custom skills for AI coding agents, designed to enhance productivity in software engineering tasks. Skills follow the OpenClaw AgentSkills specification and integrate with tools like Claude Code, Gemini CLI, and complexity-accounting.

## Structure

```
.
├── local-repo-sanitizer/  # Automated repo maintenance and health checks
│   ├── SKILL.md           # Skill definition and workflow
│   └── config.json        # Repository list configuration
├── refactor-complexity/   # Complexity-driven iterative refactoring skill
│   └── SKILL.md           # Skill definition and usage instructions
└── LICENSE
```

## Available Skills

### local-repo-sanitizer

Automated maintenance for configured GitHub repositories. Deep-analyzes and updates READMEs, ensures all tests are covered in CI, and fixes broken GitHub Actions workflows.

**Use when:** You want to sanitize repos, check repo health, or run repo maintenance across multiple repositories.

**Configuration:** Edit `local-repo-sanitizer/config.json` to define the list of repositories to process. Each entry specifies `name`, `path`, `remote` (owner/repo), and `branch`.

See [`local-repo-sanitizer/SKILL.md`](local-repo-sanitizer/SKILL.md) for full documentation.

### refactor-complexity

Complexity-metric-driven iterative refactoring. Scans a codebase with [complexity-accounting](https://github.com/zhanglpg/code-complexity-measure), identifies high-impact refactoring opportunities, applies them, and verifies NCS (Net Complexity Score) improvement.

**Use when:** You want to systematically reduce code complexity while preserving behavior.

**Quick Start:**

```bash
# Install the complexity-accounting tool
pip install complexity-accounting

# For non-Python languages, install extras
pip install complexity-accounting[go]      # Go support
pip install complexity-accounting[ts]      # TypeScript support
pip install complexity-accounting[java]    # Java support
pip install complexity-accounting[js]      # JavaScript support
pip install complexity-accounting[rust]    # Rust support
pip install complexity-accounting[cpp]     # C++ support
```

**Usage:**

The skill is invoked by an AI agent with arguments:

```
[path] [--iterations N] [--threshold NCS]
```

- **path**: Directory or file to refactor (default: current directory)
- **--iterations N**: Maximum iterations to run (default: 3)
- **--threshold NCS**: Stop when NCS drops below this value (default: iterate until diminishing returns)

**Process:**

1. **Baseline Scan** — Run `complexity_accounting scan` to establish baseline NCS
2. **Identify Opportunities** — Prioritize by impact:
   - Mega-functions (CC > 40)
   - If-elif dispatch chains (CC > 15, 4+ branches)
   - Deep nesting (max_nesting >= 4)
   - Duplicated handler blocks
   - Dead/duplicate code
   - Coupling reduction (architectural)
3. **Refactor** — Apply 2-4 related refactorings per iteration
4. **Verify** — Run tests, re-scan, compare metrics
5. **Report** — Summarize changes, remaining hotspots, recommendations

**Example Session:**

```
Agent: Let me refactor this codebase to reduce complexity.

Step 0: Baseline Scan
$ python -m complexity_accounting scan ./src --top 30
NCS: 8.4 (Concerning)
Hotspots: 12 functions above threshold (CC > 10)

Step 1: Identified Opportunities
- Mega-function: process_request() (CC=52) — extract helpers
- Dispatch chain: handle_event() (CC=23, 8 branches) — use dict lookup
- Deep nesting: validate_input() (max_nesting=5) — early returns

Step 2: Refactored
- Extracted 5 helper functions from process_request()
- Replaced if-elif chain with ACTION_HANDLERS dict
- Flattened validate_input() with guard clauses

Step 3: Verified
$ python -m complexity_accounting scan ./src --top 30
NCS: 5.2 (Moderate) — 38% improvement
Hotspots: 5 functions above threshold

Recommendation: Continue for 1-2 more iterations focusing on coupling reduction.
```

See [`refactor-complexity/SKILL.md`](refactor-complexity/SKILL.md) for full documentation.

## Getting Started

1. Clone the repository
2. Copy relevant skills to your agent's skills directory:
   ```bash
   cp -r refactor-complexity ~/.openclaw/skills/
   ```
3. Install required dependencies (see skill documentation)
4. Configure according to your workflow needs

## Development

Skills follow the [OpenClaw AgentSkills specification](https://github.com/openclaw-dev/openclaw/blob/main/docs/skills.md). Each skill includes:

- `SKILL.md` — Skill definition, usage instructions, and examples
- `references/` — Supporting documentation (optional)
- `scripts/` — Executable scripts (optional)
- `templates/` — Output templates (optional)

## License

MIT
