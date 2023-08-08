---
canonical: https://grafana.com/docs/agent/latest/flow/reference/components/otelcol.exporter.loki/
title: otelcol.exporter.loki
---

# otelcol.exporter.loki

`otelcol.exporter.loki` accepts OTLP-formatted logs from other `otelcol`
components, converts them to Loki-formatted log entries, and forwards them
to `loki` components.

> **NOTE**: `otelcol.exporter.loki` is a custom component unrelated to the
> `lokiexporter` from the OpenTelemetry Collector.
>
> Conversion of logs are done according to the OpenTelemetry
> [Logs Data Model][] specification.

Multiple `otelcol.exporter.loki` components can be specified by giving them
different labels.

[Logs Data Model]: https://opentelemetry.io/docs/reference/specification/logs/data-model/

## Usage

```river
otelcol.exporter.loki "LABEL" {
  forward_to = [...]
}
```

## Arguments

`otelcol.exporter.loki` supports the following arguments:

Name | Type | Description | Default | Required
---- | ---- | ----------- | ------- | --------
`forward_to` | `list(receiver)` | Where to forward converted Loki logs. | | yes


## Exported fields

The following fields are exported and can be referenced by other components:

Name | Type | Description
---- | ---- | -----------
`input` | `otelcol.Consumer` | A value that other components can use to send telemetry data to.

`input` accepts `otelcol.Consumer` data for logs. Other telemetry signals are ignored.

Logs sent to `input` are converted to Loki-compatible log entries and are
forwarded to the `forward_to` argument in sequence.


## Component health

`otelcol.exporter.loki` is only reported as unhealthy if given an invalid
configuration.

## Debug information

`otelcol.exporter.loki` does not expose any component-specific debug
information.

## Example

This example accepts OTLP logs over gRPC, transforms them and forwards
the converted log entries to `loki.write`:

```river
otelcol.receiver.otlp "default" {
  grpc {}

  output {
    logs = [otelcol.exporter.loki.default.input]
  }
}

otelcol.exporter.loki "default" {
  forward_to = [loki.write.local.receiver]
}

loki.write "local" {
    endpoint {
        url = "loki:3100"
    }
}
```
<!-- START GENERATED COMPATIBLE COMPONENTS -->

## Compatible components

`otelcol.exporter.loki` can output data to the following components:

- Components that accept Loki Logs:
  - [`loki.echo`]({{< relref "../components/loki.echo.md" >}})
  - [`loki.process`]({{< relref "../components/loki.process.md" >}})
  - [`loki.relabel`]({{< relref "../components/loki.relabel.md" >}})
  - [`loki.write`]({{< relref "../components/loki.write.md" >}})

Note that connecting some components may not be feasible or components may require further configuration to make the connection work correctly. Please refer to the linked documentation for more details.

<!-- END GENERATED COMPATIBLE COMPONENTS -->
