# Explanation: Observability Strategy

## Why OpenTelemetry?

We use OpenTelemetry as our standard instrumentation methodology because it provides a vendor-neutral, unified way to collect traces, metrics, and logs. This avoids vendor lock-in and allows us to switch backends without re-instrumenting our code.

## Tracing vs. Metrics

We prioritize **distributed tracing** as our primary mechanism for understanding system behavior. Traces provide context across service boundaries, allowing us to see the full lifecycle of a request.

*   **Traces** tell the story of a single request.
*   **Metrics** provide aggregated views of system health.

We use traces to debug specific issues and understand flow, while metrics are used for high-level health monitoring (e.g., error rates, latency distributions).

## Naming Philosophy

We name spans after the **business logic operation** (e.g., `process-order`, `calculate-tax`) rather than the low-level transport details (e.g., `POST /order`). This makes traces more meaningful to developers and business stakeholders, as it reflects *what* the system is doing, not just *how* it is receiving data.

## Semantic Conventions

We use **Semantic Conventions** to standardize the names of attributes (tags) on spans and metrics. This ensures that data is consistent across different services and can be easily queried and analyzed by our observability tools. For example, an HTTP status code should always be `http.response.status_code`, not `status` or `http_code`.
