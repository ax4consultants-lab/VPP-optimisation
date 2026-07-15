# Lovable Build Brief — Observer MVP

## Mission

Build the first production-shaped interface for a battery earnings optimiser connecting Amber Electric market data with Sigenergy SigenStor telemetry.

The first build is strictly **read-only Observer Mode**. It must look and behave like the future control application, but must not issue battery commands.

## Product identity

Working name: **VPP Optimiser**

Positioning: a clear, high-trust energy command centre that shows what the battery, property, market and VPP are doing—and what an optimiser would recommend.

Visual direction:

- Dark operational interface with strong readability.
- Clean Australian energy/technology aesthetic.
- Desktop-first but fully responsive.
- Status and risk must be communicated by label, icon and text—not colour alone.
- Avoid decorative clutter, oversized gradients and consumer-gadget styling.

## Required routes

### `/login`

- Email/password or magic-link authentication.
- Clear environment label: Demo, Development or Production.

### `/dashboard`

Show the present operating state:

- Solar generation kW.
- House load kW.
- Grid import/export kW.
- Battery charge/discharge kW.
- Battery SOC percentage.
- Current Amber import price.
- Current Amber feed-in price.
- Current optimiser recommendation.
- Expected value of the recommendation.
- Active alarms.
- Amber and Sigenergy connection health.
- Last telemetry and price timestamps.

Include a 24-hour timeline combining price, forecast and recommended action.

### `/market`

- Current and forecast import/feed-in prices.
- Price descriptor and spike status.
- Forecast confidence band.
- Renewable percentage.
- Actual versus forecast chart.
- Filters for channel and resolution.

### `/battery`

- SOC, power, capacity, mode and operating status.
- Solar, load and grid flow cards.
- Equipment/system details.
- Alarm history.
- Control-mode display, fixed to Observer in Phase 1.

### `/performance`

- Daily import cost.
- Daily export earnings.
- Net energy cost/revenue.
- Solar generation and self-consumption.
- Battery throughput.
- Baseline versus recommended-strategy result.
- Missed opportunity estimate.

### `/strategy`

- Current strategy profile.
- Editable simulation settings:
  - minimum reserve SOC;
  - maximum target SOC;
  - minimum profitable spread;
  - round-trip efficiency;
  - degradation cost per kWh throughput;
  - maximum charge/discharge power;
  - forecast confidence threshold.
- Save settings but do not issue commands.
- Render the next 24-hour recommended schedule.

### `/alerts`

- Active and historical Sigenergy alarms.
- Data-staleness warnings.
- Amber API failures.
- High-price opportunity alerts.
- Negative export-price warnings.

### `/audit`

- Timestamped event table.
- Event type, source, severity, correlation ID and summary.
- Expandable raw JSON for authorised users.

### `/settings`

- Site profile.
- Connection status.
- Timezone fixed initially to `Australia/Adelaide`.
- Strategy defaults.
- Notification preferences placeholders.
- No raw credentials displayed.

## Navigation

Left navigation on desktop, compact bottom or drawer navigation on mobile.

Primary items:

1. Dashboard
2. Market
3. Battery
4. Performance
5. Strategy
6. Alerts
7. Audit
8. Settings

Persistent header indicators:

- Control mode.
- System health.
- Data freshness.
- User menu.

## Data behaviour

Create a typed data-access layer. Components must not call external APIs directly.

Use seeded mock data first, shaped exactly like the internal contracts in `docs/API_CONTRACTS.md`.

Required UI states:

- loading;
- populated;
- empty;
- stale;
- partial integration failure;
- authentication failure;
- active critical alarm.

## Supabase requirements

- Use Supabase Auth.
- Apply RLS to every user-facing table.
- Use Edge Functions for Amber access and Sigenergy webhooks.
- Store external secrets only in server-side secret storage.
- Store raw external payloads separately from normalised operational records.

## Components to create

- `SystemHealthBadge`
- `ConnectionStatusCard`
- `EnergyFlowDiagram`
- `MetricCard`
- `PriceTimeline`
- `ForecastConfidenceBand`
- `RecommendationCard`
- `RecommendationTimeline`
- `AlarmBanner`
- `AlarmTable`
- `DataFreshnessIndicator`
- `StrategySettingsForm`
- `PerformanceSummary`
- `AuditEventTable`
- `ControlModeBadge`

## Core TypeScript enums

```ts
export type ControlMode =
  | 'observer'
  | 'adviser'
  | 'supervised'
  | 'guarded_auto';

export type RecommendedAction =
  | 'charge'
  | 'hold'
  | 'self_consume'
  | 'discharge'
  | 'export'
  | 'stop';

export type HealthState = 'healthy' | 'degraded' | 'offline' | 'blocked';
export type Severity = 'info' | 'warning' | 'critical';
```

## Acceptance criteria

- The application can run entirely on seeded data.
- Every route is functional and responsive.
- Dashboard timestamps clearly distinguish live, delayed and stale data.
- No external credential is present in client code.
- No command button exists in Observer Mode.
- Strategy changes affect simulation output only.
- Every recommendation includes plain-language reasoning.
- Critical alarms visually block the recommendation panel.
- Accessibility basics are met: keyboard navigation, labels, contrast and non-colour status cues.

## First Lovable instruction

Build the Observer MVP from this brief and the repository documentation. Start with the complete frontend shell, reusable components, Supabase schema/migrations and realistic seed data. Do not connect live APIs or implement battery commands in the first pass. Keep external integrations behind typed adapters so Amber and Sigenergy can be connected later without rebuilding the UI.