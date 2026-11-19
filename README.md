# otel-config-go (formerly otel-launcher-go)

> **Deprecated:** `github.com/hyperdxio/otel-config-go` is no longer
> maintained. Use the upstream
> [OpenTelemetry Go SDK](https://opentelemetry.io/docs/languages/go/) instead:
> it is more up to date and sends to ClickStack over OTLP. Set
> `OTEL_EXPORTER_OTLP_ENDPOINT` to your ClickStack endpoint and
> `OTEL_EXPORTER_OTLP_HEADERS=authorization=<ingestion key>` in place of
> `HYPERDX_API_KEY`. What `otelconfig.ConfigureOpenTelemetry` sets up maps to:
>
> - Traces: [`go.opentelemetry.io/otel/sdk/trace`](https://pkg.go.dev/go.opentelemetry.io/otel/sdk/trace)
>   with [`otlptracehttp`](https://pkg.go.dev/go.opentelemetry.io/otel/exporters/otlp/otlptrace/otlptracehttp)
>   or [`otlptracegrpc`](https://pkg.go.dev/go.opentelemetry.io/otel/exporters/otlp/otlptrace/otlptracegrpc)
> - Metrics: [`go.opentelemetry.io/otel/sdk/metric`](https://pkg.go.dev/go.opentelemetry.io/otel/sdk/metric)
>   with [`otlpmetrichttp`](https://pkg.go.dev/go.opentelemetry.io/otel/exporters/otlp/otlpmetric/otlpmetrichttp)
>   or [`otlpmetricgrpc`](https://pkg.go.dev/go.opentelemetry.io/otel/exporters/otlp/otlpmetric/otlpmetricgrpc)
> - Host and runtime metrics: [`go.opentelemetry.io/contrib/instrumentation/host`](https://pkg.go.dev/go.opentelemetry.io/contrib/instrumentation/host)
>   and [`go.opentelemetry.io/contrib/instrumentation/runtime`](https://pkg.go.dev/go.opentelemetry.io/contrib/instrumentation/runtime)
> - Exporter and propagator selection from `OTEL_*` variables:
>   [`go.opentelemetry.io/contrib/exporters/autoexport`](https://pkg.go.dev/go.opentelemetry.io/contrib/exporters/autoexport)
>   and [`go.opentelemetry.io/contrib/propagators/autoprop`](https://pkg.go.dev/go.opentelemetry.io/contrib/propagators/autoprop)

This project is a configuration layer that chooses default values for configuration options that many OpenTelemetry users would ultimately configure manually, allowing for minimal code to quickly instrument with OpenTelemetry.

Latest release built with:

- OpenTelemetry Go [v1.18.0/v0.41.0](https://github.com/open-telemetry/opentelemetry-go/releases/tag/v1.18.0)
- OpenTelemetry Semantic Conventions [v1.21.0](https://github.com/open-telemetry/opentelemetry-go/tree/main/semconv/v1.21.0)

(Note: semantic conventions to be updated to match upstream in next release)

## Getting started

```bash
go get github.com/hyperdxio/otel-config-go
```

## Configure

Minimal setup - by default will send all telemetry via GRPC to `localhost:4317`

```go
import "github.com/hyperdxio/otel-config-go/otelconfig"

func main() {
    otelShutdown, err := otelconfig.ConfigureOpenTelemetry()
    defer otelShutdown()
}
```

You can set headers directly instead.

```go
import "github.com/hyperdxio/otel-config-go/otelconfig"

func main() {
    otelShutdown, err := otelconfig.ConfigureOpenTelemetry(
        otelconfig.WithServiceName("service-name"),
        otelconfig.WithHeaders(map[string]string{
            "service-auth-key": "value",
            "service-useful-field": "testing",
        }),
    )
    defer otelShutdown()
}
```

### Migrating from otel-launcher-go to otel-config-go

As of v1.8.0, this package has been renamed from `otel-launcher-go` to `otel-config-go`. When migrating to use the renamed package, all references to `launcher` should be changed to `otelconfig`.

## Configuration Options

| Config Option               | Env Variable                        | Required | Default              |
| --------------------------- | ----------------------------------- | -------- | -------------------- |
| WithServiceName             | OTEL_SERVICE_NAME                   | y        | -                    |
| WithServiceVersion          | OTEL_SERVICE_VERSION                | n        | -                    |
| WithHeaders                 | OTEL_EXPORTER_OTLP_HEADERS          | n        | {}                   |
| WithExporterProtocol        | OTEL_EXPORTER_OTLP_PROTOCOL         | n        | grpc                 |
| WithTracesExporterEndpoint  | OTEL_EXPORTER_OTLP_TRACES_ENDPOINT  | n        | localhost:4317       |
| WithTracesExporterInsecure  | OTEL_EXPORTER_OTLP_TRACES_INSECURE  | n        | false                |
| WithMetricsExporterEndpoint | OTEL_EXPORTER_OTLP_METRICS_ENDPOINT | n        | localhost:4317       |
| WithMetricsExporterInsecure | OTEL_EXPORTER_OTLP_METRICS_INSECURE | n        | false                |
| WithLogLevel                | OTEL_LOG_LEVEL                      | n        | info                 |
| WithPropagators             | OTEL_PROPAGATORS                    | n        | tracecontext,baggage |
| WithResourceAttributes      | OTEL_RESOURCE_ATTRIBUTES            | n        | -                    |
| WithMetricsReportingPeriod  | OTEL_EXPORTER_OTLP_METRICS_PERIOD   | n        | 30s                  |
| WithMetricsEnabled          | OTEL_METRICS_ENABLED                | n        | true                 |
| WithTracesEnabled           | OTEL_TRACES_ENABLED                 | n        | true                 |

------

This is a joint effort alongside LightStep and is based their initial [otel-launcher-go](https://github.com/lightstep/otel-launcher-go). The intention is to contribute this to OpenTelemetry Go Contrib.
