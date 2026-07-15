# Delivery Roadmap

## Current state

```text
Milestone 001   Observer shell                         COMPLETE
Milestone 002   Live Amber Observer                    COMPLETE
Milestone 002H  Amber and database authority hardening MOSTLY COMPLETE
Milestone 003   Multi-tenant credentials and PWA       NEXT
Milestone 004   Live Sigenergy Observer                BLOCKED ON SIGENERGY CONTRACTS
Milestone 005   Recommendation validation              PENDING
Milestone 006   Supervised commands                    PENDING
Milestone 007   Guarded automation                     PENDING
```

Battery commands remain absent.

---

## Milestone 002 closeout — Source and runtime authority

**Outcome:** The current Amber Observer is recoverable, site-scoped and proven end to end.

- [x] Live Amber adapter.
- [x] Current-price polling and historical backfill.
- [x] Idempotent price and usage storage.
- [x] Scheduler health and independent freshness indicators.
- [x] Dedicated scheduler bearer secret.
- [x] Site-owner membership guard for caller-supplied site IDs.
- [x] RLS-scoped Amber site linking with affected-row verification.
- [ ] Store the matching `AMBER_SYNC_SECRET` in the Lovable runtime secret store.
- [ ] Complete a valid and invalid scheduler end-to-end test.
- [ ] Push the complete Lovable application and migrations into this repository.
- [ ] Verify a clean clone can typecheck and build.

**Exit criteria:** GitHub contains the full application, the scheduler succeeds against live Amber, and no caller can act across site boundaries.

---

## Milestone 003 — Multi-tenant sites, credentials and PWA

**Outcome:** Dane, Dane's father and future households can use isolated sites and provider credentials through one hosted application.

### Tenancy

- [ ] Add organisations/households.
- [ ] Add organisation memberships: owner, admin, operator, viewer.
- [ ] Assign every energy site to one organisation.
- [ ] Backfill the existing owner and site into a default organisation.
- [ ] Separate platform administration from tenant ownership.
- [ ] Add organisation and site switching.
- [ ] Scope every page and query to the selected authorised site.
- [ ] Add safe no-organisation, no-site and no-integration states.

### Per-site credentials

- [ ] Add non-secret integration credential metadata.
- [ ] Add service-only secret-store RPCs.
- [ ] Replace the production global Amber key with per-site credentials.
- [ ] Add Amber connect, validate, rotate, sync and disconnect flows.
- [ ] Prevent plaintext credentials from reaching client storage, logs or audit payloads.
- [ ] Update scheduled ingestion to resolve each site's own token.
- [ ] Isolate failures so one site cannot block another.
- [ ] Add per-site integration health and aggregate scheduler health.

### Mobile/PWA

- [ ] Add a web app manifest and installable icons.
- [ ] Add standalone mobile navigation.
- [ ] Add install guidance for supported browsers and iPhone/iPad.
- [ ] Cache only versioned static assets.
- [ ] Use network-only behaviour for auth, provider and API routes.
- [ ] Add an offline state that never presents stale data as live.

### Documentation

- [x] Define tenancy and secret authority.
- [x] Define hosted, self-hosted and mobile deployment modes.
- [ ] Add implementation report and migration notes.

**Exit criteria:** Three independent households can be provisioned without cross-tenant access, each site can use its own Amber token, and the app installs safely on a phone.

---

## Milestone 004 — Live Sigenergy Observer

**Outcome:** Real battery telemetry, system/change data and alarms replace simulated battery values without adding commands.

### Contract capture

- [ ] Confirm AppKey/AppSecret authentication and access-token lifecycle.
- [ ] Confirm Australian region API and MQTT endpoints.
- [ ] Confirm system onboarding/password authorisation flow.
- [ ] Capture API permissions and rate limits.
- [ ] Capture MQTT broker, port, TLS and client identity requirements.
- [ ] Capture telemetry, system/change and alarm topics.
- [ ] Capture payload schemas, timestamps, units and quality fields.
- [ ] Confirm QoS, retained-message and duplicate-message behaviour.
- [ ] Confirm subscription, renewal and unsubscribe operations.

### Ingestion worker

- [ ] Build an always-on Sigenergy MQTT worker.
- [ ] Store certificates and private keys server-side.
- [ ] Map authorised Sigenergy system IDs to energy sites.
- [ ] Validate and normalise telemetry.
- [ ] Validate and normalise system/change data.
- [ ] Validate and normalise alarms and recovery events.
- [ ] Add reconnect, backoff, health and audit handling.
- [ ] Mark Sigenergy as `LIVE` only after first verified data.

**Exit criteria:** Live Sigenergy data is visible per site, alarms block recommendations correctly, and control mode remains Observer.

---

## Milestone 005 — Adviser and validation

**Outcome:** The system recommends actions and proves its logic without controlling the battery.

- [ ] Deterministic charge, hold, self-consume, discharge and export recommendations.
- [ ] Forecast-confidence handling.
- [ ] Reserve, efficiency and degradation settings.
- [ ] Household-load and solar forecast inputs.
- [ ] Strategy timeline for the next 24 hours.
- [ ] Counterfactual earnings simulation.
- [ ] Compare recommendations with actual VPP behaviour.
- [ ] Daily performance summary.
- [ ] Recommendation reason codes and audit trail.
- [ ] Validate at least 14 days of recommendations against actual outcomes.

**Exit criteria:** There are no unresolved critical safety or data-quality defects and the strategy demonstrates a positive expected result after losses and degradation.

---

## Milestone 006 — Supervised commands

**Outcome:** An authorised operator can approve bounded, documented Sigenergy commands.

- [ ] Confirm official command catalogue and authorisation.
- [ ] Confirm VPP, warranty and installer implications.
- [ ] Implement command adapter and dry-run mode.
- [ ] Add owner/operator approval workflow.
- [ ] Add forced charge, forced discharge and restore-normal operations.
- [ ] Add acknowledgements and command status tracking.
- [ ] Add idempotency keys, timeout and automatic termination.
- [ ] Add manual emergency stop.
- [ ] Detect VPP override or command conflict.

**Exit criteria:** Commands are safely executed in a controlled test window, acknowledged, audited and automatically terminated within approved bounds.

---

## Milestone 007 — Guarded automation

**Outcome:** The optimiser can act autonomously inside a narrow approved policy envelope.

- [ ] Policy-based automatic approval.
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

## Milestone 008 — Self-hosted package and advanced optimisation

**Outcome:** Provide a supported local deployment and improve forecasting and portfolio capability.

- [ ] Docker Compose package.
- [ ] Local database, scheduler, optimiser and MQTT worker.
- [ ] Backup, restore, upgrade and health documentation.
- [ ] Private access through local network or mesh VPN.
- [ ] Solar and weather forecast integration.
- [ ] Load forecasting from historical patterns.
- [ ] Multi-scenario and model-predictive optimisation.
- [ ] Battery degradation model refinement.
- [ ] Portfolio and installer onboarding workflows.
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
- Tenant and site isolation tests pass.
- Simulation demonstrates a positive net result after losses and degradation.