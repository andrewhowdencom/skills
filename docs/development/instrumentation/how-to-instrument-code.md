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

## How to Set Span Kind

When creating a span, you must specify the span kind to indicate the relationship between the span and the operation.

*   **Server:** For operations that handle incoming synchronous requests (e.g., HTTP server, gRPC server).
*   **Client:** For operations that make outgoing synchronous requests (e.g., HTTP client, gRPC client).
*   **Internal:** For internal operations (default).
*   **Producer:** For operations that create an asynchronous request (e.g., publishing a message to a queue).
*   **Consumer:** For operations that handle an asynchronous request (e.g., consuming a message from a queue).

```go
// Example: Starting a client span for an RPC
ctx, span := tracer.Start(ctx, "call-downstream-service",
    trace.WithSpanKind(trace.SpanKindClient),
)
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
    semconv "go.opentelemetry.io/otel/semconv/v1.26.0"
)

// ...

span.SetAttributes(
    semconv.HTTPMethodKey.String("GET"),
    semconv.HTTPRouteKey.String("/users/{id}"),
)
```

## How to Instrument RPCs

When instrumenting RPCs (Remote Procedure Calls), ensure you set the correct `SpanKind` and use the appropriate semantic conventions.

> **Note:** Where possible, prefer using helper functions from semantic convention libraries (e.g., `httpconv` for HTTP) to automatically determine attributes from existing objects.

### Client-Side RPCs

For outgoing RPCs, use `trace.SpanKindClient` and add attributes from the semantic conventions library.

```go
import (
    "go.opentelemetry.io/otel/trace"
    semconv "go.opentelemetry.io/otel/semconv/v1.26.0"
)

// ...

ctx, span := tracer.Start(ctx, "get-user",
    trace.WithSpanKind(trace.SpanKindClient),
    trace.WithAttributes(
        semconv.RPCSystemKey.String("grpc"),
        semconv.RPCServiceKey.String("UserService"),
        semconv.RPCMethodKey.String("GetUser"),
        semconv.NetPeerNameKey.String("users-service"),
        semconv.NetPeerPortKey.Int(8080),
    ),
)
defer span.End()
```

### Server-Side RPCs

For incoming RPCs, use `trace.SpanKindServer` and add attributes from the semantic conventions library.

```go
// ...
ctx, span := tracer.Start(ctx, "GetUser",
    trace.WithSpanKind(trace.SpanKindServer),
    trace.WithAttributes(
        semconv.RPCSystemKey.String("grpc"),
        semconv.RPCServiceKey.String("UserService"),
        semconv.RPCMethodKey.String("GetUser"),
    ),
)
defer span.End()
```

## How to Log

Use `log/slog` for logging.

### Initialization

Initialize the logger with a handler (e.g., Text) and set it as default.

```go
import "log/slog"

// ...

logger := slog.New(slog.NewTextHandler(os.Stdout, nil))
slog.SetDefault(logger)
```

### Logging Events

```go
// Debugging (Development only)
slog.Debug("processing order", "order_id", orderID)

// Lifecycle Events (Startup/Shutdown)
slog.Info("starting application", "version", version)
slog.Info("shutting down application", "signal", sig)
```
