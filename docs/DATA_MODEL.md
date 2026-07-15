# Core Data Model

All timestamps are stored in UTC. User-facing times render in `Australia/Adelaide`.

## Identity and access

### `profiles`
- `id uuid` PK/FK auth user
- `display_name text`
- `role text` — owner/operator/viewer
- `created_at timestamptz`

### `site_memberships`
- `site_id uuid`
- `user_id uuid`
- `role text`
- unique `(site_id,user_id)`

## Sites and integrations

### `energy_sites`
- `id uuid` PK
- `name text`
- `timezone text`
- `control_mode text`
- `amber_site_id text`
- `nmi text`
- `sigenergy_system_id text`
- `status text`
- `created_at`, `updated_at`

### `integration_connections`
- `id uuid`
- `site_id uuid`
- `provider text` — amber/sigenergy
- `status text`
- `last_success_at`, `last_failure_at`
- `last_error_code text`
- `metadata jsonb`

Never store plaintext API secrets in these tables.

## External payloads

### `raw_external_events`
- `id uuid`
- `site_id uuid nullable`
- `provider text`
- `event_type text`
- `external_event_id text nullable`
- `idempotency_key text unique`
- `received_at timestamptz`
- `payload jsonb`
- `processing_status text`
- `processing_error text nullable`

## Market data

### `price_intervals`
- `id uuid`
- `site_id uuid`
- `channel_type text` — general/controlledLoad/feedIn
- `interval_type text` — actual/current/forecast
- `start_time`, `end_time`
- `duration_minutes int`
- `spot_cents_per_kwh numeric`
- `retail_cents_per_kwh numeric`
- `renewables_percent numeric`
- `descriptor text`
- `spike_status text`
- `forecast_low numeric nullable`
- `forecast_predicted numeric nullable`
- `forecast_high numeric nullable`
- unique `(site_id,channel_type,start_time,interval_type)`

### `usage_intervals`
- `id uuid`
- `site_id uuid`
- `channel_identifier text`
- `channel_type text`
- `start_time`, `end_time`
- `kwh numeric`
- `cost numeric`
- `quality text`
- unique `(site_id,channel_identifier,start_time)`

## Sigenergy state

### `system_snapshots`
- `id uuid`
- `site_id uuid`
- `observed_at timestamptz`
- `battery_soc_percent numeric`
- `battery_power_kw numeric`
- `solar_power_kw numeric`
- `load_power_kw numeric`
- `grid_power_kw numeric`
- `operating_mode text`
- `system_status text`
- `raw_event_id uuid`

Power sign conventions must be normalised and documented in code.

### `system_assets`
- `id uuid`
- `site_id uuid`
- `external_asset_id text`
- `asset_type text`
- `model text`
- `serial_number text`
- `firmware_version text`
- `rated_capacity_kwh numeric`
- `usable_capacity_kwh numeric`
- `max_charge_kw numeric`
- `max_discharge_kw numeric`
- `metadata jsonb`

### `alarms`
- `id uuid`
- `site_id uuid`
- `external_alarm_id text`
- `alarm_code text`
- `severity text`
- `blocking boolean`
- `status text` — active/recovered
- `raised_at`, `recovered_at`
- `message text`
- `raw_event_id uuid`

## Strategy and optimisation

### `strategy_profiles`
- `id uuid`
- `site_id uuid`
- `name text`
- `is_active boolean`
- `minimum_reserve_soc numeric`
- `maximum_target_soc numeric`
- `max_charge_kw numeric`
- `max_discharge_kw numeric`
- `round_trip_efficiency numeric`
- `degradation_cost_per_kwh numeric`
- `minimum_profit_margin numeric`
- `confidence_threshold numeric`
- `version int`

### `optimisation_runs`
- `id uuid`
- `site_id uuid`
- `run_at timestamptz`
- `engine_version text`
- `strategy_profile_id uuid`
- `input_snapshot jsonb`
- `status text`
- `error text nullable`

### `recommendations`
- `id uuid`
- `optimisation_run_id uuid`
- `site_id uuid`
- `action text`
- `start_time`, `end_time`
- `power_kw numeric`
- `target_soc numeric nullable`
- `expected_value numeric`
- `confidence numeric`
- `reason_codes text[]`
- `explanation text`
- `safety_status text`

## Commands — reserved for later phases

### `commands`
- `id uuid`
- `site_id uuid`
- `recommendation_id uuid nullable`
- `command_type text`
- `parameters jsonb`
- `status text`
- `idempotency_key text unique`
- `requested_by uuid nullable`
- `approved_by uuid nullable`
- `requested_at`, `approved_at`, `sent_at`, `acknowledged_at`, `completed_at`
- `provider_command_id text nullable`
- `failure_reason text nullable`

## Audit

### `audit_events`
- `id uuid`
- `site_id uuid nullable`
- `actor_type text` — user/system/provider
- `actor_id text nullable`
- `event_type text`
- `severity text`
- `correlation_id text`
- `summary text`
- `details jsonb`
- `created_at timestamptz`

Audit records are append-only to application roles.
