# Observability

Ahara product telemetry uses the shared OTEL pipeline and shared Grafana stack.
Do not create product-specific CloudWatch dashboards as the primary operational
surface.

This standard implements
[ADR 021](../adr/021-shared-otel-and-grafana-observability.md).

## Telemetry Path

- Instrument services with Ahara OTEL wrappers.
- Send logs, traces, and metrics to the OTLP endpoint published by
  `ahara-infra`.
- Treat CloudWatch Logs as a Lambda/runtime fallback surface, not the product
  dashboard source.

## Grafana Dashboards

- Product/domain dashboard source lives in the repo that owns the data and query
  semantics.
- Put dashboard JSON under `observability/dashboards/*.json` unless the repo has
  a stronger local convention.
- Each dashboard JSON must declare a stable Grafana `uid` and `title`.
- Declare the deployment in `platform.yml`:

```yaml
observability:
  dashboards:
    path: observability/dashboards
    folder_uid: my-product
    folder_title: My Product
    prune: true
```

- The product deployer role must include the `grafana-dashboard-deploy` policy
  module in `ahara-infra`.
- Shared CI deploys dashboards through the `ahara-grafana-dashboard-deploy`
  Lambda. Do not copy product dashboards into `ahara-observability` just to
  deploy them, and do not redeploy Grafana for dashboard-only domain changes.
- The Grafana service-account token belongs in SSM at
  `/ahara/observability/grafana-dashboard-deployer-token`; product repos must
  invoke the deploy Lambda instead of reading the token directly.
