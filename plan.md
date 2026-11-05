# StellarCore Wave Program Issue Master Plan

## 1. Executive Summary & Program Framework

### 1.1 Program Mission
StellarCore participates in the **Stellar Development Foundation (SDF) Drips Wave Program**. The Wave Program accelerates ecosystem resilience and open-source contributions across the Stellar network. Maintainers publish well-defined, tightly scoped issues that community contributors claim, execute, and verify during scheduled sprint cycles. Completed, reviewed, and merged contributions earn points that convert into USDC rewards.

### 1.2 Maintainer Principles & Scoping Philosophy
To ensure contributor success while maintaining system reliability, every issue posted under the Wave Program adheres to five core maintainer rules:

1. **Deterministic Acceptance Criteria**: Each issue specifies explicit input/output conditions, file boundaries, and pass/fail criteria. Ambiguity is resolved during grooming before the issue is opened for assignment.
2. **Offline-First Test Harness**: In accordance with the repository guidelines, unit and integration tests must never hit live anchor endpoints or external networks. Every issue requiring network behavior must supply or extend deterministic mock fixtures.
3. **Defense-in-Depth & Evidence Graph Integrity**: StellarCore relies on verifiable evidence. Work items must never fabricate data, bypass database invariants, or introduce unreviewed `Prisma.raw` queries.
4. **Reproducible Verification Steps**: Issues specify the exact commands needed to validate the change (`npm test`, `npx tsc --noEmit`, `npm run audit:integrity`, `npm run audit:compatibility`, etc.).
5. **Zero Secret Leakage & Safe Degradation**: All public API work must adhere to the bounded error envelope, zero stack-trace leakage, and validated caching semantics (`no-store` on error/sensitive boundaries).

---

## 2. Issue Taxonomy, Complexity Tiers, and Scoring

Each issue posted to the repository issue tracker is categorized by complexity, area, and sprint cycle. Points directly correspond to reward payout tiers established in `README.md`.

| Complexity Level | Label | Points (USDC) | Definition & Scope Boundary | Typical Turnaround |
| :--- | :--- | :--- | :--- | :--- |
| **Trivial** | `complexity: trivial` | **100** | Self-contained, single-file changes; static registry additions; documentation clarifications; UI copy/token alignment; isolated fixture additions. | 24 – 48 hours |
| **Medium** | `complexity: medium` | **150** | Multi-file features or fixes; new unit/integration test suites; component accessibility and responsive polish; rate limiter/middleware adjustments; anchor parsing schemas. | 3 – 5 days |
| **High** | `complexity: high` | **200** | Subsystem enhancements; multi-source aggregation algorithms; database migration and failover safety; cryptographic/HMAC verification; end-to-end Playwright resilience flows. | 5 – 7 days |

### Standard Issue Labels
- **Wave Identifiers**: `drips-wave`, `wave-sprint-<number>`, `good first issue`
- **Complexity**: `complexity: trivial`, `complexity: medium`, `complexity: high`
- **Work Domain**: `area:stellar`, `area:rates`, `area:reputation`, `area:api`, `area:dashboard`, `area:database`, `area:testing`, `area:docs`, `area:security`
- **Type**: `type: bug`, `type: feature`, `type: test`, `type: docs`, `type: chore`
- **Status**: `status: available`, `status: claimed`, `status: in-review`, `status: revisions-needed`

---

## 3. Work Categories Posted Across Sprint Cycles

Maintainers structure the backlog into five core domains to balance immediate stabilization with long-term platform capability.

```text
┌────────────────────────────────────────────────────────────────────────┐
│                   StellarCore Wave Issue Workstreams                   │
└────────────────────────────────────────────────────────────────────────┘
          │                   │                  │                 │
          ▼                   ▼                  ▼                 ▼
   ┌─────────────┐     ┌─────────────┐    ┌─────────────┐   ┌─────────────┐
   │  Bug Fixes  │     │New Features │    │   Testing   │   │Docs & DevEx │
   │  & Hardening│     │& Capabilities    │ & Invariants│   │& Governance │
   └─────────────┘     └─────────────┘    └─────────────┘   └─────────────┘
          │                   │                  │                 │
          └───────────────────┴────────┬─────────┴─────────────────┘
                                       │
                                       ▼
                       ┌───────────────────────────────┐
                       │    Security, Observability    │
                       │     & Database Resilience     │
                       └───────────────────────────────┘
```

### 3.1 Bug Fixes & Edge Case Hardening (`type: bug`)
Focuses on edge conditions in distributed network interactions, mathematical edge cases in aggregation, UI layout anomalies under extreme datasets, and API protocol adherence.
- **Rate Math & Temporal Bounds**: Handling fractional rounding, division by zero when corridors experience zero liquidity, and UTC leap second/millisecond truncation.
- **API Boundary Envelopes**: Ensuring consistent error schemas (`error.code`, `error.message`) on atypical database connection aborts or client disconnections (HTTP 499).
- **Dashboard & Component Resilience**: Preventing layout overflow when rates contain extreme mantissas (e.g., sub-satoshi prices or hyper-inflated fiat ratios).
- **Safe State Recovery**: Retaining cached UI state when background refresh requests fail, preventing empty-screen flicker.

### 3.2 New Features & Capabilities (`type: feature`)
Drives expansion of reviewed anchors, corridor discovery, rate aggregation models, and public evidence presentation.
- **Reviewed SEP-38 Rate Sources**: Adding vetted rate sources for active corridors (e.g., secondary providers for USDC/BRL, EURC/USD) to fulfill `MIN_FRESH_SOURCES=2` requirements.
- **Transfer Outcome Ingestion**: Implementing cryptographic HMAC webhook verification and replay defense for off-chain settlement evidence per `docs/rfc-outcome-webhook-schema.md`.
- **Corridor Anomaly Detection & Alerts**: Pure-function heuristics detecting abrupt spread expansion or anchor silence over configurable sliding windows.
- **Dashboard Discovery Filters**: Accessible UI controls for filtering corridors by asset issuer, minimum fresh sources, and reputation band.

### 3.3 Testing, Invariants & Verification (`type: test`)
Reinforces the evidence graph, ensures regression-free refactoring, and hardens untrusted parser boundaries.
- **Property-Based & Fuzz Testing**: Fast-check / property suites for untrusted `stellar.toml` (SEP-1) and quote response (SEP-38) payloads under `tests/property/`.
- **Reputation Basis-Point Invariants**: Exhaustive boundary tests verifying that reputation scoring remains deterministic, integer-bound (0–10,000 bps), and tie-breaker stable.
- **Playwright E2E User Journeys**: Automated browser testing for corridor selection, responsive breakpoints, reduced-motion compliance, and keyboard navigation.
- **Differential Database Testing**: Validating query execution plans and index utilization under synthetic high-cardinality snapshots.

### 3.4 Documentation, Developer Experience & Runbooks (`type: docs`)
Empowers new contributors, maintains operational readiness, and guarantees audit trail reproducibility.
- **Interactive OpenAPI / Contract Fixtures**: Updating and documenting API response fixtures under `tests/fixtures/api/`.
- **Operator Runbooks**: Step-by-step diagnostic workflows for edge conditions (e.g., diagnosing missing rate medians, recovering from primary DB failover).
- **Contributor Scaffolding Guides**: Step-by-step guides for running isolated local PostgreSQL databases, seeding synthetic test fixtures, and running Vitest suites offline.

### 3.5 Security, Observability & Database Resilience (`type: security`, `type: chore`)
Protects against data poisoning, connection exhaustion, secret leakage, and migration failures.
- **Strict TLS & Connection Pool Eviction**: Verifying socket lifecycle recycling and connection pool timeouts during primary failovers.
- **Secret Redaction Auditing**: Automated AST checks ensuring headers, tokens, and database credentials are excluded from serialized logs and public API payloads.
- **Prisma Boundary Auditing**: Maintaining `npm run audit:sql` gates to prevent raw SQL vulnerabilities and untyped query regressions.

---

## 4. Planned Sprint Waves & Scoped Issue Backlog

Below is the concrete backlog of planned issues ready for posting across upcoming Wave Program sprint cycles.

### Wave Sprint 1: Registry Expansion & Evidence Hardening

#### Issue #W1-01 (Trivial · 100 Pts)
- **Title**: `chore(registry): onboard reviewed anchor definition for Anclap (ARS corridor)`
- **Labels**: `drips-wave`, `wave-sprint-1`, `complexity: trivial`, `area:stellar`, `type: feature`
- **Scope**:
  1. Add Anclap anchor configuration to `constants/anchors.ts` with validated `homeDomain`, `signingKey`, and SEP endpoints.
  2. Associate with the `ARS:USDC` corridor in `constants/corridors.ts`.
  3. Include verified historical `stellar.toml` snapshot fixture in `tests/fixtures/stellar/`.
- **Acceptance Criteria**:
  - `npm run audit:config` passes with zero warnings.
  - New anchor passes `lib/stellar/anchorSync.ts` schema validation offline.
  - No network calls introduced in test suites.
- **Verification**: `npm run audit:config && npm test tests/unit/stellar/`

#### Issue #W1-02 (Medium · 150 Pts)
- **Title**: `test(rates): add fuzz property tests for malformed SEP-38 quote responses`
- **Labels**: `drips-wave`, `wave-sprint-1`, `complexity: medium`, `area:rates`, `area:testing`, `type: test`
- **Scope**:
  1. Extend `tests/property/sep38Property.test.ts` using randomized string generators.
  2. Inject extreme inputs: negative prices, NaN strings, exponential notation, missing timestamp fields, future timestamps (> +5 minutes), and circular asset pairs.
  3. Assert `lib/rates/snapshot.ts` safely discards invalid inputs without throwing unhandled exceptions.
- **Acceptance Criteria**:
  - 10,000 random iterations run deterministically with fixed seed reporting.
  - Any failing seed is logged in canonical reproduction format.
- **Verification**: `npx tsx --test tests/property/sep38Property.test.ts`

#### Issue #W1-03 (High · 200 Pts)
- **Title**: `feat(reputation): implement deterministic tie-breaker for identical corridor scores`
- **Labels**: `drips-wave`, `wave-sprint-1`, `complexity: high`, `area:reputation`, `type: feature`
- **Scope**:
  1. Update `lib/reputation/engine.ts` ranking logic when two anchors share identical composite basis points.
  2. Order deterministically by: (a) fresh evidence volume count descending, (b) earliest verified onboarding timestamp ascending, (c) lexicographical anchor domain.
  3. Add exhaustive unit tests in `tests/unit/reputation/rankingTieBreaker.test.ts`.
- **Acceptance Criteria**:
  - Ranking order is 100% stable regardless of database row retrieval order.
  - Integer arithmetic only; zero floating-point math introduced.
  - `npm run audit:integrity` and `npm test` pass.
- **Verification**: `npm test tests/unit/reputation/ && npm run audit:compatibility`

---

### Wave Sprint 2: Rate Engine & Resilient API Boundaries

#### Issue #W2-01 (Trivial · 100 Pts)
- **Title**: `fix(api): return explicit Retry-After headers on 429 rate limit responses`
- **Labels**: `drips-wave`, `wave-sprint-2`, `complexity: trivial`, `area:api`, `type: bug`
- **Scope**:
  1. Inspect `lib/api/rateLimiter.ts` when request rate exceeds threshold.
  2. Ensure standard HTTP header `Retry-After: <seconds>` is computed from remaining bucket window.
  3. Verify unit test coverage in `tests/unit/api/rateLimiter.test.ts`.
- **Acceptance Criteria**:
  - Header is integer value representing seconds until window reset.
  - Matches shared bounded JSON envelope: `{ error: { code: "RATE_LIMITED", message: "..." } }`.
- **Verification**: `npm test tests/unit/api/rateLimiter.test.ts`

#### Issue #W2-02 (Medium · 150 Pts)
- **Title**: `feat(dashboard): add keyboard shortcut navigation and ARIA live regions for rate updates`
- **Labels**: `drips-wave`, `wave-sprint-2`, `complexity: medium`, `area:dashboard`, `type: feature`
- **Scope**:
  1. Enhance `components/CorridorPanel.tsx` and `components/RateHistoryChart.tsx` with accessible ARIA live regions (`aria-live="polite"`).
  2. Implement keyboard shortcut navigation (`/` to focus corridor selector, `r` to trigger manual retry refresh).
  3. Add accessibility unit tests using `@testing-library/react`.
- **Acceptance Criteria**:
  - Screen readers announce rate refresh and staleness warning transitions without focus shifts.
  - Zero violation of WCAG 2.1 Level AA contrast and accessibility standards.
- **Verification**: `npm run lint && npm test tests/unit/dashboard/`

#### Issue #W2-03 (High · 200 Pts)
- **Title**: `feat(rates): implement multi-source median calculation with dynamic source-weighting fallback`
- **Labels**: `drips-wave`, `wave-sprint-2`, `complexity: high`, `area:rates`, `type: feature`
- **Scope**:
  1. Enhance `lib/rates/median.ts` to support multi-source aggregation across >= 3 independent sources.
  2. Implement outlier elimination (modified Thompson Tau or interquartile range filtering).
  3. Preserve strict `MIN_FRESH_SOURCES` threshold check; gracefully return `INSUFFICIENT_SOURCES` if fewer than 2 valid observations remain.
  4. Write comprehensive unit tests in `tests/unit/rates/medianOutlier.test.ts`.
- **Acceptance Criteria**:
  - Deterministic calculations verified against high-precision fixtures.
  - Public rates API contract remains backwards-compatible.
- **Verification**: `npm run audit:compatibility && npm test tests/unit/rates/`

---

### Wave Sprint 3: Database Integrity & Transfer Outcome Architecture

#### Issue #W3-01 (Trivial · 100 Pts)
- **Title**: `docs(runbook): add step-by-step diagnostic runbook for corridor staleness alerts`
- **Labels**: `drips-wave`, `wave-sprint-3`, `complexity: trivial`, `area:docs`, `type: docs`
- **Scope**:
  1. Author `docs/runbook-corridor-staleness.md`.
  2. Detail operator steps when `STALE_EVIDENCE` is flagged: verifying anchor SEP-38 endpoints, checking cron run logs, validating database connection pools.
  3. Add cross-references to `README.md` and `docs/troubleshooting.md`.
- **Acceptance Criteria**:
  - Markdown adheres to repository link and alert formatting conventions.
  - Read-only diagnostics commands clearly documented.
- **Verification**: `npx markdownlint docs/runbook-corridor-staleness.md` (or repo doc checks)

#### Issue #W3-02 (Medium · 150 Pts)
- **Title**: `test(database): add migration preflight verification script and rollback dry-run test`
- **Labels**: `drips-wave`, `wave-sprint-3`, `complexity: medium`, `area:database`, `area:testing`, `type: test`
- **Scope**:
  1. Implement automated test in `tests/integration/database/migrationSafety.test.ts`.
  2. Verify that `prisma/migrations` contains clean, ordered migration paths without checksum drift.
  3. Validate that preflight assertions catch unapplied or conflicting schema states.
- **Acceptance Criteria**:
  - Verifies database migrations without mutating the active database environment.
  - Complies with `RUN_INTEGRITY_DATABASE_INTEGRATION` safety flags.
- **Verification**: `npm run audit:integrity && npm test tests/integration/database/`

#### Issue #W3-03 (High · 200 Pts)
- **Title**: `feat(outcomes): implement RFC outcome webhook parser with HMAC-SHA256 signature verification`
- **Labels**: `drips-wave`, `wave-sprint-3`, `complexity: high`, `area:security`, `area:stellar`, `type: feature`
- **Scope**:
  1. Implement webhook validation module in `lib/stellar/outcomeWebhook.ts` following `docs/rfc-outcome-webhook-schema.md`.
  2. Verify payload signatures using constant-time cryptographic equality (`crypto.timingSafeEqual`).
  3. Enforce replay defense via timestamp tolerances (+/- 300 seconds) and nonce persistence check.
  4. Unit test with valid, expired, modified, and malformed HMAC signatures in `tests/unit/stellar/outcomeWebhook.test.ts`.
- **Acceptance Criteria**:
  - Invalid signatures reject with HTTP 401 without processing body payload.
  - Timing attack resilience guaranteed by constant-time verification.
  - No secret exposure in server-side logs.
- **Verification**: `npm test tests/unit/stellar/outcomeWebhook.test.ts`

---

## 5. Issue Specification & Contributor Template

To maintain uniform quality, all issues posted by maintainers must follow this standard markdown template:

```markdown
### Summary
<!-- 2-3 sentences explaining the problem or goal -->

### Background & Context
<!-- Link relevant architecture documents, RFCs, or code files (e.g. docs/architecture.md, lib/rates/...) -->

### Scope of Work
- [ ] Task 1: Specific module or file to modify
- [ ] Task 2: Specific logic or boundary to implement
- [ ] Task 3: Unit / integration test coverage to add

### Non-Goals & Boundaries
<!-- Explicit statements of what NOT to do, e.g. "Do not modify the database schema", "Do not make live HTTP calls" -->

### Acceptance Criteria
1. Strict TypeScript passes: `npx tsc --noEmit`
2. Test suites pass: `npm test <path-to-test>`
3. Registry & compatibility checks pass: `npm run audit:config && npm run audit:compatibility`
4. No sensitive information or secrets exposed in payloads or logs.

### Complexity & Bounty
- **Complexity**: trivial (100 pts) | medium (150 pts) | high (200 pts)
- **Reward**: Equivalent USDC payout via Stellar Drips Wave
```

---

## 6. Sprint Lifecycle & Contributor Onboarding Protocol

```text
┌────────────────────────────────────────────────────────────────────────┐
│                        Sprint Execution Workflow                       │
└────────────────────────────────────────────────────────────────────────┘
     │
     ▼
1. Maintainer Backlog Grooming
   └─ Scopes issues, tags labels, sets complexity points, checks test fixtures.
     │
     ▼
2. Issue Claiming & Assignment
   └─ Contributor comments with plan; maintainer assigns issue (1 active per contributor).
   └─ 48h check-in policy; unassigned if inactive after 72h.
     │
     ▼
3. Contributor Implementation & Verification
   └─ Fork repo -> branch off main (`feat/...`, `fix/...`, `docs/...`).
   └─ Implement logic -> write offline tests -> run lint and type checks.
     │
     ▼
4. Pull Request Review & CI Gate
   └─ PR opened against `main`; describes change with verification evidence.
   └─ CI runs: typecheck, lint, test suite, config audit, compatibility audit.
   └─ Maintainer code review: architecture compliance, invariant safety.
     │
     ▼
5. Merge & Wave Bounty Award
   └─ Maintainer merges PR to `main` via squash/rebase.
   └─ Contributor address recorded; wave points credited for USDC distribution.
```

### 6.1 Contributor Code Review Checklist
Before any PR is approved and merged, maintainers verify:
- [ ] **Type Safety**: No `any` escapes or bypassed strict compiler flags.
- [ ] **Network Isolation**: All test suites utilize mock responses or fixtures; zero live external network calls.
- [ ] **Database Safeguards**: No new raw SQL queries without explicit maintainer security review (`npm run audit:sql`).
- [ ] **Error Envelopes**: Public API error responses preserve the shared envelope and do not leak stack traces or internal connection details.
- [ ] **Documentation & Changelog**: Meaningful user- or developer-facing changes have an entry added to `CHANGELOG.md` under `[Unreleased]`.

---

## 7. Program Governance & Success Metrics

To measure the effectiveness of the Wave Program sprints, maintainers track:
- **Issue Turnaround Rate**: Target median turnaround of < 48 hours for trivial, < 5 days for medium, and < 7 days for high complexity.
- **First-Time Contributor Conversion**: Percentage of contributors completing their initial issue who proceed to claim medium/high issues.
- **CI First-Pass Rate**: Percentage of contributor PRs that pass automated CI checks on initial opening (target > 80%).
- **Ecosystem Breadth**: Cumulative growth of verified anchor registries, corridor definitions, and robust rate source observations.
