# Agent Operational Checklist

> Operational checklists for AI agents working on the ai-lib ecosystem.
> Read [SOUL.md](SOUL.md) for persona; load rules from `rules/` before making changes.

---

## Before Making Code Changes

- [ ] **Load applicable rules**: Filter `rules/` by project and language (ARCH-*, RUST-*, PY-*, TS-*, GO-*)
- [ ] **Check MEMORY.md**: Read `ai-lib-plans/MEMORY.md` for durable decisions and conventions
- [ ] **Governance priority check**: Treat `ai-lib-constitution` + `ai-lib-plans` as highest-priority governance for ai-lib ecosystem work
- [ ] **Check reusable tools index**: Read `ai-lib-plans/tools/INDEX.md`; prefer existing tools before writing new scripts
- [ ] **Verify scope**: Confirm which projects are affected (ai-protocol, ai-lib-rust, ai-lib-python, ai-lib-ts, ai-lib-go, ai-protocol-mock, ai-lib-benchmark)
- [ ] **Confirm branch default naming**: Use `main` as canonical default branch (ARCH-004), never `master` as default
- [ ] **Confirm document privacy boundary**: Internal work docs (discussion/plan/report/summary) must not be pushed to public repos (DOC-002)
- [ ] **Canonical remote (GOV-001 v2)**: All public repos live on `ailib-official`. Push/PR to `ailib-official/<repo>` directly. `hiddenpath` public code repos are archived/read-only. Only `ai-lib-constitution` and `ai-lib-plans` remain on `hiddenpath` (private).

---

## After Making Code Changes

- [ ] **Run linters**: rustfmt/clippy (Rust), ruff/mypy (Python), eslint (TypeScript), golangci-lint (Go)
- [ ] **Run tests**: Unit tests + compliance tests where applicable
- [ ] **Reference rule IDs**: When reporting violations, use `[RULE-ID] Message` format
- [ ] **Update docs**: If API or behavior changed, update README and type hints. Follow DOC-001: English code docs + Chinese module header; internal docs in Chinese.
- [ ] **Tooling persistence**: Reusable scripts/programs must be stored in `ai-lib-plans/tools/` and indexed in `ai-lib-plans/tools/INDEX.md`

---

## Protocol / Manifest Changes

- [ ] **Validate against schema**: Use ai-protocol schemas (provider.json, capabilities.json, etc.)
- [ ] **Run compliance tests**: `ai-protocol/tests/compliance/`
- [ ] **Check all runtimes**: Ensure Rust, Python, TS, Go still pass with updated manifest

---

## Adding a New Provider

1. Create manifest: `ai-protocol/v1/providers/newprovider.yaml` or v2-alpha equivalent
2. Define: endpoint, auth, error_classification, streaming decoder, event_map
3. Run compliance tests — **no runtime code changes required** (ARCH-001)

---

## Release Checklist

- [ ] All compliance tests pass
- [ ] CHANGELOG updated
- [ ] Version bumped in pyproject.toml / Cargo.toml / package.json
- [ ] Release notes drafted
