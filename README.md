# VPP Optimisation

A secure, multi-household battery earnings optimisation platform for Amber Electric and Sigenergy SigenStor systems.

The application combines:

- **Amber Electric API** — live market prices, feed-in prices, forecasts, tariff context and historical usage.
- **Sigenergy developer platform** — future live system telemetry, system/change data, alarms and authorised commands.
- **Optimisation engine** — evaluates prices, forecast confidence, battery state, household load, solar availability, reserve constraints, efficiency and degradation cost.
- **Lovable application** — mobile-first operator dashboard, strategy configuration, performance analysis, integration setup and audit visibility.

## Current status

**Stage:** Live Amber Observer hardening and multi-tenant foundation.

Completed or substantially implemented in Lovable:

- Observer dashboard and protected routes;
- Supabase schema and site-scoped RLS;
- live Amber price and usage ingestion;
- deterministic simulation and recommendations;
- scheduler health and independent freshness states;
- audit visibility controls;
- explicit `SIMULATED` Sigenergy labelling;
- no battery command surface.

Current priority:

1. Put the complete Lovable application under GitHub authority.
2. Replace the temporary global Amber credential with per-site encrypted credentials.
3. Add organisation/household tenancy and site switching.
4. Make the application installable as a PWA.
5. Capture Sigenergy authentication, MQTT and payload contracts when developer approval is completed.

## Operating principle

```text
Observe → Analyse → Simulate → Validate → Supervised control → Guarded automation
```

The browser and phone are never the control authority. Credentials, ingestion, optimisation and any future command execution remain server-side.

## Product model

```text
Organisation / household
├── Members
├── Energy sites
│   ├── Amber credential and site mapping
│   ├── Sigenergy system authorisation
│   ├── Strategy profile
│   ├── Price, usage and telemetry data
│   └── Audit history
└── Organisation settings
```

Every organisation and site is isolated from every other organisation and site. Tenant owners cannot act across tenant boundaries.

## Target architecture

```text
Amber API ─────────────┐
                       ├─> Hosted or self-hosted backend ─> Optimisation engine
Sigenergy MQTT/API ────┘                 │                         │
                                         │                         v
Phone/Desktop PWA <─ Authenticated API <─┴─ Audit + policy engine ─> Command queue
                                                                      │
                                                                      v
                                                            Sigenergy OpenAPI
```

Sigenergy commands remain disabled until officially documented, authorised and validated.

## Deployment decision

The product is:

- **hosted by default** for simple onboarding and reliable background operation;
- **self-hostable by design** for privacy-focused and local deployments;
- **installable on phones as a PWA**;
- never dependent on a phone remaining awake as the ingestion or MQTT server.

See `docs/DEPLOYMENT_MODES.md`.

## First-release capabilities

- Secure authentication.
- Organisation and site tenancy.
- Per-site Amber credential setup and rotation.
- Amber price and historical usage ingestion.
- Site switching and site-scoped dashboards.
- Live market and simulated battery status until Sigenergy is connected.
- Price and renewable forecast timeline.
- Historical import cost and export earnings.
- Read-only optimisation recommendations.
- Strategy simulator and missed-opportunity analysis.
- Full audit log for inbound data, recommendations and operator actions.
- Explicit control-mode state: `observer`, `adviser`, `supervised`, `guarded_auto`.
- Installable mobile PWA.

## Non-goals for the first release

- Unattended battery commands.
- Reverse engineering private mobile-app endpoints.
- Direct browser access to Amber or Sigenergy credentials.
- Hosting the permanent optimiser or MQTT connection on a phone.
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
    ├── TENANCY_AND_SECRET_AUTHORITY.md
    ├── DEPLOYMENT_MODES.md
    └── DECISIONS.md
```

## Integration authority

### Amber Electric

Each production energy site has its own Amber token stored in a server-only secret store. The browser receives only masked metadata and connection status.

Required data:

- sites and channels;
- current import and feed-in prices;
- previous actual intervals;
- forecast intervals and confidence bands;
- historical usage and cost.

### Sigenergy

The developer dashboard provides an AppKey/AppSecret, authenticated systems, API permissions and data-subscription configuration. MQTT is the preferred future ingestion transport.

Required integration areas:

- application authentication and token lifecycle;
- system onboarding/authorisation;
- MQTT broker, topics and certificates;
- telemetry subscription;
- system/change-data subscription;
- alarm subscription;
- command issuance and acknowledgement after the Observer/Adviser gates are passed.

Do not invent Sigenergy payloads or command mappings while official contracts remain outstanding.

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

## Technical stack

- **Frontend:** Lovable, React, TypeScript, Tailwind, shadcn/ui.
- **Backend:** Supabase Postgres, Auth and scheduled/server functions for the MVP.
- **Secrets:** Supabase Vault or equivalent server-only store with service-only access.
- **Charts:** Recharts.
- **Validation:** Zod.
- **Optimiser:** deterministic TypeScript initially; a separate service may be introduced for advanced forecasting.
- **Future Sigenergy ingestion:** always-on MQTT worker.

## Build order

1. Complete Amber authority and scheduler hardening.
2. Mirror the complete Lovable project into GitHub.
3. Add organisation tenancy and site switching.
4. Add per-site Amber credential setup, rotation and revocation.
5. Make the application an installable PWA.
6. Capture Sigenergy authentication, MQTT and payload contracts.
7. Add live Sigenergy Observer ingestion.
8. Validate recommendations against actual outcomes.
9. Gate supervised commands behind explicit approval and hard safety policies.
10. Package a self-hosted Docker edition.

## Environment and secrets

Never commit live provider credentials, access tokens, service-role keys, MQTT certificates or scheduler secrets.

## Documentation priority

Lovable should read these files before implementation:

1. `LOVABLE_BUILD_BRIEF.md`
2. `docs/TENANCY_AND_SECRET_AUTHORITY.md`
3. `docs/DEPLOYMENT_MODES.md`
4. `docs/PRODUCT_REQUIREMENTS.md`
5. `docs/ARCHITECTURE.md`
6. `docs/DATA_MODEL.md`
7. `docs/SECURITY_AND_SAFETY.md`
8. `docs/API_CONTRACTS.md`

## Licence and use

Private operational project unless explicitly relicensed. Battery control must remain compliant with applicable agreements, manufacturer instructions, electrical requirements and network/VPP constraints.