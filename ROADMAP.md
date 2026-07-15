# Delivery Roadmap

## Phase 0 — Foundation

**Outcome:** Lovable has a controlled specification and stable project skeleton.

- [x] Initialise repository.
- [x] Define product purpose and operating principle.
- [x] Define first-pass architecture, schema and safety model.
- [ ] Confirm Sigenergy authentication, boarding and instruction payloads.
- [ ] Confirm Amber API token and site access.
- [ ] Confirm exact SigenStor model, usable capacity and power limits.
- [ ] Record VPP contractual and operational constraints.

**Exit criteria:** Required credentials exist, webhook requirements are understood, and the first build can proceed without inventing API behaviour.

---

## Phase 1 — Lovable Observer MVP

**Outcome:** A usable read-only energy command centre using mock and then live data.

### Product

- [ ] Responsive dashboard shell.
- [ ] Authentication and protected routes.
- [ ] Overview, Market, Battery, Performance, Alerts, Strategy and Settings pages.
- [ ] Connection-status panel for Amber and Sigenergy.
- [ ] Demo/seed mode for development.

### Data

- [ ] Supabase schema and migrations.
- [ ] Row-level security.
- [ ] Amber site and channel records.
- [ ] Price interval storage.
- [ ] Sigenergy telemetry, system-data and alarm storage.
- [ ] Audit-event storage.

### Integration

- [ ] Amber API server-side adapter.
- [ ] Amber current-price polling.
- [ ] Amber historical usage backfill in seven-day windows.
- [ ] Sigenergy telemetry webhook.
- [ ] Sigenergy system-data webhook.
- [ ] Sigenergy alarm webhook.
- [ ] Signature/authentication verification for inbound pushes.

**Exit criteria:** Live Amber and Sigenergy data can be inspected in one interface; no battery command code is enabled.

---

## Phase 2 — Adviser and Simulation

**Outcome:** The system recommends actions and proves its logic without controlling the battery.

- [ ] Deterministic recommendation engine.
- [ ] Charge, hold, self-consume, discharge and export recommendations.
- [ ] Forecast-confidence handling.
- [ ] Reserve, efficiency and degradation settings.
- [ ] Household-load and solar forecast inputs.
- [ ] Strategy timeline for the next 24 hours.
- [ ] Counterfactual earnings simulation.
- [ ] Compare recommendations with actual VPP behaviour.
- [ ] Daily performance summary.
- [ ] Recommendation reason codes and audit trail.

**Exit criteria:** At least 14 days of recommendations are evaluated against actual outcomes, with no unresolved critical safety or data-quality defects.

---

## Phase 3 — Supervised Commands

**Outcome:** An authorised operator can approve bounded commands.

- [ ] Implement Sigenergy command adapter.
- [ ] Implement command dry-run mode.
- [ ] Command approval workflow.
- [ ] Forced-charge command.
- [ ] Forced-discharge command.
- [ ] Stop/restore-normal command.
- [ ] Command acknowledgement and status tracking.
- [ ] Idempotency keys.
- [ ] Timeout and fail-safe restoration.
- [ ] Manual emergency stop.
- [ ] VPP override/conflict detection.

**Exit criteria:** Commands are safely executed in a controlled test window, acknowledged, audited and automatically terminated within approved bounds.

---

## Phase 4 — Guarded Automation

**Outcome:** The optimiser can act autonomously inside a narrow approved policy envelope.

- [ ] Policy-based automatic command approval.
- [ ] Freshness and alarm interlocks.
- [ ] Maximum power, duration and cycle limits.
- [ ] Minimum profit and confidence thresholds.
- [ ] Daily loss ceiling.
- [ ] Grid-outage and backup-reserve protection.
- [ ] Automatic rollback to normal mode.
- [ ] Operator notifications.
- [ ] Weekly strategy review report.

**Exit criteria:** Guarded automation runs reliably for 30 days with no safety breach and measurable net benefit over the baseline strategy.

---

## Phase 5 — Advanced Optimisation

**Outcome:** Improve forecasting, profitability and portfolio readiness.

- [ ] Solar forecast integration.
- [ ] Load forecasting from historical patterns.
- [ ] Multi-scenario price optimisation.
- [ ] Model predictive control.
- [ ] Battery degradation model refinement.
- [ ] Weather and seasonal features.
- [ ] Multiple-site support.
- [ ] Tariff and network constraint profiles.
- [ ] Portfolio and vendor onboarding workflow.
- [ ] Exportable performance and compliance reports.

---

## Standing gates

The project does not advance into automated control until:

- Sigenergy write commands are officially documented and authorised.
- VPP and warranty implications are understood.
- Operator override and emergency stop are tested.
- All command paths are server-side.
- Audit coverage is complete.
- The safety policy engine can veto every command.
- Simulation demonstrates a positive net result after losses and degradation.
