# Tenancy and Secret Authority

## Purpose

The VPP Optimiser is a multi-household, multi-site application. Dane, Dane's father and future users must be able to operate independent Amber and Sigenergy systems without any cross-tenant data or credential exposure.

## Authority model

```text
Organisation / household
├── Organisation memberships
├── Energy sites
│   ├── Optional site memberships
│   ├── Amber connection
│   ├── Sigenergy connection
│   ├── Strategy profile
│   ├── Price and usage data
│   ├── Telemetry and alarms
│   └── Audit history
└── Organisation settings
```

An organisation is the primary tenant boundary. Every energy site belongs to exactly one organisation.

## Roles

Organisation roles:

- `owner` — manages members, sites, integration credentials and organisation settings.
- `admin` — manages sites and integrations but cannot transfer or delete the organisation.
- `operator` — views operational data and may run permitted Observer/Adviser actions; cannot read or manage credentials.
- `viewer` — read-only access to approved site data.

Platform administration is separate from tenant roles. An organisation owner has no authority over another organisation.

## Site selection

Every dashboard and data query is scoped to a selected site that the signed-in user can access.

The application must:

- reject caller-supplied site IDs outside the user's membership;
- clear a selected site when membership is removed;
- return a safe no-site state rather than falling back to another tenant's data;
- persist only site and organisation identifiers on the client.

## Integration credentials

Amber and Sigenergy credentials are site-specific or application-authorisation-specific secrets.

Plaintext secrets must never be stored in ordinary public tables. The database may retain only metadata such as:

- provider;
- organisation ID;
- site ID;
- secret-store reference;
- masked hint;
- secret version;
- validation status;
- created, rotated and revoked timestamps;
- last validation time and error category.

Secret values include:

- Amber API tokens;
- Sigenergy AppSecret values;
- Sigenergy access tokens;
- MQTT private keys and certificates;
- webhook or scheduler shared secrets.

## Secret operations

All secret operations are server-side:

1. The user submits a credential through an authenticated form.
2. The backend verifies organisation and site authority.
3. The backend validates the credential with the provider.
4. The backend writes or rotates the credential in Supabase Vault or an equivalent server-only secret store.
5. The browser receives only status, provider metadata and a masked hint.

Client-callable functions must never return plaintext credentials.

Backend secret RPCs must:

- be executable by `service_role` only;
- revoke execution from `PUBLIC`, `anon` and `authenticated`;
- use `SECURITY DEFINER` only where necessary;
- set a fixed `search_path`;
- schema-qualify referenced objects;
- record metadata-only audit events.

## Amber authority

Each energy site uses its own Amber token.

The production flow is:

```text
Select site
→ submit token to server
→ validate with Amber GET /v1/sites
→ choose matching Amber site
→ revalidate ownership of selected Amber site
→ store token in secret store
→ map Amber site ID and NMI
→ begin per-site ingestion
```

The token must not be written to local storage, session storage, URLs, logs, audit payloads or toast messages.

A global `AMBER_API_KEY` may exist only as a temporary development migration fallback. Production must fail closed when a site has no active credential.

## Sigenergy authority

The expected managed model is one approved developer application with multiple customer systems authorised under it. Sigenergy must confirm whether unrelated households can onboard into the same application.

Until that contract is confirmed:

- Sigenergy remains read-only and simulated in the application;
- no payload schema is invented;
- no command endpoint is implemented;
- AppKey, AppSecret, access tokens and MQTT certificates remain server-side.

## Scheduler isolation

Scheduled ingestion iterates active sites independently.

One site's invalid credential, provider outage or rate limit must not prevent another site's ingestion. Health and failure state are recorded per site and aggregated for operator visibility.

## Audit rules

Audit events record:

- actor;
- organisation and site;
- provider;
- operation type;
- status;
- reason/error category;
- correlation ID;
- timestamps.

Audit events never record secret values. Raw payload visibility is restricted to authorised organisation/site owners and sanitised where provider data may contain identifiers.