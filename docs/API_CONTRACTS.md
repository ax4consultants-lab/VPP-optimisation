# Internal API Contracts

These contracts isolate the UI and optimiser from vendor-specific payloads. External adapters must validate and map provider data into these forms.

## Energy state

```ts
export interface EnergyState {
  siteId: string;
  observedAt: string;
  batterySocPercent: number | null;
  batteryPowerKw: number | null;
  solarPowerKw: number | null;
  loadPowerKw: number | null;
  gridPowerKw: number | null;
  operatingMode: string | null;
  systemStatus: 'healthy' | 'degraded' | 'offline' | 'blocked';
  freshnessSeconds: number;
}
```

Normalised sign convention:

- `batteryPowerKw > 0`: battery discharging.
- `batteryPowerKw < 0`: battery charging.
- `gridPowerKw > 0`: importing from grid.
- `gridPowerKw < 0`: exporting to grid.

## Market interval

```ts
export interface MarketInterval {
  siteId: string;
  channelType: 'general' | 'controlledLoad' | 'feedIn';
  intervalType: 'actual' | 'current' | 'forecast';
  startTime: string;
  endTime: string;
  durationMinutes: 5 | 15 | 30;
  retailCentsPerKwh: number;
  spotCentsPerKwh: number;
  descriptor: string;
  spikeStatus: 'none' | 'potential' | 'spike';
  renewablesPercent: number | null;
  forecast?: {
    low: number;
    predicted: number;
    high: number;
  } | null;
}
```

## Alarm

```ts
export interface SystemAlarm {
  id: string;
  siteId: string;
  externalAlarmId: string | null;
  code: string;
  severity: 'info' | 'warning' | 'critical';
  blocking: boolean;
  status: 'active' | 'recovered';
  raisedAt: string;
  recoveredAt: string | null;
  message: string;
}
```

## Recommendation

```ts
export interface Recommendation {
  id: string;
  siteId: string;
  generatedAt: string;
  action: 'charge' | 'hold' | 'self_consume' | 'discharge' | 'export' | 'stop';
  startTime: string;
  endTime: string;
  powerKw: number;
  targetSocPercent: number | null;
  expectedValueAud: number;
  confidence: number;
  reasonCodes: string[];
  explanation: string;
  safetyStatus: 'allowed' | 'blocked' | 'simulation_only';
  optimiserVersion: string;
}
```

## Dashboard response

```ts
export interface DashboardState {
  site: {
    id: string;
    name: string;
    controlMode: 'observer' | 'adviser' | 'supervised' | 'guarded_auto';
    timezone: string;
  };
  energy: EnergyState | null;
  currentPrices: MarketInterval[];
  recommendation: Recommendation | null;
  activeAlarms: SystemAlarm[];
  connections: Array<{
    provider: 'amber' | 'sigenergy';
    status: 'healthy' | 'degraded' | 'offline';
    lastSuccessAt: string | null;
    message: string | null;
  }>;
}
```

## Webhook endpoints

```text
POST /functions/v1/sigenergy-telemetry
POST /functions/v1/sigenergy-system-data
POST /functions/v1/sigenergy-alarm
```

Every receiver must:

1. verify provider authentication/signature;
2. reject oversized or malformed payloads;
3. derive an idempotency key;
4. store the raw event;
5. return quickly;
6. normalise asynchronously where practical;
7. avoid logging secrets.

Provider-specific authentication and response requirements must be implemented from the official Sigenergy documentation, not guessed.

## Amber server functions

```text
POST /functions/v1/amber-sync-sites
POST /functions/v1/amber-sync-current-prices
POST /functions/v1/amber-backfill-prices
POST /functions/v1/amber-backfill-usage
```

Browser clients call the application's authenticated API only. They must never receive the Amber bearer token.

## Command contract — disabled in Observer MVP

```ts
export interface ProposedCommand {
  siteId: string;
  recommendationId: string | null;
  commandType: 'force_charge' | 'force_discharge' | 'stop' | 'restore_normal';
  powerKw: number | null;
  targetSocPercent: number | null;
  expiresAt: string;
  idempotencyKey: string;
}
```

A command is not valid merely because it matches this schema. It requires control-mode permission, current safety-policy approval and a supported Sigenergy instruction mapping.
