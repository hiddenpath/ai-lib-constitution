# Business Boundary Rules — ai-lib Ecosystem

> **Source**: Extracted from PRODUCT_PLAN_v2 (synthesis of reports 1-6)
> **Status**: Draft — pending owner confirmation
> **Date**: 2026-04-25

---

## BIZ-001: Product Matrix Architecture

**Rule**: The ai-lib product line is organized as a 2×2 matrix (Architecture Layer × Market Layer):

| | To B | To C |
|---|---|---|
| Prism (P-layer) | Prism Enterprise | Prism |
| Vela (A-layer) | Vela Pro | Vela |

No product name may be introduced outside this matrix without:
1. Explicit `PRODUCT_PLAN_v2.md` update
2. Owner confirmation
3. `ai-lib-plans/MEMORY.md` sync

**Rationale**: Reports 1-4 identified 7 scattered product concepts; the matrix converges them into 2 product lines with clear market positioning.

---

## BIZ-002: Three-Zone Open/Close Boundary (A/B/C Bands)

| Zone | Content | License Default |
|------|---------|----------------|
| **A-band** (Permanently Open) | ai-protocol schemas, E-layer (core/SDK/WASM basic), Vela client core, benchmark methods/baselines | Apache-2.0 |
| **B-band** (Open, No SLA) | Contact (P) reference implementations, E2E sync client, example routing strategies | Apache-2.0 + explicit "NOT PRODUCTION SLA" disclaimer |
| **C-band** (May be closed) | Prism routing engine, Key pool, Pricer, Admin API, audit reports, SSO, compliance templates, signed Pack distribution | Proprietary / dual-license TBD |

**Rule**: No A-band content may be moved to C-band without owner approval. No C-band content may be silently mixed into A-band repositories.

**Rationale**: Schema is already public in ai-protocol; commercial value must live in implementation + operations, not in hiding specs (Report 3).

---

## BIZ-003: Prism Enterprise Code Architecture

**Rule**: Prism Enterprise and Prism share the same codebase. Enterprise-exclusive features are gated behind Rust feature flags (`--features enterprise`), not separate repositories.

**Rationale**: Same codebase prevents "free version bug fixed, enterprise version forgotten" drift. Rust feature flags are the natural mechanism (D2 confirmed).

---

## BIZ-004: Privacy-First Architecture

**Rule**: Prism stores ONLY ExecutionMetadata (provider_id, model_id, latencies, usage, cost, error_code). Conversation content MUST NOT touch P-layer persistent storage. This is an architectural constraint, not a policy choice.

**Exceptions**: Phase 2+ E2E encrypted sync stores only ciphertext blobs (zero-knowledge). Decryption keys never leave Vela (A-layer).

**Rationale**: "Architecture-level privacy" is a core differentiator vs OpenRouter (configurable) and Poe (policy-based). See prism_vs_poe_vs_openrouter.md §2.5.

---

## BIZ-005: Phase Gate Discipline

**Rule**: New product features are gated by Phase milestones:

| Feature | Phase Required | Gate |
|---------|---------------|------|
| Prism basic API + Key pool | 1 | M1 (proxy works) |
| Billing / margin | 2 | First paying user |
| Enterprise (SSO, audit, private deploy) | 2 | Enterprise pilot |
| WASM client-side routing | 2 | ailib-wasm-test v0.2 |
| Compliance templates | 2+ | Industry partner |
| Multi-modal | 3 | Stable revenue |
| P2P / Mesh | Never promised in 12-month roadmap | Research only |

No "Phase 3" feature may enter Phase 1 commit scope without owner override.

**Rationale**: Report 2 identified over-promising risk; Report 4 requires explicit PT-073 dependency tagging.

---

## BIZ-006: Hot Product Prioritization

**Rule**: Each Phase MUST identify 1-3 "hot products" — standalone tools or demos with viral potential. Hot products receive dedicated marketing effort (HN post, demo video, blog post) at Phase launch, independent of main product marketing.

**Rationale**: Technical infrastructure products struggle with awareness. Hot products serve as entry points that pull users into the ecosystem.

---

## Amendment Log

| Date | Amendment | Author |
|------|-----------|--------|
| 2026-04-25 | Initial draft from reports 1-6 synthesis | Spider |
