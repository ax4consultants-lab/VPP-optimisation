# System Architecture

## Authority boundaries

- **Amber Electric:** market, tariff and metered-usage source.
- **Sigenergy:** physical system telemetry, system state, alarms and command execution.
- **Optimisation service:** recommendation authority only.
- **Safety policy engine:** final veto authority for every command.
- **Operator:** explicit authority for supervised control and configuration changes.
- **Lovable frontend:** presentation and authorised intent capture; never direct device authority.

## Logical components

```text
[Lovable Web App]
       |
       v
[Authenticated Backend API]
       |
       +--> [Read Models / Postgres]
       +--> [Strategy Service]
       +--> [Optimisation Engine]
       +--> [Audit Service]
       +--> [Command Approval Service]
                    |
                    v
             [Safety Policy Engine]
                    |
                    v
               [Command Queue]
                    |
                    v
            [Sigenergy Adapter]

[Amber Scheduler] ----> [Amber Adapter] ----> [Normaliser] ----> [Postgres]

[Sigenergy Telemetry Webhook] --+
[Sigenergy System Webhook] ------+--> [Verify + Deduplicate + Raw Store + Normalise]
[Sigenergy Alarm Webhook] -------+
```

## Recommended MVP deployment

Use Supabase for the first implementation:

- Postgres for operational and historical data.
- Auth for user identity.
- RLS for site-level access.
- Edge Functions for external API calls and webhook receivers.
- Scheduled functions for Amber polling and optimisation runs.
- Realtime only where it improves the live dashboard.

The optimiser may begin as deterministic TypeScript. Introduce a separate Python service only when advanced forecasting or mathematical optimisation requires it.

## Data flow

### Amber

1. Scheduled server function requests sites and current price intervals.
2. Adapter validates the external response.
3. Raw payload is stored with request metadata.
4. Data is normalised by site, channel and interval.
5. Read models are refreshed.
6. An audit event records success or failure.

Historical usage and prices must be fetched in provider-supported windows and retried idempotently.

### Sigenergy telemetry

1. Sigenergy posts to a dedicated telemetry URL.
2. Receiver authenticates/verifies the request.
3. Receiver calculates an idempotency key.
4. Raw payload is stored.
5. Supported fields are normalised.
6. Current site state is updated transactionally.
7. Dashboard subscribers receive the new state.

### Sigenergy alarms

1. Alarm event is verified and stored.
2. Alarm severity and blocking status are mapped.
3. Active alarm state is updated.
4. Critical/blocking alarms invalidate pending recommendations and future commands.
5. Recovery events clear only the matching active alarm.

### Recommendation

1. Build a decision snapshot from fresh telemetry, price forecasts, strategy settings and active constraints.
2. Run deterministic optimisation.
3. Run safety pre-checks even in simulation mode.
4. Store recommendation, reasons, assumptions and optimiser version.
5. Present it to the operator.

### Future command

1. Recommendation is selected for execution.
2. Approval requirements are evaluated.
3. Safety policy performs a final check against current data—not the earlier snapshot alone.
4. Idempotent command is queued.
5. Sigenergy adapter issues the command.
6. Acknowledgement and resulting telemetry are reconciled.
7. Timeout triggers stop/restore-normal workflow.

## Failure behaviour

- Amber unavailable: retain Sigenergy monitoring; mark market data stale and block price-driven commands.
- Sigenergy telemetry unavailable: retain market view; block all commands.
- Alarm webhook unavailable: block automated control until health is restored.
- Optimiser failure: preserve normal battery/VPP operation and issue no command.
- Database failure: issue no command.
- Frontend unavailable: backend fail-safe rules and bounded-command expiry remain effective.

## Environments

- **Demo:** seeded data, no external calls.
- **Development:** sandbox/test credentials where available; commands disabled.
- **Staging:** live read data permitted; command dry-run only.
- **Production:** live data; control enabled only through explicit feature and policy gates.

Every screen and audit event must identify its environment.
