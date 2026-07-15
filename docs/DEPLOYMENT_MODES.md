# Deployment Modes

## Decision

The VPP Optimiser is hosted by default, self-hostable by design and installable on phones as a Progressive Web App.

The phone is a client interface. It is not the always-on ingestion, MQTT or optimisation server.

## Managed hosted edition

The managed edition provides:

- central authentication;
- multi-organisation and multi-site tenancy;
- per-site Amber credential storage;
- Sigenergy onboarding when approved;
- scheduled ingestion;
- future MQTT ingestion workers;
- automatic updates;
- remote mobile access;
- central backup and monitoring.

This is the default for family, friends and non-technical users.

## Self-hosted edition

The self-hosted edition is a future packaging target using the same source repository and authority model.

Expected services:

```text
vpp-optimiser/
├── web application
├── backend API
├── optimiser worker
├── scheduler
├── Sigenergy MQTT worker
├── PostgreSQL database
└── encrypted secret store
```

Target hosts include:

- Linux mini PC;
- NAS or home server;
- EchoForge infrastructure;
- Windows or Linux workstation using containers.

The deployment should eventually be distributed through Docker Compose with environment templates, health checks, backup guidance and upgrade procedures.

## Phone and tablet experience

The web application becomes an installable PWA with:

- home-screen icon;
- standalone display;
- responsive navigation;
- secure authenticated access;
- update notification;
- connection-loss messaging.

The PWA must not cache:

- credentials;
- authenticated API responses;
- audit payloads;
- live price data as if it remains current;
- battery telemetry as if it remains current.

Only versioned static assets required for the application shell may be cached. Authentication and API routes use network-only behaviour.

## Why the phone is not the backend

Mobile operating systems suspend background applications and are unsuitable for dependable permanent MQTT connections, scheduled ingestion and battery-control safety logic.

The phone may:

- display prices, telemetry and earnings;
- change strategy settings;
- approve future supervised actions;
- receive notifications;
- show alarms and audit history.

The always-on backend must:

- poll Amber;
- receive or subscribe to Sigenergy data;
- maintain provider tokens and certificates;
- run optimisation schedules;
- enforce safety policies;
- record audit events;
- remain available when the phone is asleep, offline or restarted.

## Network access for self-hosted deployments

Supported access patterns should include:

- local Wi-Fi;
- Tailscale or equivalent private mesh;
- a secured reverse proxy and domain;
- optional managed relay service.

Direct exposure of database, MQTT or secret-store ports to the public internet is prohibited.

## Deployment invariants

All editions preserve these rules:

- tenant isolation;
- server-side credentials;
- site-scoped provider calls;
- Observer-first progression;
- no browser-held command authority;
- deterministic safety veto before any future command;
- complete audit coverage.