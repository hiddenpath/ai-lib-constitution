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
- [ ] **Canonical remote (GOV-001 v2)**: All public repos live on `ailib-official`. Push/PR to `ailib-official/<repo>` directly. `hiddenpath` public code repos are archived/read-only. `ai-lib-constitution`, `ai-lib-plans`, and **`papers`** (private) remain on `hiddenpath`; do **not** repoint `papers` to `ailib-official`.

---

## Merge / Rebase Conflicts (GOV-002)

When resolving **merge, rebase, or cherry-pick** conflicts in any public runtime or protocol repo:

- [ ] **Load GOV-002** (`rules/governance/GOV-002-merge-conflict-resolution-discipline.yaml`): no blind whole-file ours/theirs; align intent with spec/tasks; PR notes list paths and rationale; run narrow tests for touched execution paths; preserve safety boundaries (proxy, trust_env, compliance CI).

---

## After Making Code Changes

- [ ] **Run linters**: rustfmt/clippy (Rust), ruff/mypy (Python), eslint (TypeScript), golangci-lint (Go)
- [ ] **Run tests**: Unit tests + compliance tests where applicable
- [ ] **Reference rule IDs**: When reporting violations, use `[RULE-ID] Message` format
- [ ] **Update docs**: If API or behavior changed, update README and type hints. Follow DOC-001: English code docs + Chinese module header; internal docs in Chinese.
- [ ] **Tooling persistence**: Reusable scripts/programs must be stored in `ai-lib-plans/tools/` and indexed in `ai-lib-plans/tools/INDEX.md`

---

## PR Review Checklist (GOV-003)

When reviewing an open PR for merge:

### Phase 1 — Automated checks
- [ ] **CI passed** (GitHub Actions / equivalent). Wait for pending if needed.
- [ ] **Local linter**: `cargo fmt --check && cargo clippy -D warnings` (Rust) / equivalent
- [ ] **Local tests**: `cargo test --features <affected_features>` or equivalent
- [ ] **Repo check**: No `hiddenpath` URLs in workflows, README, or Cargo.toml (GOV-001)

### Phase 2 — Code review
- [ ] **Architecture**: Does diff follow ARCH-* rules (protocol-driven, pipeline, cross-runtime)?
- [ ] **Safety**: No unsafe/unwrap/panic/hardcoded secrets. Auth/rate-limit/proxy boundaries preserved.
- [ ] **ai-lib migration sensitive**: If touching transport/connection/Provider trait, review against ZS-ML plan.
- [ ] **Breaking changes**: Documented in CHANGELOG + PR description?
- [ ] **Test adequacy**: New tests cover happy + error paths?
- [ ] **Conflict resolution**: If merge conflict existed, verify no blind whole-file ours/theirs (GOV-002)

### Phase 3 — Decision
- [ ] All checks pass → **approve + merge** (`gh pr merge --squash` or `git merge --no-ff` + push via SSH)
- [ ] Any check fails → **document blocking issue**, close/reject or mark blocked

### Phase 4 — Plans backfill
- [ ] Update task YAML: `status: completed`, `pr.merge_commit`, `completion_notes`
- [ ] Update TASKS_INDEX.md
- [ ] Commit + push to `hiddenpath/ai-lib-plans`

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
