# Product Requirements

## Problem

A battery participating through Amber Electric and Sigenergy can generate value through self-consumption, avoided imports and wholesale export events. Existing applications show parts of the system, but do not provide one auditable view of:

- market conditions;
- battery and property state;
- what the VPP actually did;
- what an alternative optimiser would have done;
- whether the result was profitable after losses and battery wear.

## Product objective

Create a secure system that initially observes and explains system performance, then progresses through simulation and supervised control toward guarded automated optimisation.

## Users

### Owner/operator

Needs a clear picture of current operation, earnings, missed opportunities, alarms and strategy.

### Technical administrator

Needs connection health, payload inspection, audit events, configuration and integration diagnostics.

### Future portfolio operator

Needs multi-site onboarding, policy templates, role separation and portfolio performance.

## Core user outcomes

1. Understand the present energy flow and market state in seconds.
2. See whether data is live, delayed or stale.
3. Understand why the optimiser recommends an action.
4. Compare actual VPP performance against a simulated alternative.
5. Maintain backup reserve and safety constraints.
6. Prove every decision and command through an immutable audit trail.

## Functional requirements

### FR-001 Authentication

Only authenticated users may access site data. Role support must allow at least `owner`, `operator` and `viewer`.

### FR-002 Site connections

The system must show Amber and Sigenergy connection status independently.

### FR-003 Market data

The system must ingest current, historical and forecast Amber price intervals by channel type.

### FR-004 Energy telemetry

The system must ingest Sigenergy telemetry and normalise solar, load, grid, battery power and SOC.

### FR-005 System state

The system must retain battery capacity, operating status and relevant device/system attributes.

### FR-006 Alarms

The system must ingest alarms and recovery events and identify whether an alarm blocks optimisation.

### FR-007 Observer dashboard

The system must display live state, price state, data freshness, active alarms and the current recommendation.

### FR-008 Strategy settings

Users with authority may configure simulation limits and assumptions. Phase 1 settings must not trigger commands.

### FR-009 Recommendations

Each recommendation must contain an action, proposed power, start/end time, expected value, confidence and reason codes.

### FR-010 Simulation

The system must calculate a baseline and counterfactual result using historical telemetry and prices.

### FR-011 Audit

All inbound payloads, normalisation results, recommendations, settings changes, approvals and commands must be auditable.

### FR-012 Control modes

The system must enforce exactly one mode per site: observer, adviser, supervised or guarded_auto.

### FR-013 Safety veto

A separate deterministic policy engine must be able to reject any command regardless of optimiser output.

### FR-014 Command lifecycle

Future command support must record proposed, approved, queued, sent, acknowledged, active, completed, failed, cancelled and timed-out states.

## Non-functional requirements

- Timezone: `Australia/Adelaide`, while storing timestamps in UTC.
- UI must clearly identify stale data.
- All external calls must be server-side.
- Webhooks must be authenticated and idempotent.
- Secrets must never be written to logs or audit payloads.
- The system must continue to show the last known state during partial outages.
- Safety decisions must be deterministic and testable.
- Optimisation logic must be versioned.
- Financial calculations must retain source interval references.

## Success measures

### MVP

- Amber and Sigenergy data visible in one dashboard.
- Data freshness and connection failures are obvious.
- Historical performance can be calculated.
- Recommendations are generated without commands.

### Adviser

- Recommendation logic can be replayed and explained.
- Counterfactual results are credible and reconciled to source data.

### Control

- All commands are bounded, acknowledged and reversible.
- No command is sent when telemetry is stale or a blocking alarm exists.

### Commercial

- Demonstrated net value above the existing baseline after efficiency and degradation assumptions.
