# Security and Safety

## Security principles

- Deny by default.
- Keep provider secrets server-side.
- Separate recommendation authority from command authority.
- Verify and deduplicate every webhook.
- Use least-privilege roles and site-scoped RLS.
- Record actions without recording credentials.
- Fail safe: loss of trusted data means no command.

## Secret handling

Amber tokens, Sigenergy credentials, signing keys and service-role keys must be stored in managed server-side secrets. They must never appear in:

- frontend environment variables;
- browser network responses;
- database rows accessible to users;
- audit details;
- exception messages;
- source control.

## Webhook controls

Each Sigenergy endpoint must implement the official authentication mechanism and:

- enforce HTTPS;
- validate timestamp/replay requirements where supported;
- limit request size;
- validate schema;
- generate an idempotency key;
- preserve raw payloads with restricted access;
- return provider-required status promptly;
- alert on repeated verification failures.

## Command safety gates

No command may be sent unless:

1. Environment permits command execution.
2. Site control mode permits the command.
3. User approval exists when required.
4. Telemetry age is below the configured threshold.
5. Market data age is below the configured threshold.
6. No blocking alarm is active.
7. System and grid status permit operation.
8. SOC, power, energy and duration remain within limits.
9. Expected net value exceeds the minimum margin.
10. Command maps to an officially supported Sigenergy instruction.
11. Idempotency and expiry values are present.
12. A stop/restore path exists.

## Hard limits

Configure per site and do not allow the optimiser to alter them:

- minimum reserve SOC;
- maximum target SOC;
- maximum charge kW;
- maximum discharge kW;
- maximum command duration;
- maximum daily battery throughput;
- maximum daily commanded cycles;
- minimum expected profit;
- maximum accepted forecast uncertainty;
- maximum allowable data age.

## Fail-safe behaviour

- Telemetry stale: block commands.
- Market data stale: block market-driven commands.
- Critical alarm: cancel pending commands and invoke approved stop/restore process.
- Command acknowledgement missing: mark uncertain, stop further commands and escalate.
- Backend restart: do not repeat commands without idempotent reconciliation.
- Optimiser exception: return to no-action recommendation.
- Database unavailable: issue no command.

## Control modes

### Observer

Read, display and audit only.

### Adviser

Generate recommendations; no command submission.

### Supervised

Operator approves each bounded command.

### Guarded auto

Policy may approve commands inside a fixed envelope. Manual stop remains available.

Mode changes are privileged, audited events and should require re-authentication for higher authority.

## Audit requirements

Record:

- integration successes and failures;
- raw-event receipt identifiers;
- normalisation outcomes;
- strategy changes;
- optimiser inputs, version and output;
- safety-policy decisions;
- approvals and rejections;
- command payload hash, lifecycle and acknowledgement;
- alarms, recovery and emergency stops.

Audit events are append-only for application users.

## Operational constraints to confirm

Before any live command testing, document:

- Amber/VPP dispatch authority and override behaviour;
- Sigenergy command precedence;
- installer and manufacturer warranty conditions;
- backup operation and outage behaviour;
- network export limits;
- device firmware and supported commands;
- emergency contact and rollback procedure.
