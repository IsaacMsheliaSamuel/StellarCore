# StellarCore Wave Program Master Engineering Plan
## Comprehensive Sprint Backlog, Issue Taxonomy, and Contributor Operations

---

## 1. Executive Summary & Wave Program Charter

### 1.1 Ecosystem Mission & Context
StellarCore is an open-source rate discovery, anchor synchronization, and reputation evaluation engine for the Stellar cross-border payment ecosystem. Backed by Next.js 15 App Router, PostgreSQL via Prisma, and strict TypeScript, StellarCore tracks real-time indicative exchange rates (SEP-38), discovers anchor service capabilities (SEP-1), and computes deterministic reputation scores for payment corridors worldwide.

The project participates in the **Stellar Development Foundation (SDF) Drips Wave Program**. The Wave Program operates on bi-weekly sprint cadences where maintainers publish tightly scoped, bite-sized, and test-backed issues. Open-source developers from across the global Stellar developer community claim these issues, implement solutions within strict isolation boundaries, pass offline test gates, and earn verified points that disburse as **USDC** rewards.

### 1.2 The Maintainer Manifesto: Core Engineering Tenets
Every issue created by StellarCore maintainers must enforce the project's foundational engineering tenets:

1. **Zero External Network Dependencies in CI/Tests**:
   - Unit, integration, and compatibility tests must run 100% offline.
   - External anchor servers, Horizon endpoints, or remote TOML files must be replaced by deterministic mock fixtures located under `tests/fixtures/`.
   - Network timeouts or anchor flakiness must never cause CI pipeline failure.

2. **Deterministic Integer Basis-Point Arithmetic**:
   - In financial rate normalization and anchor reputation scoring, floating-point rounding errors can cause non-deterministic state drift.
   - Reputation scoring, fill rates, and fee assessments must use integer basis points (0–10,000 bps) and BigInt timestamps.

3. **Strict Evidence Provenance & Immutable Graph Invariants**:
   - Every rate observation and transfer outcome recorded in PostgreSQL must maintain an unbroken provenance lineage (`configurationFingerprint`, `capturedAt`, `anchorId`, `corridorId`).
   - The platform never fabricates fallback rates or infers off-chain settlement without verified cryptographic or reviewed evidence.

4. **Bounded Public API Envelopes & Zero Information Leakage**:
   - Public JSON endpoints under `app/api/` must strictly conform to canonical response fixtures.
   - Failures must emit standardized error envelopes (`error.code`, `error.message`). Under no circumstance may database exceptions, connection pool diagnostics, raw SQL traces, or internal IP addresses leak to client responses.

5. **Defense-in-Depth Database Integrity**:
   - All database interactions use `@prisma/adapter-pg` through verified-TLS connection pools.
   - Unreviewed raw SQL or `Prisma.raw` queries are strictly forbidden and guarded by `npm run audit:sql`.

---

## 2. Issue Complexity Tiers, Point Allocations & Label Taxonomy

To maintain clarity and fairness, all issues posted on GitHub are categorized using explicit complexity tiers, workflow statuses, and functional domain labels.

### 2.1 Complexity Tiers & Bounty Scale

```text
┌─────────────────────────────────────────────────────────────────────────────┐
│                    Wave Program Complexity & Bounty Tiers                   │
├────────────────────┬──────────────┬──────────────────┬──────────────────────┤
│ Tier               │ Points (USDC)│ Scope Bounds     │ Review SLA           │
├────────────────────┼──────────────┼──────────────────┼──────────────────────┤
│ complexity: trivial│ 100 USDC     │ 1–2 files changed│ 24–48 hours          │
│ complexity: medium │ 150 USDC     │ 3–6 files changed│ 48–72 hours          │
│ complexity: high   │ 200 USDC     │ Subsystem wide   │ 3–5 days             │
└────────────────────┴──────────────┴──────────────────┴──────────────────────┘
```

#### Tier 1: Trivial (`complexity: trivial` · 100 Points)
- **Scope**: Isolated, single-module modifications with minimal downstream ripple effects.
- **Examples**:
  - Adding reviewed anchor/corridor constants (`constants/anchors.ts`, `constants/corridors.ts`).
  - Correcting UI typo or adjusting design-token spacing in static landing views.
  - Adding individual mock fixtures for known SEP-1 or SEP-38 responses.
  - Fixing edge-case sanitization in string formatting utilities.
- **Estimated Completion Time**: 2–6 hours.

#### Tier 2: Medium (`complexity: medium` · 150 Points)
- **Scope**: Multi-file features or bug fixes requiring localized algorithmic logic, schema parser adjustments, or new unit test suites.
- **Examples**:
  - Implementing an edge-case rate normalization heuristic in `lib/rates/normalize.ts`.
  - Adding ARIA live-announcements and keyboard navigation to dashboard panels.
  - Writing property-based fuzz tests in `tests/property/` with fast-check.
  - Adding defensive HTTP 429 `Retry-After` calculation with controlled clock unit tests.
- **Estimated Completion Time**: 1–3 days.

#### Tier 3: High (`complexity: high` · 200 Points)
- **Scope**: Deep subsystem capabilities, cryptographic verification, cross-table database invariants, complex rate aggregation, or full-stack browser E2E flows.
- **Examples**:
  - Multi-source outlier-resistant median pricing across heterogeneous anchor feeds.
  - HMAC-SHA256 signature verification and replay prevention for outcome webhooks.
  - Database primary failover socket eviction and retry loop hardening.
  - Playwright end-to-end regression test suites covering degraded corridor states.
- **Estimated Completion Time**: 4–7 days.

### 2.2 Comprehensive Label Taxonomy

| Label Namespace | Label | Description |
| :--- | :--- | :--- |
| **Program** | `drips-wave` | Core marker identifying an SDF Wave bounty issue. |
| | `wave-sprint-1` | Scheduled for Wave Sprint Cycle 1. |
| | `wave-sprint-2` | Scheduled for Wave Sprint Cycle 2. |
| | `wave-sprint-3` | Scheduled for Wave Sprint Cycle 3. |
| | `wave-sprint-4` | Scheduled for Wave Sprint Cycle 4. |
| | `good first issue` | Friendly for newcomers to the StellarCore architecture. |
| **Type** | `type: bug` | Defect, unexpected edge-case behavior, or invariant regression. |
| | `type: feature` | New functional capability, endpoint, or calculation algorithm. |
| | `type: test` | Test suite addition, property fuzzing, or E2E automation. |
| | `type: docs` | Technical runbook, architecture RFC, or onboarding document. |
| | `type: security` | Hardening, secret redaction, audit compliance, or TLS integrity. |
| | `type: chore` | Refactoring, tooling upgrades, or CI workflow enhancement. |
| **Domain** | `area:stellar` | SEP-1 discovery, SEP-10 auth, SEP-38 quote protocol. |
| | `area:rates` | Snapshot collection, median pricing, freshness evaluation. |
| | `area:reputation` | Composite scoring, score bands, basis-point math. |
| | `area:api` | Next.js route handlers under `app/api/`, headers, pagination. |
| | `area:dashboard` | Next.js server/client components, GSAP, accessibility. |
| | `area:database` | Prisma models, migrations, TLS pool manager, failover. |
| | `area:integrity` | Read-only graph audit, invariant checks, remediation scripts. |
| **Status** | `status: available` | Open for claiming by any community contributor. |
| | `status: claimed` | Assigned to a contributor; actively under development. |
| | `status: in-review` | Pull request opened and undergoing maintainer review. |
| | `status: revisions-needed`| Changes requested following maintainer review. |

---

## 3. Work Categories Posted Across Sprint Cycles

Maintainers structure the backlog into five balanced work categories to ensure system resilience, developer adoption, and feature velocity.

```text
                               ┌────────────────────────────────┐
                               │  StellarCore Work Categories   │
                               └───────────────┬────────────────┘
                                               │
        ┌──────────────────┬───────────────────┼───────────────────┬──────────────────┐
        │                  │                   │                   │                  │
        ▼                  ▼                   ▼                   ▼                  ▼
┌──────────────┐   ┌──────────────┐    ┌──────────────┐    ┌──────────────┐   ┌──────────────┐
│  Category A  │   │  Category B  │    │  Category C  │    │  Category D  │   │  Category E  │
│  Bug Fixes   │   │ New Features │    │  Testing &   │    │Documentation │   │  Security &  │
│ & Hardening  │   │& Capabilities│    │  Invariants  │    │  & Runbooks  │   │ Architecture │
└──────────────┘   └──────────────┘    └──────────────┘    └──────────────┘   └──────────────┘
```

---

### Category A: Bug Fixes & Edge-Case Hardening (`type: bug`)
Bug fixes in StellarCore address subtle runtime boundary failures, concurrency races, financial calculation precision limits, and error boundary leaks.

#### 1. Rate Normalization & Arithmetic Boundaries
- **Fractional Inversions**: SEP-38 quotes provide both buy and sell quotes (`source_amount` vs `destination_amount`). Precision loss can occur when converting tiny fractions (e.g., 0.000000000000000001 XLM/USDC). Issues fix decimal precision scaling using `Prisma.Decimal` and `lib/rates/decimal.ts`.
- **Zero-Volume Division Guards**: When a corridor exhibits zero volume or missing quotes during an aggregation window, rate calculation must gracefully yield `INSUFFICIENT_SOURCES` rather than triggering a division-by-zero or emitting `NaN`.
- **Clock Drift & Timestamp Future-Skew**: Anchor servers operating with slight clock skew can emit quote timestamps in the near future. Rate engines must bound clock skew acceptance to `+300 seconds` while rejecting timestamps further ahead.

#### 2. Database Connection & Failover Resilience
- **Socket Eviction on Failover**: When AWS Aurora or Supabase executes a primary failover, idle pooled connections can enter a read-only or broken pipe state (`ECONNRESET`). Issues harden `lib/db/poolManager.ts` to actively purge poisoned sockets rather than attempting sequential queries on dead file descriptors.
- **Transaction Rollback Leakage**: Ensuring uncommitted transactions in `lib/stellar/registryReconciliation.ts` roll back cleanly without leaving partial records or orphan corridor associations.

#### 3. Client Disconnections & Request Abort (HTTP 499)
- Handling `AbortSignal` across long-running aggregation or evidence retrieval API requests. If a client terminates the connection mid-flight, database streams and connection handles must close immediately, returning HTTP 499 internally without polluting server error logs.

#### 4. Dashboard State Preservation
- When background polling encounters transient HTTP 503 or network drops, dashboard panels must display a non-destructive banner preserving the last known fresh evidence rather than flashing blank or emptying the DOM.

---

### Category B: New Features & Capabilities (`type: feature`)
New feature issues expand the utility of StellarCore without compromising the core read/write segregation architecture.

#### 1. Additional Reviewed Rate Sources & Anchor Onboarding
- Expanding the checked-in registry in `constants/anchors.ts` and `constants/corridors.ts` to integrate new compliant anchors across Africa, Latin America, Southeast Asia, and Europe.
- Enforcing the rule that corridor medians require `MIN_FRESH_SOURCES=2`. Introducing secondary independent SEP-38 rate sources for critical corridors (e.g., `USDC/BRL`, `USDC/KES`, `EURC/NGN`).

#### 2. TransferOutcome Ingestion Architecture (RFC Implementation)
- Building the foundation for off-chain settlement tracking per `docs/rfc-outcome-ingestion-architecture.md`.
- Implementing cryptographically signed webhooks that verify anchor settlement receipts, calculate actual slippage vs indicative quote, and persist outcomes to `TransferOutcome`.

#### 3. Corridor Health Heuristics & Anomaly Detection
- Implementing pure-function heuristics in `lib/rates/anomaly.ts` to detect sudden spread spikes (> 500 bps over 15-minute moving average) or anchor heartbeat silence.
- Adding an evidence-backed anomaly indicator to the corridor detail page.

#### 4. Dashboard Enhancements & Responsive Visualization
- Interactive rate spread history charts using lightweight SVG/Canvas with zero heavy charting dependencies.
- Advanced corridor filtering by asset class (fiat-backed, stablecoin, native), anchor status, and reputation score tier.

---

### Category C: Testing, Invariants & Verification (`type: test`)
Robustness in StellarCore is maintained through multi-layer automated testing: property-based fuzzing, database integrity audits, and Playwright browser journeys.

#### 1. Property-Based Fuzzing (`tests/property/`)
- Using `fast-check` to generate millions of randomized, untrusted payloads mimicking malformed `stellar.toml` files, corrupted SEP-38 JSON responses, and malicious UTF-8/homoglyph domain names.
- Proving that parsers fail closed without unhandled exceptions or memory spikes.

#### 2. Deterministic Reputation Invariant Testing
- Verifying the mathematical guarantees of `lib/reputation/score.ts`:
  - Output is strictly integer-bounded between `0` and `10,000` basis points.
  - Adding an additional verified positive outcome never decreases an anchor's score.
  - Scores map deterministically to `GREEN`, `AMBER`, or `RED` score bands with zero boundary ambiguity.

#### 3. Playwright Browser E2E & Visual Regressions
- Automated headless browser suites testing complete user journeys: navigating from the landing page, selecting corridors, inspecting historical rate charts, and testing keyboard accessibility.
- Emulating degraded network responses (HTTP 500, slow 3G) to verify that loading skeletons and retry controls behave correctly.

#### 4. Differential Database Index Auditing
- Validating query plans (`EXPLAIN (ANALYZE, BUFFERS)`) for `findLatestObservations` under synthetic database sizes of 100,000+ snapshots. Proving that PostgreSQL uses index-only scans on `rate_snapshots_latest_observation_idx`.

---

### Category D: Documentation, Developer Experience & Runbooks (`type: docs`)
Documentation issues ensure that contributors, operators, and auditors have exact, step-by-step guidance.

#### 1. Operational Diagnostic Runbooks (`docs/`)
- Authoring runbooks for production operators: diagnosing empty public evidence views, managing certificate rotation, handling corridor staleness alerts, and executing disaster recovery drills.

#### 2. OpenAPI Specification & Contract Sync
- Generating and maintaining the OpenAPI 3.1 specification for all public routes under `/api`.
- Keeping canonical JSON fixtures in `tests/fixtures/api/` synchronized with API serializers via `npm run audit:compatibility`.

#### 3. Local Scaffolding & Mock Server Tooling
- Creating developer-friendly local setup tooling: Docker Compose configurations for PostgreSQL with pre-seeded fixture data, and lightweight mock HTTP servers simulating anchor SEP-1/SEP-38 endpoints.

---

### Category E: Security, Observability & Architecture (`type: security`, `type: chore`)
Issues dedicated to hardening system boundaries, eliminating supply-chain vulnerabilities, and enforcing operational safety.

#### 1. Strict TLS & Secret Redaction Sweeps
- Enforcing `lib/database/tlsPolicy.ts` across all database adapters.
- Authoring automated AST lint rules that detect and prevent logging of sensitive authorization headers, bearer tokens, or PostgreSQL connection strings.

#### 2. Static Raw SQL Boundary Verification
- Enhancing `npm run audit:sql` to prevent developers from introducing raw string interpolation into Prisma queries, enforcing parameterized queries across the codebase.

#### 3. Release Provenance & Software Bill of Materials (SBOM)
- Maintaining `npm run release:manifest` to generate reproducible CycloneDX SBOMs, ensuring full visibility into runtime dependencies and preventing dependency confusion attacks.

---

## 4. Multi-Wave Sprint Roadmap & Concrete Scoped Issues

Below is the master backlog of **24 granular, ready-to-post issues** distributed across four planned Wave Program sprint cycles.

```text
┌────────────────────────────────────────────────────────────────────────┐
│                   Multi-Wave Sprint Implementation Roadmap             │
├─────────────────┬──────────────────────────────────┬───────────────────┤
│ Sprint Cycle    │ Primary Focus                    │ Issues            │
├─────────────────┼──────────────────────────────────┼───────────────────┤
│ Wave Sprint 1   │ Registry & Data Engine Hardening │ #W1-01 to #W1-06  │
│ Wave Sprint 2   │ Rate Intelligence & Outlier Math │ #W2-01 to #W2-06  │
│ Wave Sprint 3   │ Settlement Proofs & Webhook RFC  │ #W3-01 to #W3-06  │
│ Wave Sprint 4   │ Visual Analytics & Public DevEx  │ #W4-01 to #W4-06  │
└─────────────────┴──────────────────────────────────┴───────────────────┘
```

---

### Wave Sprint 1: Registry Expansion & Data Engine Hardening

#### Issue #W1-01: Onboard Reviewed Anchor Definition for YellowCard (NGN/KES)
- **Labels**: `drips-wave`, `wave-sprint-1`, `complexity: trivial`, `area:stellar`, `type: feature`
- **Points**: 100 USDC
- **Context & Goal**:
  YellowCard is a leading fiat on/off-ramp operating across Sub-Saharan Africa. We need to add YellowCard's reviewed anchor definition to our static registry and associate it with the `USDC:NGN` and `USDC:KES` corridors.
- **Affected Files**:
  - `constants/anchors.ts`
  - `constants/corridors.ts`
  - `tests/fixtures/stellar/yellowcard.toml`
- **Scope & Implementation Tasks**:
  1. Add `yellowcard` entry to `constants/anchors.ts` with valid `homeDomain: "yellowcard.io"`, official signing key, and supported SEPs (`[1, 10, 38]`).
  2. Map `yellowcard` to corridors `usdc-ngn` and `usdc-kes` in `constants/corridors.ts`.
  3. Commit a verified historical snapshot fixture of `stellar.toml` in `tests/fixtures/stellar/yellowcard.toml`.
- **Non-Goals**:
  - Do NOT invoke live network calls to fetch `yellowcard.io/.well-known/stellar.toml`.
  - Do NOT modify the Prisma database schema.
- **Acceptance Criteria**:
  - `npm run audit:config` passes with zero discrepancies.
  - `npm test tests/unit/stellar/corridorRegistry.test.ts` passes.
- **Verification Command**:
  ```bash
  npm run audit:config && npm test tests/unit/stellar/
  ```

---

#### Issue #W1-02: Defensive Retry-After Header Calculation for HTTP 429 Responses
- **Labels**: `drips-wave`, `wave-sprint-1`, `complexity: trivial`, `area:api`, `type: bug`
- **Points**: 100 USDC
- **Context & Goal**:
  When external consumers exceed rate limits, `lib/api/rateLimiter.ts` returns an HTTP 429. However, the response currently lacks the standard HTTP `Retry-After` header. We must calculate the remaining window duration in whole seconds and append this header.
- **Affected Files**:
  - `lib/api/rateLimiter.ts`
  - `tests/unit/api/rateLimiter.test.ts`
- **Scope & Implementation Tasks**:
  1. In `lib/api/rateLimiter.ts`, calculate `retryAfterSeconds = Math.ceil((resetTimestampMs - Date.now()) / 1000)`.
  2. Append `Retry-After: <retryAfterSeconds>` to response headers when rate limited.
  3. Ensure `retryAfterSeconds` is clamped between 1 and 60 seconds.
  4. Write unit tests in `tests/unit/api/rateLimiter.test.ts` using mock system timers (`vi.useFakeTimers()`).
- **Non-Goals**:
  - Do NOT alter the JSON response payload schema (`error.code`, `error.message`).
- **Acceptance Criteria**:
  - HTTP 429 responses consistently contain a valid numeric `Retry-After` header.
  - All existing rate limiting tests remain green.
- **Verification Command**:
  ```bash
  npx vitest run tests/unit/api/rateLimiter.test.ts
  ```

---

#### Issue #W1-03: Homoglyph and IDN Domain Sanitization in SEP-1 Discovery
- **Labels**: `drips-wave`, `wave-sprint-1`, `complexity: medium`, `area:stellar`, `area:security`, `type: bug`
- **Points**: 150 USDC
- **Context & Goal**:
  Malicious actors can register Internationalized Domain Names (IDNs) using lookalike unicode characters (homoglyphs) to spoof trusted anchor domains. We must canonicalize and punycode-encode domain names in `lib/stellar/hostnameCanonicalization.ts` before conducting TLS handshakes.
- **Affected Files**:
  - `lib/stellar/hostnameCanonicalization.ts`
  - `lib/stellar/sep1.ts`
  - `tests/unit/stellar/hostnameCanonicalization.test.ts`
- **Scope & Implementation Tasks**:
  1. Enhance `canonicalizeHostname(domain: string): string` to convert input to lowercase, strip trailing dots, and convert unicode characters to Punycode ASCII using `node:url` or `punycode`.
  2. Reject domains containing mixed scripts, control characters, or invalid DNS characters.
  3. Add a unit test table covering Cyrillic homoglyphs (e.g. Cyrillic 'а' replacing Latin 'a').
- **Non-Goals**:
  - Do NOT introduce external third-party dependencies; use Node.js standard built-ins.
- **Acceptance Criteria**:
  - All anchor domain resolutions pass through canonicalization.
  - Mixed-script spoof domains are rejected with `INVALID_DOMAIN_ENCODING`.
- **Verification Command**:
  ```bash
  npx vitest run tests/unit/stellar/hostnameCanonicalization.test.ts
  ```

---

#### Issue #W1-04: Automated AST Verification for Secret Redaction in Server Logs
- **Labels**: `drips-wave`, `wave-sprint-1`, `complexity: medium`, `area:security`, `type: test`
- **Points**: 150 USDC
- **Context & Goal**:
  To adhere to our threat model (`docs/threat-model.md`), log serializers must never capture bearer tokens, `CRON_SECRET`, database passwords, or private keys. We need a static AST analysis test that checks all logging calls across `lib/` and `app/api/`.
- **Affected Files**:
  - `lib/tooling/secretRedactionAudit.ts`
  - `tests/unit/security/secretRedactionAst.test.ts`
- **Scope & Implementation Tasks**:
  1. Build an AST scanner using TypeScript compiler API (`ts.createSourceFile`).
  2. Inspect arguments passed to `console.log`, `console.error`, and `structuredLog`.
  3. Assert that identifier names such as `secret`, `password`, `token`, `key`, or `authorization` are never logged without passing through `redactSecret()`.
- **Acceptance Criteria**:
  - Test scans all `.ts` files under `app/api/` and `lib/scheduled/`.
  - Violations report file name, line number, and offending symbol.
- **Verification Command**:
  ```bash
  npm test tests/unit/security/secretRedactionAst.test.ts
  ```

---

#### Issue #W1-05: Database Connection Lifetime Recycling & Idle Timeout Pool Eviction
- **Labels**: `drips-wave`, `wave-sprint-1`, `complexity: high`, `area:database`, `type: bug`
- **Points**: 200 USDC
- **Context & Goal**:
  Under long-running Next.js container deployments, idle PostgreSQL connections can be terminated by intermediary firewalls, resulting in silent socket degradation. We must configure TCP keep-alives and maximum connection lifetimes in `lib/db/poolManager.ts`.
- **Affected Files**:
  - `lib/db/poolManager.ts`
  - `tests/unit/database/poolManager.test.ts`
- **Scope & Implementation Tasks**:
  1. Configure `pg.Pool` options:
     - `idleTimeoutMillis: 30000` (evict idle connections after 30s).
     - `maxLifetimeSeconds: 1800` (recycle connections after 30 minutes).
     - `keepAlive: true` and `keepAliveInitialDelayMillis: 10000`.
  2. Implement an error hook on `pool.on('error', ...)` that isolates terminated clients and triggers garbage collection of dead pool resources.
  3. Write mock connection lifecycle tests simulating socket hang-ups.
- **Non-Goals**:
  - Do NOT remove or bypass `lib/database/tlsPolicy.ts`.
- **Acceptance Criteria**:
  - Disconnected sockets are removed from the pool without raising unhandled node process exceptions.
  - Reconnect test suite in `tests/unit/database/poolManager.test.ts` succeeds.
- **Verification Command**:
  ```bash
  npm test tests/unit/database/poolManager.test.ts
  ```

---

#### Issue #W1-06: Property-Based Fuzzing for Untrusted SEP-1 TOML Payloads
- **Labels**: `drips-wave`, `wave-sprint-1`, `complexity: high`, `area:stellar`, `area:testing`, `type: test`
- **Points**: 200 USDC
- **Context & Goal**:
  Anchor `stellar.toml` files are fetched from untrusted origins. We must prove through property-based fuzz testing that parser `lib/stellar/sep1.ts` cannot be crashed by prototype pollution, gigantic arrays, circular references, or binary gibberish.
- **Affected Files**:
  - `tests/property/sep1Property.test.ts`
  - `lib/stellar/sep1.ts`
- **Scope & Implementation Tasks**:
  1. Author `tests/property/sep1Property.test.ts` using `fast-check`.
  2. Generate arbitrary inputs: multi-megabyte strings, deep nested structures, malformed TOML keys, Unicode control characters, and duplicated TOML tables.
  3. Assert `parseStellarToml()` always either returns a valid typed `StellarToml` object or rejects with a clean `Sep1ParseError`.
- **Acceptance Criteria**:
  - Executes 10,000 iterations without uncaught exceptions or memory leakage.
  - Reproducible seed logged upon any test failure.
- **Verification Command**:
  ```bash
  npx tsx --test tests/property/sep1Property.test.ts
  ```

---

### Wave Sprint 2: Rate Intelligence, Median Outliers & Corridor Discovery

#### Issue #W2-01: ISO 4217 Currency Code Validation in Corridor Registration
- **Labels**: `drips-wave`, `wave-sprint-2`, `complexity: trivial`, `area:rates`, `type: bug`
- **Points**: 100 USDC
- **Context & Goal**:
  Corridor assets must follow strict naming: either valid ISO 4217 fiat codes (e.g. `USD`, `BRL`, `NGN`) or valid Stellar asset codes (e.g. `USDC`, `EURC`). We must add a static validation rule in `lib/stellar/corridorSync.ts`.
- **Affected Files**:
  - `lib/stellar/corridorRegistry.ts`
  - `constants/corridors.ts`
  - `tests/unit/stellar/corridorRegistry.test.ts`
- **Scope & Implementation Tasks**:
  1. Add asset code validation regex: `/^[A-Z0-9]{3,12}$/`.
  2. Verify country codes conform to ISO 3166-1 alpha-2 (e.g. `US`, `BR`, `NG`).
  3. Add unit test assertions catching malformed corridor configurations.
- **Acceptance Criteria**:
  - `npm run audit:config` catches invalid asset/country codes at build time.
- **Verification Command**:
  ```bash
  npm run audit:config && npm test tests/unit/stellar/corridorRegistry.test.ts
  ```

---

#### Issue #W2-02: Rate Snapshot Expiration Boundary Inclusive Edge-Case Fix
- **Labels**: `drips-wave`, `wave-sprint-2`, `complexity: trivial`, `area:rates`, `type: bug`
- **Points**: 100 USDC
- **Context & Goal**:
  Rate freshness is evaluated against a configured threshold (`FRESHNESS_THRESHOLD_SECONDS = 300`). Currently, an observation whose age is *exactly* 300.000 seconds is inconsistently flagged as stale due to strict inequality (`>`) vs inclusive comparison (`>=`).
- **Affected Files**:
  - `lib/rates/freshness.ts`
  - `tests/unit/rates/freshnessCutoff.test.ts`
- **Scope & Implementation Tasks**:
  1. Update `isRateFresh(capturedAt, now, thresholdSeconds)` in `lib/rates/freshness.ts` to implement inclusive cutoff: `ageSeconds <= thresholdSeconds`.
  2. Add unit test asserting that an observation exactly at boundary (300.000s) evaluates as fresh, while 300.001s evaluates as stale.
- **Acceptance Criteria**:
  - Deterministic evaluation verified to the millisecond.
- **Verification Command**:
  ```bash
  npm test tests/unit/rates/freshnessCutoff.test.ts
  ```

---

#### Issue #W2-03: ARIA Live Status Announcements in Dashboard Rate Polling
- **Labels**: `drips-wave`, `wave-sprint-2`, `complexity: medium`, `area:dashboard`, `type: feature`
- **Points**: 150 USDC
- **Context & Goal**:
  When rate panels refresh periodically in the browser dashboard (`components/dashboard/RatePanel.tsx`), visually impaired users using screen readers receive no feedback. We must add polite ARIA live regions announcing rate updates and staleness warnings.
- **Affected Files**:
  - `components/dashboard/RatePanel.tsx`
  - `components/ui/EvidenceTimestamp.tsx`
  - `tests/unit/frontend/dashboardAccessibility.test.ts`
- **Scope & Implementation Tasks**:
  1. Add `<div aria-live="polite" className="sr-only">` to announce status changes (e.g. "Rates refreshed for USDC/BRL: 5.42 BRL", "Rates are stale: waiting for fresh evidence").
  2. Ensure announcements do not steal keyboard focus or re-trigger on un-changed state.
  3. Write accessibility tests using `@testing-library/react`.
- **Acceptance Criteria**:
  - Zero automated axe-core accessibility violations.
  - Unit tests confirm announcement text rendering.
- **Verification Command**:
  ```bash
  npm test tests/unit/frontend/dashboardAccessibility.test.ts
  ```

---

#### Issue #W2-04: Dynamic Source Suppression Counters & Circuit Breaker
- **Labels**: `drips-wave`, `wave-sprint-2`, `complexity: medium`, `area:rates`, `type: feature`
- **Points**: 150 USDC
- **Context & Goal**:
  If an anchor's SEP-38 quote endpoint fails consecutively across multiple cron runs, we must prevent redundant outbound requests by temporarily suppressing the candidate via `ScheduledSourceSuppression` in PostgreSQL.
- **Affected Files**:
  - `lib/scheduled/suppression.ts`
  - `lib/scheduled/suppressionRepository.ts`
  - `tests/unit/stellar/anchorSuppressionCounters.test.ts`
- **Scope & Implementation Tasks**:
  1. Increment `consecutiveFailures` counter on each failure.
  2. If `consecutiveFailures >= 5`, update `state` to `SUPPRESSED` and set `suppressedAt = now`.
  3. When an anchor succeeds after cooldown, transition state to `ACTIVE` and log `reactivationReason`.
  4. Write unit tests proving suppression counter increments deterministically once per cron execution.
- **Acceptance Criteria**:
  - Suppressed sources are bypassed during candidate preparation.
  - Zero infinite-retry storms against failing anchors.
- **Verification Command**:
  ```bash
  npm test tests/unit/stellar/anchorSuppressionCounters.test.ts
  ```

---

#### Issue #W2-05: Multi-Source Median Calculation with Outlier Elimination
- **Labels**: `drips-wave`, `wave-sprint-2`, `complexity: high`, `area:rates`, `type: feature`
- **Points**: 200 USDC
- **Context & Goal**:
  When corridors have >= 4 independent rate sources, rogue or anomalous quotes can distort the simple median. We must introduce an outlier-resistant median calculation algorithm using Interquartile Range (IQR) filtering before computing the corridor midpoint.
- **Affected Files**:
  - `lib/rates/median.ts`
  - `tests/unit/rates/median.test.ts`
  - `tests/unit/rates/median.property.test.ts`
- **Scope & Implementation Tasks**:
  1. Enhance `computeCorridorMedian(rates: RateObservation[]): MedianResult`.
  2. For `N >= 4`, calculate Q1, Q3, and `IQR = Q3 - Q1`. Filter out observations where `rate < Q1 - 1.5 * IQR` or `rate > Q3 + 1.5 * IQR`.
  3. If remaining count >= `MIN_FRESH_SOURCES=2`, calculate the median of filtered observations.
  4. Preserve integer basis-point tie-breakers for even sample counts.
  5. Author property-based invariant tests proving no single rogue outlier can shift the median beyond IQR bounds.
- **Non-Goals**:
  - Do NOT modify public API JSON structure.
- **Acceptance Criteria**:
  - All unit and property tests pass.
  - Backward compatibility verified with `npm run audit:compatibility`.
- **Verification Command**:
  ```bash
  npm test tests/unit/rates/median.test.ts tests/unit/rates/median.property.test.ts
  ```

---

#### Issue #W2-06: Database Explain-Analyze Benchmark Harness for Rate Snapshots
- **Labels**: `drips-wave`, `wave-sprint-2`, `complexity: high`, `area:database`, `type: test`
- **Points**: 200 USDC
- **Context & Goal**:
  The `rate_snapshots` table grows rapidly. We must ensure the `findLatestObservations` query continues using an index-only scan on `rate_snapshots_latest_observation_idx` as volume scales to 500,000+ rows.
- **Affected Files**:
  - `scripts/benchmarkLatestObservations.ts`
  - `tests/integration/database/latestObservationsBenchmark.test.ts`
- **Scope & Implementation Tasks**:
  1. Author a synthetic benchmark script that seeds 100,000 rate snapshots across 10 anchors and 20 corridors into a local throwaway database (`BENCHMARK_DATABASE_URL`).
  2. Execute `EXPLAIN (ANALYZE, BUFFERS, FORMAT JSON)` on the `DISTINCT ON (anchor_id)` query.
  3. Assert `Index Only Scan` is utilized with 0 heap fetches.
  4. Fail execution if sequence scan or sort buffer overflow (`Sort Method: external merge`) is detected.
- **Non-Goals**:
  - Never execute benchmarks against production databases.
- **Acceptance Criteria**:
  - Benchmark runs cleanly via `npm run benchmark:latest-observations`.
- **Verification Command**:
  ```bash
  npm run benchmark:latest-observations
  ```

---

### Wave Sprint 3: Settlement Proofs, Webhooks & Off-Chain Verification

#### Issue #W3-01: Transfer Status Enum Synchronization & Serialization Guard
- **Labels**: `drips-wave`, `wave-sprint-3`, `complexity: trivial`, `area:api`, `type: bug`
- **Points**: 100 USDC
- **Context & Goal**:
  `TransferStatus` in `prisma/schema.prisma` supports `COMPLETED`, `PARTIAL`, `REFUNDED`, `EXPIRED`, and `ERROR`. We must ensure our API serializer in `lib/api/serialization.ts` maps these enum values safely to lowercase public strings without string coercion errors.
- **Affected Files**:
  - `lib/api/serialization.ts`
  - `tests/unit/api/serialization.test.ts`
- **Scope & Implementation Tasks**:
  1. Add strict exhaustive switch check for all `TransferStatus` enum members.
  2. Ensure TypeScript throws compile-time errors if a new status enum is added without serializer handling.
  3. Write unit tests verifying round-trip serialization.
- **Acceptance Criteria**:
  - TypeScript exhaustive type guard verified.
  - Serializer tests pass.
- **Verification Command**:
  ```bash
  npx tsc --noEmit && npm test tests/unit/api/serialization.test.ts
  ```

---

#### Issue #W3-02: Cryptographic Timestamp Tolerance & Nonce Validation
- **Labels**: `drips-wave`, `wave-sprint-3`, `complexity: trivial`, `area:security`, `type: feature`
- **Points**: 100 USDC
- **Context & Goal**:
  Webhook delivery systems are vulnerable to replay attacks. In `lib/stellar/outcomeWebhook.ts`, we must enforce strict timestamp skew bounds and track recent payload nonces.
- **Affected Files**:
  - `lib/stellar/outcomeWebhook.ts`
  - `tests/unit/stellar/outcomeWebhookSecurity.test.ts`
- **Scope & Implementation Tasks**:
  1. Reject webhook payloads where `|payload.timestamp - Date.now()| > 300_000` (5 minutes).
  2. Implement an in-memory sliding window cache (LRU) for payload UUIDs to reject replayed requests.
  3. Author unit tests covering clock skew boundaries and duplicate nonce submission.
- **Acceptance Criteria**:
  - Expired or future webhooks are rejected with `WEBHOOK_TIMESTAMP_OUT_OF_BOUNDS`.
  - Duplicate nonces reject with `WEBHOOK_NONCE_REPLAYED`.
- **Verification Command**:
  ```bash
  npm test tests/unit/stellar/outcomeWebhookSecurity.test.ts
  ```

---

#### Issue #W3-03: Reputation Window Trailing Calculation (7d, 30d, 90d)
- **Labels**: `drips-wave`, `wave-sprint-3`, `complexity: medium`, `area:reputation`, `type: feature`
- **Points**: 150 USDC
- **Context & Goal**:
  The reputation engine calculates `fillRate7d`, `fillRate30d`, and `fillRate90d` from `TransferOutcome` records. We must write a dedicated windowing utility in `lib/reputation/score.ts` that calculates fill rates using deterministic integer math.
- **Affected Files**:
  - `lib/reputation/score.ts`
  - `tests/unit/reputation/fillRateWindows.test.ts`
- **Scope & Implementation Tasks**:
  1. Group transfer outcomes by trailing cutoff timestamps (`now - 7d`, `now - 30d`, `now - 90d`).
  2. Compute `fillRate = Math.round((completedCount * 10000) / totalCount) / 10000`.
  3. Return `null` when a trailing window has 0 recorded transfers.
  4. Write table-driven unit tests verifying fill rate calculations across varied transfer histories.
- **Acceptance Criteria**:
  - Integer basis-point rounding verified.
  - Zero division errors on empty histories.
- **Verification Command**:
  ```bash
  npm test tests/unit/reputation/fillRateWindows.test.ts
  ```

---

#### Issue #W3-04: Non-Destructive Integrity Audit Remediation Script
- **Labels**: `drips-wave`, `wave-sprint-3`, `complexity: medium`, `area:integrity`, `type: feature`
- **Points**: 150 USDC
- **Context & Goal**:
  When `npm run audit:integrity` flags orphaned rate snapshots or mismatched corridor associations, operators need automated, non-destructive SQL generation scripts that produce dry-run remediation commands without auto-executing writes.
- **Affected Files**:
  - `lib/integrity/remediation.ts`
  - `scripts/generateRemediationSql.ts`
  - `tests/unit/integrity/remediation.test.ts`
- **Scope & Implementation Tasks**:
  1. Implement `formatRemediationScript(findings: IntegrityFinding[]): string`.
  2. Output safe, parameterized PostgreSQL scripts wrapped in `BEGIN; ... ROLLBACK;` dry-run blocks.
  3. Ensure scripts provide commented audit explanations for every remediation step.
  4. Write unit tests verifying script output against known corruption fixtures.
- **Acceptance Criteria**:
  - Script output is pure text; performs zero database mutations.
- **Verification Command**:
  ```bash
  npm test tests/unit/integrity/remediation.test.ts
  ```

---

#### Issue #W3-05: HMAC-SHA256 Webhook Ingestion Engine with Constant-Time Verification
- **Labels**: `drips-wave`, `wave-sprint-3`, `complexity: high`, `area:security`, `area:stellar`, `type: feature`
- **Points**: 200 USDC
- **Context & Goal**:
  Implement the production webhook receiver for off-chain settlement evidence as specified in `docs/rfc-outcome-webhook-schema.md`. All payloads must be verified against anchor pre-shared secrets using constant-time cryptographic comparisons.
- **Affected Files**:
  - `lib/stellar/outcomeWebhook.ts`
  - `app/api/webhooks/outcomes/route.ts`
  - `tests/unit/stellar/outcomeWebhook.test.ts`
- **Scope & Implementation Tasks**:
  1. Extract `X-StellarCore-Signature` and `X-StellarCore-Timestamp` headers.
  2. Compute HMAC-SHA256 over `${timestamp}.${rawBody}` using configured secret.
  3. Compare computed signature with header using `crypto.timingSafeEqual` to guard against timing attacks.
  4. Validate JSON payload against Zod schema (`anchorSlug`, `corridorSlug`, `fillRate`, `settlementMs`, `slippage`).
  5. Persist valid outcomes to PostgreSQL `TransferOutcome` table.
- **Non-Goals**:
  - Do NOT log raw payloads or secret keys.
- **Acceptance Criteria**:
  - Invalid signatures reject with HTTP 401 within constant time.
  - Successfully verified webhooks return HTTP 202 Accepted.
- **Verification Command**:
  ```bash
  npm test tests/unit/stellar/outcomeWebhook.test.ts
  ```

---

#### Issue #W3-06: Database Failover Simulation & Transaction Interruption Suite
- **Labels**: `drips-wave`, `wave-sprint-3`, `complexity: high`, `area:database`, `type: test`
- **Points**: 200 USDC
- **Context & Goal**:
  During AWS Aurora Postgres failovers, transactions in progress receive `57P01` (`admin_shutdown`) or `08006` (`connection_failure`). We must write a fault-injection test suite verifying that our transaction manager (`lib/db/transactionSafety.ts`) retries safely without committing duplicate records.
- **Affected Files**:
  - `lib/db/transactionSafety.ts`
  - `tests/unit/database/transactionFailoverFaultInjection.test.ts`
- **Scope & Implementation Tasks**:
  1. Build a mock Postgres client that injects `57P01` errors during `COMMIT` execution.
  2. Verify that `withSafeTransaction()` catches the failover error, rolls back local state, and retries with exponential jittered backoff.
  3. Prove that non-idempotent operations fail closed rather than duplicate records.
- **Acceptance Criteria**:
  - Zero duplicate database writes under simulated failover.
  - Transient failover errors recover within 3 retry attempts.
- **Verification Command**:
  ```bash
  npm test tests/unit/database/transactionFailoverFaultInjection.test.ts
  ```

---

### Wave Sprint 4: Dashboard Experience, Visual Analytics & Ecosystem Tooling

#### Issue #W4-01: Human-Readable File-Size & Rate Decimal Formatting Polish
- **Labels**: `drips-wave`, `wave-sprint-4`, `complexity: trivial`, `area:dashboard`, `type: bug`
- **Points**: 100 USDC
- **Context & Goal**:
  When displaying rates for high-denomination currency pairs (e.g., `USDC/IDR` = 15,750.50) vs micro-value pairs (e.g., `XLM/USDC` = 0.1254), fixed decimal notation breaks table readability. We must introduce adaptive decimal formatting.
- **Affected Files**:
  - `lib/rates/decimal.ts`
  - `components/dashboard/RatePanel.tsx`
  - `tests/unit/rates/decimal.test.ts`
- **Scope & Implementation Tasks**:
  1. Implement `formatAdaptiveRate(rate: Decimal): string`:
     - Rate >= 100: 2 decimal places with locale thousands separators (`15,750.50`).
     - 1 <= Rate < 100: 4 decimal places (`5.4210`).
     - Rate < 1: 6 decimal places (`0.125400`).
  2. Add unit tests covering boundary values and extreme values.
- **Acceptance Criteria**:
  - Consistent layout rendering across all supported corridor pairs.
- **Verification Command**:
  ```bash
  npm test tests/unit/rates/decimal.test.ts
  ```

---

#### Issue #W4-02: Developer Docker Compose Setup for Offline Postgres & Seed Fixtures
- **Labels**: `drips-wave`, `wave-sprint-4`, `complexity: trivial`, `area:docs`, `type: chore`
- **Points**: 100 USDC
- **Context & Goal**:
  Contributors often experience friction setting up a local PostgreSQL instance with SSL and UUID extensions. We need a zero-configuration Docker Compose setup with pre-seeded sample anchors and corridors.
- **Affected Files**:
  - `docker-compose.dev.yml`
  - `scripts/seedLocalDevelopment.ts`
  - `README.md`
- **Scope & Implementation Tasks**:
  1. Author `docker-compose.dev.yml` with `postgres:16-alpine`.
  2. Mount an initialization script enabling `uuid-ossp` and `pgcrypto`.
  3. Add `npm run dev:db` script in `package.json`.
  4. Document quick-start instructions in `README.md`.
- **Acceptance Criteria**:
  - Running `docker compose -f docker-compose.dev.yml up -d` starts a healthy Postgres instance ready for `npx prisma migrate dev`.
- **Verification Command**:
  ```bash
  docker compose -f docker-compose.dev.yml config
  ```

---

#### Issue #W4-03: Keyboard Shortcuts & Focus Trapping for Modal Dialogs
- **Labels**: `drips-wave`, `wave-sprint-4`, `complexity: medium`, `area:dashboard`, `type: feature`
- **Points**: 150 USDC
- **Context & Goal**:
  The corridor detail and evidence modal dialogs in the dashboard must be fully navigable via keyboard, trapping focus when open and restoring focus to the triggering element upon closing (Escape key).
- **Affected Files**:
  - `components/corridors/CorridorDetailModal.tsx`
  - `hooks/useFocusTrap.ts`
  - `tests/unit/frontend/dashboardRetryKeyboard.test.ts`
- **Scope & Implementation Tasks**:
  1. Author reusable `useFocusTrap(isOpen, modalRef)` hook.
  2. Trap Tab / Shift+Tab within modal bounds.
  3. Close modal on `Escape` keypress and restore focus to trigger button.
  4. Add unit test coverage in `tests/unit/frontend/dashboardRetryKeyboard.test.ts`.
- **Acceptance Criteria**:
  - WCAG 2.1 compliance for modal focus management verified.
- **Verification Command**:
  ```bash
  npm test tests/unit/frontend/dashboardRetryKeyboard.test.ts
  ```

---

#### Issue #W4-04: Automated OpenAPI 3.1 Contract Generation & Drift Gate
- **Labels**: `drips-wave`, `wave-sprint-4`, `complexity: medium`, `area:api`, `area:docs`, `type: feature`
- **Points**: 150 USDC
- **Context & Goal**:
  Public APIs change over time. We must prevent drift between our Next.js route serializers and our published OpenAPI 3.1 documentation by generating the OpenAPI schema directly from Zod route contracts.
- **Affected Files**:
  - `scripts/generateOpenApiSchema.ts`
  - `docs/openapi.json`
  - `tests/unit/compatibility/contractCoverage.test.ts`
- **Scope & Implementation Tasks**:
  1. Author generation script utilizing `zod-to-openapi` or TypeScript AST.
  2. Map all routes under `app/api/` (`/anchors`, `/corridors`, `/rates`, `/reputation`).
  3. Add CI gate in `tests/unit/compatibility/contractCoverage.test.ts` that asserts `docs/openapi.json` is identical to generated output.
- **Acceptance Criteria**:
  - CI fails if an API route serializer is updated without updating `docs/openapi.json`.
- **Verification Command**:
  ```bash
  npm run audit:compatibility && npm test tests/unit/compatibility/
  ```

---

#### Issue #W4-05: SVG Corridor Spread History Visualization with Zero Dependencies
- **Labels**: `drips-wave`, `wave-sprint-4`, `complexity: high`, `area:dashboard`, `type: feature`
- **Points**: 200 USDC
- **Context & Goal**:
  To maintain high performance and avoid massive bundle sizes, we forbid heavy charting libraries (like Chart.js or D3). We must build a lightweight, server-rendered SVG sparkline and spread history component in `components/corridors/CorridorRateHistoryChart.tsx`.
- **Affected Files**:
  - `components/corridors/CorridorRateHistoryChart.tsx`
  - `lib/ui/svgChartGeometry.ts`
  - `tests/unit/frontend/rateHistoryChart.test.ts`
- **Scope & Implementation Tasks**:
  1. Implement pure geometry generator calculating SVG path `d="..."` strings from rate history arrays.
  2. Render interactive hover dots and tooltips using pure React state.
  3. Ensure SVG elements include semantic accessibility tags (`<title>`, `<desc>`, role="img").
  4. Add unit tests verifying SVG coordinate calculations and responsive viewbox scaling.
- **Non-Goals**:
  - Do NOT import any client-side charting libraries.
- **Acceptance Criteria**:
  - Bundle size impact < 5KB.
  - Fully responsive and accessible to screen readers.
- **Verification Command**:
  ```bash
  npm test tests/unit/frontend/rateHistoryChart.test.ts
  ```

---

#### Issue #W4-06: Playwright End-to-End Resilience Suite for Network Degradation
- **Labels**: `drips-wave`, `wave-sprint-4`, `complexity: high`, `area:testing`, `type: test`
- **Points**: 200 USDC
- **Context & Goal**:
  We must prove that the browser dashboard handles adverse real-world network conditions without crashing. We will implement Playwright browser tests intercepting `/api/*` routes to simulate high latency, rate limiting (HTTP 429), and server outages (HTTP 503).
- **Affected Files**:
  - `tests/e2e/dashboardResilience.spec.ts`
  - `playwright.config.ts`
- **Scope & Implementation Tasks**:
  1. Author `tests/e2e/dashboardResilience.spec.ts`.
  2. Intercept `/api/rates` to inject 2000ms delay: assert skeleton loaders render.
  3. Intercept `/api/rates` with HTTP 429: assert retry countdown badge appears.
  4. Intercept `/api/corridors` with HTTP 500: assert failure guidance and retry button display properly.
  5. Test keyboard accessibility of the retry button.
- **Acceptance Criteria**:
  - Playwright test runs cleanly in CI headless mode.
- **Verification Command**:
  ```bash
  npx playwright test tests/e2e/dashboardResilience.spec.ts
  ```

---

## 5. Issue Publishing & Contributor Operations Protocol

To guarantee a transparent, equitable, and predictable experience for all Wave Program participants, maintainers adhere to the following operational protocols.

### 5.1 Issue Creation & Scoping Quality Checklist
Before an issue is given the `drips-wave` and `status: available` labels, maintainers verify:
- [ ] Problem statement describes the exact user- or system-level objective.
- [ ] Explicit list of affected files and directories is provided.
- [ ] Clear boundaries ("Non-Goals") explicitly state what contributors must avoid.
- [ ] Acceptance criteria list exact commands that must pass.
- [ ] Offline test fixture paths are specified; zero live network calls permitted.
- [ ] Complexity tier and points (100 / 150 / 200 USDC) are assigned.

### 5.2 Contributor Claiming & Assignment Lifecycle

```text
  [Issue Posted: status: available]
                  │
                  ▼
  [Contributor Comments with Approach & Plan]
                  │
                  ▼
  [Maintainer Assigns Issue: status: claimed]
                  │
        ┌─────────┴─────────┐
        ▼                   ▼
  (Active Work)      (Inactive > 72h)
        │                   │
        │                   ▼
        │          [Issue Unassigned & Re-opened]
        ▼
  [PR Opened: status: in-review]
        │
        ├───────────────────┐
        ▼                   ▼
  (Changes Needed)     (Approved)
        │                   │
        ▼                   ▼
  [status: revisions]  [Merged to main]
                            │
                            ▼
                       [USDC Payout]
```

1. **One Active Issue Per Contributor**:
   To prevent issue hoarding and maximize community participation, contributors may only be assigned **one active issue at a time**.
2. **72-Hour Inactivity Rule**:
   If an assigned contributor does not open a draft PR or post a progress update within **72 hours**, the issue is unassigned and returned to `status: available`.
3. **Draft Pull Requests Encouraged**:
   Contributors are urged to open a Draft PR within 24 hours of claiming an issue to verify early CI pipeline checks.

---

## 6. Pull Request Review & Merge Gate

Maintainers perform code reviews according to a rigorous checklist to prevent code regressions and architectural drift.

### 6.1 Automated CI Pipeline Gates
Every opened PR must pass these automated gates in GitHub Actions:
```bash
# 1. Strict TypeScript compilation check (no errors, no any escapes)
npx tsc --noEmit

# 2. ESLint code quality & formatting verification
npm run lint

# 3. Unit and integration test suite execution
npm test

# 4. Registry consistency and offline configuration audit
npm run audit:config

# 5. Public API serialization backwards-compatibility gate
npm run audit:compatibility

# 6. Raw SQL boundary audit (no unreviewed Prisma.raw or SQL strings)
npm run audit:sql
```

### 6.2 Maintainer Code Review Rubric
Beyond passing CI, maintainers manually check:
- **Architecture Integrity**: Does the code respect read-only vs write engine boundaries?
- **Network Isolation**: Are all new tests mocking external requests?
- **Integer Math**: Are financial calculations using basis points and `Decimal` rather than floating-point math?
- **Security**: Are error responses sanitized? Does logging omit sensitive headers or tokens?
- **Changelog**: Has a concise bullet point been added to `CHANGELOG.md` under `[Unreleased]`?

---

## 7. Metrics, Program Governance & USDC Disbursement

### 7.1 Key Performance Indicators (KPIs)
Maintainers monitor four core metrics throughout each Wave sprint:
1. **Time-to-First-Review (TTFR)**: Target < 24 hours for draft and ready PRs.
2. **First-Pass Merge Rate**: Target > 70% of PRs approved within 1 review cycle.
3. **Zero Invariant Drift**: 100% compliance with `npm run audit:integrity` on `main`.
4. **Contributor Retention**: Percentage of contributors returning for subsequent wave cycles.

### 7.2 Point Reconciliation & USDC Disbursement
At the close of each 2-week sprint cycle:
1. Maintainers compile the merged PR manifest linking PRs to issue point allocations.
2. Contributor Stellar public keys (G-addresses) are verified.
3. USDC payments are distributed directly on the Stellar network via automated multi-payment transactions, tagged with memo `StellarCore Wave Bounty Sprint <N>`.
4. The public disbursement ledger is committed to `docs/wave-disbursements.md` for full community transparency.
