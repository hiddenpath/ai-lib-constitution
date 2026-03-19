---
# This repository contains structured rules for AI programming agents
# working on the ai-lib ecosystem projects.
#
# DESIGN PRINCIPLES FOR AI AGENT READABLE RULES:
# 1. All rules are in YAML format for machine readability
# 2. Each rule has clear applicability scope and priority
# 3. Rules include code examples (correct and incorrect)
# 4. Rules specify validation methods (linter, test, manual review)
# 5. Rules are referenced by unique IDs for cross-referencing
#
# STRUCTURE:
#
# rules/
#   architecture/    - Design principles and patterns
#   coding/          - Language-specific coding standards
#   testing/         - Testing requirements and patterns
#   documentation/   - Documentation standards
#   release/         - Release process requirements
#   security/        - Security guidelines
#
# schemas/           - JSON Schema definitions for validation
# templates/         - Code and document templates
# examples/          - Good and bad examples
# validatable/       - Automated validation scripts
# checklists/        - Executable checklists
# metadata/          - Rule metadata and indexes
#
# USAGE BY AI AGENTS:
#
# 1. Load all rule files from rules/
# 2. Filter by applicable project/language
# 3. Check priority and severity
# 4. Apply rules to code changes
# 5. Validate using specified tools
# 6. Report violations with rule ID reference
#
# EXAMPLE RULE LOADING:
#
# ```python
# import yaml
# from pathlib import Path
#
# def load_rules(category, language=None):
#     rules = []
#     for file in Path(f"rules/{category}").glob("*.yaml"):
#         rule = yaml.safe_load(file.read_text())
#         if language is None or language in rule.get("languages", []):
#             rules.append(rule)
#     return sorted(rules, key=lambda r: r.get("severity", "medium"))
# ```
#
# RULE REFERENCE FORMAT:
#
# When reporting violations, use format: `[RULE-ID] Message`
# Example: `[RUST-001] Use Result<T, E> instead of unwrap()`
#
---

## Workspace Requirement

**All ai-lib ecosystem work must have ai-lib-constitution and ai-lib-plans in the workspace.**  
Each project includes `.cursor/rules/ai-lib-constraint.mdc` (alwaysApply) to enforce this.  
See [docs/WORKSPACE_SETUP.md](docs/WORKSPACE_SETUP.md) for setup and verification steps.

### Agent Identity & Checklists
- **[SOUL.md](SOUL.md)** — Agent persona and principles
- **[AGENTS.md](AGENTS.md)** — Operational checklists (before/after changes, release)

### Architecture Rules
- Everything is an operator (算子), everything is protocol (协议)
- Zero hardcoded provider logic
- Protocol-driven configuration

### Coding Standards by Language
- **Rust**: See `rules/coding/rust/`
- **Python**: See `rules/coding/python/`
- **TypeScript**: See `rules/coding/typescript/`
- **Go**: See `rules/coding/go/`

### Validation Standards
- All runtimes must pass compliance tests for AI-Protocol
- Linting: rustfmt/clippy (Rust), ruff/mypy (Python), eslint (TypeScript), golangci-lint (Go)
- Runtime tests: `cargo test`, `pytest`, `npm test`, `go test ./...`
- Test coverage: Minimum 80% for public APIs

### Documentation Standards
- README must include: Vision, Quick Start, Features, API, Architecture
- All public APIs must have type information and examples
- **Code docs**: English by default; add one Chinese summary line at module/file header
- **Scope**: Applies to all publicly published code (ai-lib-rust, ai-lib-python, ai-lib-ts, ai-protocol-mock, ai-protocol)
- **Internal docs**: Plans, reports, explanations for maintainer — use Chinese by default

## Current Ecosystem Projects

| Project | Purpose | Language |
|--------|---------|----------|
| ai-protocol | Provider-agnostic specification | YAML/JSON |
| ai-lib-rust | Rust runtime implementation | Rust |
| ai-lib-python | Python runtime implementation | Python |
| ai-lib-ts | TypeScript runtime implementation | TypeScript/JavaScript |
| ai-lib-go | Go runtime implementation | Go |
| ai-lib-benchmark | Cross-runtime benchmark toolkit | Multi-language |
| ai-protocol-mock | Mock server for testing | Python |

## Rules Index

### Architecture Rules
| Rule ID | Topic | Priority |
|---------|-------|----------|
| ARCH-001 | Protocol-driven design | critical |
| ARCH-002 | Operator-based architecture | critical |
| ARCH-003 | Multi-language consistency | high |

### Rust Coding Rules
| Rule ID | Topic | Priority |
|---------|-------|----------|
| RUST-001 | Error handling with Result | critical |
| RUST-002 | Async functions | critical |
| RUST-003 | Naming conventions | medium |

### Python Coding Rules
| Rule ID | Topic | Priority |
|---------|-------|----------|
| PY-001 | Type hints required | high |
| PY-002 | Async I/O patterns | critical |
| PY-003 | Error handling | high |

### TypeScript Coding Rules
| Rule ID | Topic | Priority |
|---------|-------|----------|
| TS-001 | Strict mode required | critical |
| TS-002 | Type safety | critical |
| TS-003 | Error handling | high |

### Go Rules
| Rule ID | Topic | Priority |
|---------|-------|----------|
| GO-001 | Coding standards | high |
| GO-002 | Error handling and protocol code mapping | critical |
| GO-003 | Concurrency and context safety | high |
| GO-004 | Testing and compliance parity | high |
| GO-005 | Manifest/schema compatibility | critical |
| GO-006 | Transport and streaming contract | high |
| GO-007 | Release and module governance | high |

### Documentation Rules
| Rule ID | Topic | Priority |
|---------|-------|----------|
| DOC-001 | Code docs: English + Chinese module header | high |

### Benchmark Rules
| Rule ID | Topic | Priority |
|---------|-------|----------|
| BENCH-001 | Benchmark reproducibility and baseline governance | high |

---

**Version**: 1.0.0
**Last Updated**: 2026-03-15
**Maintainer**: ai-lib team
**Purpose**: AI agent executable rules for ai-lib ecosystem development
