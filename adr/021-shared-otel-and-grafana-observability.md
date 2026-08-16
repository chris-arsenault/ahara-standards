# ADR 021: Shared OTEL and Grafana observability

## Status

Accepted (2026-08-16)

## Context

Product telemetry needs one operational path without making the shared
observability repository own every product's query semantics. CloudWatch Logs
remain useful for Lambda runtime failures, but product-specific CloudWatch
dashboards split operations across consoles and duplicate the Grafana stack.

Dashboard deployment also needs to follow application releases. Copying JSON
into the observability runtime repository separates a dashboard from the code
and data contract it explains.

## Decision

Ahara services send logs, metrics, and traces through the shared OTEL ingest
path. Grafana is the primary operational surface; CloudWatch Logs remain a
fallback for Lambda runtime diagnosis.

Each product repository owns its domain dashboard JSON and declares the
dashboard directory and Grafana folder in `platform.yml`. Shared CI invokes the
platform dashboard-deploy Lambda. `ahara-observability` owns the Grafana
runtime, datasources, and cross-platform dashboards, not product dashboards.

The product deployer invokes the dashboard-deploy Lambda and cannot read the
Grafana service-account token from SSM.

## Alternatives

- Product-specific CloudWatch dashboards would duplicate the shared Grafana
  surface and leave non-AWS workloads elsewhere.
- Keeping all dashboards in `ahara-observability` would separate domain queries
  from their owning services and release lifecycle.
- Giving product deployers the Grafana token would broaden secret access beyond
  what dashboard deployment requires.

## Consequences

- Operators use Grafana for product telemetry across AWS and household hosts.
- Dashboard changes ship with the product that owns their data contract.
- The observability runtime can change independently of product dashboards.
- Lambda runtime failures may still require CloudWatch Logs when OTEL export is
  unavailable.
