# How-to: Instrument Code

This guide provides practical steps for adding instrumentation to your code.

## How to Start a Span

When starting a new operation, create a span named after the business logic.

```go
// GOOD: Named after business logic
ctx, span := tracer.Start(ctx, "calculate-tax")
defer span.End()

// BAD: Named after transport
ctx, span := tracer.Start(ctx, "GET /tax")
defer span.End()
```

## How to Record Errors

Always record errors on the span and set the status to Error.

```go
if err != nil {
    span.RecordError(err)
    span.SetStatus(codes.Error, err.Error())
    return err
}
```

## How to Configure the Exporter

Ensure your exporter is configured to batch data.

```go
// Example OpenTelemetry Go SDK configuration
traceProvider := sdktrace.NewTracerProvider(
    sdktrace.WithBatcher(exporter, sdktrace.WithBatchTimeout(60 * time.Second)),
)
```

## How to Use Semantic Conventions

Use the `semconv` package to ensure you are using standard attribute names.

```go
import (
    "go.opentelemetry.io/otel/attribute"
    semconv "go.opentelemetry.io/otel/semconv/v1.17.0"
)

// ...

span.SetAttributes(
    semconv.HTTPMethodKey.String("GET"),
    semconv.HTTPRouteKey.String("/users/{id}"),
)
```
