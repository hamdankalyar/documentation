---
title: OpenTelemetry Runtime Metrics
kind: documentation
type: multi-code-lang
---

## Overview

Runtime metrics are application metrics about memory usage, garbage collection, or parallelization. Datadog tracing libraries provide [runtime metrics collection][5] for each supported language, but in addition, OpenTelemetry (OTel) collects runtime metrics, which can be sent to Datadog through the OpenTelemetry SDKs.

Datadog collects OpenTelemetry runtime metrics in the following languages:
- Java
- .NET
- Go

## Metric naming conventions

Runtime metrics follow different naming conventions depending on their source: OpenTelemetry Collector Datadog Exporter, Datadog Agent OTLP Ingestion, or Datadog tracing libraries. When using OpenTelemetry runtime metrics with Datadog, you receive both the original OpenTelemetry runtime metrics as well as mapped Datadog runtime metrics for equivalent metrics. Runtime metrics have the following prefixes which indicate their source:

| OTel Collector Datadog Exporter | Datadog Agent OTLP Ingest |  Datadog tracing library |
| --- | --- | --- |
| `otel.process.runtime.*` | `process.runtime.*` | `runtime.<LANG>.*` |

**Note**: OpenTelemetry runtime metrics are mapped to Datadog by metric name. Don't do mapping renaming of host metrics for OpenTelemetry runtime metrics or it will break.

## Setup

Select your language to see instructions for setting up and configuring the OpenTelemetry SDK to send runtime metrics:

{{< partial name="opentelemetry/otel-runtime-metrics.html" >}}
<br/>

## View runtime metric dashboards

After setup is complete, see your runtime metrics on the [APM Service Page][6] (see Java example below), the flame graph metrics tab, and in [default runtime dashboards][7].

{{< img src="opentelemetry/otel_runtime_metrics_service_page.png" alt="Service page showing OpenTelemetry runtime metrics on the JVM Metrics tab" style="width:100%;" >}}

[5]: /tracing/metrics/runtime_metrics/
[6]: https://app.datadoghq.com/apm/services
[7]: https://app.datadoghq.com/dash/integration/256/jvm-metrics
