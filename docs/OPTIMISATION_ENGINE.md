# Optimisation Engine

## Objective

Maximise expected net financial value while preserving reserve, respecting equipment/VPP constraints and avoiding unprofitable battery cycling.

## Inputs

- Current battery SOC and available capacity.
- Maximum charge/discharge power.
- Current solar, load and grid flow.
- Amber import and feed-in interval prices.
- Price forecast low/predicted/high values.
- Forecast confidence.
- Expected household load.
- Expected solar generation.
- Minimum reserve and target SOC.
- Round-trip efficiency.
- Degradation cost per kWh throughput.
- Active alarms and system health.
- Control mode and VPP constraints.

## Actions

- `charge`
- `hold`
- `self_consume`
- `discharge`
- `export`
- `stop`

## First implementation

Use a deterministic horizon optimiser over the next 24 hours. Five-minute or 30-minute intervals may be used according to data availability.

For each candidate action, estimate:

```text
net value = market value
          - energy losses
          - degradation cost
          - risk penalty
          - constraint penalties
```

Never treat forecast value as guaranteed. Use confidence bands to reduce commitment where uncertainty is high.

## Minimum profitability test

A grid-charge/export cycle should not be recommended unless:

```text
expected export revenue
- charge energy cost
- efficiency loss cost
- degradation cost
- configured safety margin
> 0
```

## Reason codes

Recommendations must use machine-readable reason codes, including:

- `NEGATIVE_IMPORT_PRICE`
- `LOW_IMPORT_PRICE`
- `HIGH_FEED_IN_PRICE`
- `FORECAST_SPIKE_AHEAD`
- `PRESERVE_FOR_LATER_INTERVAL`
- `PROTECT_RESERVE`
- `INSUFFICIENT_PRICE_SPREAD`
- `FORECAST_CONFIDENCE_LOW`
- `TELEMETRY_STALE`
- `BLOCKING_ALARM`
- `SOC_LIMIT_REACHED`
- `POWER_LIMIT_REACHED`
- `VPP_CONSTRAINT`

## Safety separation

The optimiser proposes; it does not authorise.

A separate safety policy engine evaluates the proposed action using the latest state. It may reduce, reject or expire the proposal.

## Simulation

Every optimisation run stores:

- source intervals;
- telemetry snapshot;
- assumptions;
- strategy version;
- engine version;
- recommendation;
- expected result;
- safety outcome.

Historical replay must use only information that would have been available at the replay timestamp, preventing look-ahead bias.

## Initial baseline comparisons

- Actual Amber/VPP outcome.
- Self-consumption-only strategy.
- Simple threshold strategy.
- Forecast-aware optimiser.

## Later development

After the deterministic engine is validated, consider:

- mixed-integer linear programming;
- model predictive control;
- probabilistic price scenarios;
- learned solar/load forecasts;
- portfolio optimisation.

Machine learning may improve forecasts but must not replace deterministic command constraints.