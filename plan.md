# StellarCore Wave Program Issue Plan

## 1. Wave Program Framework
StellarCore participates in the **Stellar Drips Wave Program**. Maintainers publish scoped, test-backed issues that community contributors claim during 2-week sprint cycles. Merged contributions earn points converting to **USDC** rewards.

### Complexity & Bounty Tiers
- **Trivial (100 pts / USDC)**: 1–2 files; isolated fixes, registry additions, docs (24–48h SLA).
- **Medium (150 pts / USDC)**: 3–5 files; parsers, accessibility, unit/property tests (3–5 days SLA).
- **High (200 pts / USDC)**: Subsystems; rate aggregation, HMAC webhooks, failover resilience (5–7 days SLA).

---

## 2. Work Types & Planned Issues

### A. Bug Fixes & Hardening
Addresses math boundaries, connection resilience, and API safety:
- **#W-B01 (Trivial · 100 pts)**: `fix(api)`: Return integer `Retry-After` header on HTTP 429 rate limits.
- **#W-B02 (Medium · 150 pts)**: `fix(rates)`: Fix inclusive timestamp cutoff in `isRateFresh()` so quotes at exactly 300s evaluate as fresh.
- **#W-B03 (High · 200 pts)**: `fix(database)`: Evict broken sockets on Aurora failover in `poolManager.ts` to prevent query failures.

### B. New Features & Capabilities
Expands anchor registries, rate sources, and off-chain proofs:
- **#W-F01 (Trivial · 100 pts)**: `feat(registry)`: Onboard YellowCard (`yellowcard.io`) for `USDC:NGN` and `USDC:KES` corridors.
- **#W-F02 (Medium · 150 pts)**: `feat(dashboard)`: Add ARIA live regions and keyboard shortcuts (`/`, `r`) for rate status.
- **#W-F03 (High · 200 pts)**: `feat(rates)`: Implement multi-source IQR outlier-resistant median calculation for corridors with >= 4 feeds.
- **#W-F04 (High · 200 pts)**: `feat(outcomes)`: Implement RFC HMAC-SHA256 outcome webhook receiver with constant-time verification.

### C. Testing & Invariants
Enforces system reliability with zero live network calls in CI:
- **#W-T01 (Medium · 150 pts)**: `test(property)`: Fast-check fuzz tests for untrusted SEP-1 TOML and SEP-38 quote parsers.
- **#W-T02 (Medium · 150 pts)**: `test(reputation)`: Invariant unit tests proving reputation scores stay within 0–10,000 bps.
- **#W-T03 (High · 200 pts)**: `test(e2e)`: Playwright suite simulating network degradation (HTTP 429/500/latency) on `/dashboard`.

### D. Documentation & DevEx
Improves developer setup, runbooks, and API contracts:
- **#W-D01 (Trivial · 100 pts)**: `docs(runbook)`: Author operational runbook for diagnosing corridor staleness alerts.
- **#W-D02 (Trivial · 100 pts)**: `chore(docker)`: Add `docker-compose.dev.yml` with pre-seeded Postgres fixtures for local setup.
- **#W-D03 (Medium · 150 pts)**: `docs(openapi)`: Add automated OpenAPI 3.1 drift gate synchronizing route serializers.

---

## 3. Contributor Lifecycle & Merge Gates
1. **Claiming**: Contributors post an implementation plan on an issue labeled `drips-wave` and `status: available`. Limit: 1 active issue per contributor; 72h inactivity unassigns.
2. **Quality Gates**: All PRs must branch from `main`, provide offline tests, and pass:
   - `npx tsc --noEmit && npm run lint && npm test`
   - `npm run audit:config && npm run audit:compatibility && npm run audit:sql`
3. **Payout**: Merged PRs are credited and disbursed in USDC to the contributor's Stellar address.
