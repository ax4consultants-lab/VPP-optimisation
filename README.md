# VPP Optimisation

A secure battery earnings optimisation platform for an Amber Electric retail/VPP arrangement and a Sigenergy SigenStor battery system.

The application combines:

- **Amber Electric API** — market prices, feed-in prices, forecasts, tariff context and historical usage.
- **Sigenergy OpenAPI** — live system telemetry, system data, alarms and authorised charge/discharge commands.
- **Optimisation engine** — evaluates price, forecast confidence, battery state, household load, solar availability, reserve constraints, efficiency and degradation cost.
- **Lovable application** — operator dashboard, strategy configuration, approvals, performance analysis and audit visibility.

## Current status

**Stage:** Repository foundation and first Lovable build preparation.

The first release is intentionally **Observer Mode**. It must ingest and display data before any autonomous battery command is permitted.

## Operating principle

```text
Observe → Analyse → Simulate → Validate → Supervised control → Guarded automation
```

The browser is never the control authority. Credentials and command execution remain server-side.

## Target architecture

```text
Amber API ──────────────┐
                       ├─> Ingestion + normalisation ─> Optimisation engine
Sigenergy webhooks ────┘                                  │
                                                          v
Lovable dashboard <─ Backend API <─ Audit + policy engine ─> Command queue
                                                               │
                                                               v
                                                       Sigenergy OpenAPI
```

## First-release capabilities

- Secure user authentication.
- Amber site discovery and price ingestion.
- Sigenergy telemetry, system-data and alarm webhook receivers.
- Live dashboard for solar, load, grid, battery and market state.
- Price and renewable forecast timeline.
- Historical import cost and export earnings.
- Read-only optimisation recommendations.
- Strategy simulator and missed-opportunity analysis.
- Full audit log for inbound data, recommendations and operator actions.
- Explicit control-mode state: `observer`, `adviser`, `supervised`, `guarded_auto`.

## Non-goals for the first release

- Unattended battery commands.
- Reverse engineering private mobile-app endpoints.
- Direct browser access to Amber or Sigenergy credentials.
- Bypassing VPP, warranty, installer or network constraints.
- AI-generated commands without deterministic safety validation.

## Repository map

```text
.
├── README.md
├── ROADMAP.md
├── LOVABLE_BUILD_BRIEF.md
├── .env.example
├── .gitignore
└── docs/
    ├── PRODUCT_REQUIREMENTS.md
    ├── ARCHITECTURE.md
    ├── DATA_MODEL.md
    ├── API_CONTRACTS.md
    ├── OPTIMISATION_ENGINE.md
    ├── SECURITY_AND_SAFETY.md
    └── DECISIONS.md
```

## Required integrations

### Amber Electric

Required data:

- Sites and channels.
- Current import and feed-in prices.
- Previous actual intervals.
- Forecast intervals and confidence bands.
- Historical usage and cost.

### Sigenergy

Required integration areas:

- Authentication and system boarding.
- Telemetry push endpoint.
- System-data push endpoint.
- Alarm push endpoint.
- Command issuance and command acknowledgement.

Sigenergy requires three separate receiving URLs for telemetry, system data and alarms. These are configured with Sigenergy support.

## Core safety rules

No command may be issued unless all of the following are true:

1. Control mode permits commands.
2. Sigenergy system state is healthy.
3. Telemetry is fresh.
4. No blocking alarm exists.
5. Grid state permits the action.
6. Battery SOC remains inside configured limits.
7. Charge/discharge power stays inside approved limits.
8. Expected profit exceeds the configured minimum margin.
9. Command duration is bounded.
10. Every decision and command is recorded.

## Suggested technical stack

- **Frontend:** Lovable, React, TypeScript, Tailwind, shadcn/ui.
- **Backend:** Supabase Postgres, Auth, Edge Functions and scheduled jobs for the MVP.
- **Charts:** Recharts.
- **Validation:** Zod.
- **Optimiser:** deterministic TypeScript service initially; Python service may be introduced later for advanced forecasting and optimisation.

## Build order

1. Create the frontend shell and navigation.
2. Implement database schema and row-level security.
3. Build mock Amber and Sigenergy adapters.
4. Build dashboard using seeded data.
5. Connect Amber read-only API.
6. Expose and register Sigenergy webhook endpoints.
7. Validate telemetry and alarm ingestion.
8. Run the optimiser in simulation only.
9. Compare recommended actions with actual VPP behaviour.
10. Gate command capability behind explicit approval and hard safety policies.

## Environment and secrets

Copy `.env.example` into the selected secret-management system. Never commit live API credentials.

## Documentation priority

Lovable should read these files before implementation:

1. `LOVABLE_BUILD_BRIEF.md`
2. `docs/PRODUCT_REQUIREMENTS.md`
3. `docs/ARCHITECTURE.md`
4. `docs/DATA_MODEL.md`
5. `docs/SECURITY_AND_SAFETY.md`
6. `docs/API_CONTRACTS.md`

## Licence and use

Private operational project unless explicitly relicensed. Battery control must remain compliant with applicable agreements, manufacturer instructions, electrical requirements and network/VPP constraints.