# Reference: Instrumentation Standards

This document outlines the mandatory standards for instrumentation in this repository.

## 1. Methodology
*   **Standard:** OpenTelemetry (OTel) MUST be used for all instrumentation.
*   **Propagation:** W3C Trace Context MUST be propagated across all service boundaries.

## 2. Naming Conventions
*   **Span Names:** MUST be named after the *business operation* (e.g., `checkout`, `update-inventory`).
    *   **Forbidden:** Do NOT name spans after transport details (e.g., `POST /api/checkout`, `grpc.Health/Check`).
*   **Metric Names:** MUST use the OpenTelemetry naming style (e.g., `foo.bar`).
    *   **Forbidden:** Do NOT use the Prometheus naming style (e.g., `<app>_foo_bar_total`).
    *   **Context:** Rely on attributes and the metric type to provide context.
*   **Instrumentation Scope:** Tracers and Meters MUST be named using the fully qualified library name (e.g., `github.com/org/repo/pkg/service`).

## 3. Semantic Conventions
*   **Attributes:** All attributes (tags) on spans and metrics MUST follow [OpenTelemetry Semantic Conventions](https://opentelemetry.io/docs/specs/semconv/). You MUST use the most recent version (currently `v1.37.0`).
*   **Forbidden Attributes:** You MUST NOT use `http.url` (or `url.full`) as it frequently includes sensitive data like OAuth tokens or PII. Instead, use `http.path` with appropriate placeholders for variables (e.g., `/users/{userId}`).
*   **Custom Attributes:** If no standard convention exists, use a consistent namespaced prefix (e.g., `app.your_domain.attribute_name`).

## 4. Metrics
*   **Runtime Metrics:** The following runtime metrics MUST be captured:
    *   Connection pool usage (DB, HTTP clients)
    *   Thread pool / Goroutine usage
    *   Scheduler latency
*   **OS Metrics:** Linux/OS metrics (CPU, Memory, Disk I/O) MUST NOT be captured by the application. These are the responsibility of the infrastructure agent.

## 5. Reporting Configuration
*   **Frequency:** Metrics MUST be reported at a 60-second frequency by default.
*   **Batching:** Data MUST be batched before transport to reduce network overhead.

## 6. Logging
*   **Library:** `log/slog` MUST be used for all logging. The default handler MUST be `TextHandler`.
*   **Configuration:** The application MUST expose a `--log-level` CLI flag to allow the user to configure the log level (e.g., `debug`, `info`, `warn`, `error`).
*   **Usage:**
    *   **Debug:** Use for detailed information useful during development or debugging.
    *   **Lifecycle:** Use for high-level application events (startup, shutdown, signal handling).
    *   **Forbidden:** Do NOT use logs for access logs or tracking high-volume events (use Tracing).
