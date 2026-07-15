# Architecture Decisions

## ADR-001 — Start in Observer Mode

**Decision:** The first release ingests, displays and simulates. It does not issue battery commands.

**Reason:** Live telemetry and market data must be reconciled before control logic can be trusted.

## ADR-002 — Amber is the market-data plane

**Decision:** Amber provides site, tariff, price, forecast and metered-usage information.

**Reason:** Its public API is read-oriented and suited to commercial intelligence rather than physical device control.

## ADR-003 — Sigenergy is the device-control plane

**Decision:** Official Sigenergy OpenAPI is the only planned production command path.

**Reason:** It supports telemetry, system data, alarms and authorised charge/discharge instructions. Private mobile-app endpoint reverse engineering is excluded.

## ADR-004 — Browser has no provider authority

**Decision:** Lovable frontend calls only the application backend.

**Reason:** Provider credentials, webhook verification and battery commands require server-side control.

## ADR-005 — Optimiser and safety engine are separate

**Decision:** The optimiser proposes actions; a deterministic policy engine may veto them.

**Reason:** Profit logic must never be able to bypass physical, contractual or operational constraints.

## ADR-006 — Supabase is acceptable for the MVP

**Decision:** Use Supabase Auth, Postgres, RLS, Edge Functions and scheduled jobs for the initial product.

**Reason:** This is the fastest path for Lovable while retaining clear service boundaries. Advanced optimisation can later move into a dedicated service.

## ADR-007 — Raw and normalised data are both retained

**Decision:** Store restricted raw provider events and separate normalised records.

**Reason:** This enables replay, debugging, reconciliation and audit without coupling the application to vendor schemas.

## ADR-008 — Store UTC, render Adelaide time

**Decision:** Persist timestamps in UTC and display using `Australia/Adelaide`.

**Reason:** Energy market and device systems may use different time conventions; explicit conversion prevents interval errors.

## ADR-009 — Commands must expire

**Decision:** Every future command has a bounded duration and an approved stop/restore path.

**Reason:** A profitable action can become unsafe or costly if left active after market or communication conditions change.
