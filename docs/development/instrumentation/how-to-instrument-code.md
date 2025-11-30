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

## Instrumenting RPCs

When making remote procedure calls (RPCs), you should mark your spans with the appropriate `SpanKind` (Client or Server) and use standard semantic attributes.

### gRPC Services

For gRPC, use the semantic convention constants. Use the `Key` constants to create attributes.

**Client Side:**

```go
import (
	"context"

	"go.opentelemetry.io/otel"
	semconv "go.opentelemetry.io/otel/semconv/v1.37.0"
	"go.opentelemetry.io/otel/trace"
)

func (c *Client) CallRPC(ctx context.Context) error {
	tracer := otel.Tracer("github.com/org/repo/pkg/service")

	// Use semconv constants for attributes
	ctx, span := tracer.Start(ctx, "my.rpc.Method",
		trace.WithSpanKind(trace.SpanKindClient),
		trace.WithAttributes(
			semconv.RPCSystemKey.String("grpc"),
			semconv.RPCServiceKey.String("my.service.Name"),
			semconv.RPCMethodKey.String("Method"),
		),
	)
	defer span.End()

	// Make the RPC call...
	return nil
}
```

**Server Side:**

Server-side gRPC instrumentation typically uses an interceptor, but if manual instrumentation is needed:

```go
func (s *Server) MyMethod(ctx context.Context, req *pb.Request) (*pb.Response, error) {
    // 1. Extract propagation context (optional, often handled by interceptors)
    // 2. Start span with SpanKindServer
    tracer := otel.Tracer("github.com/org/repo/pkg/service")
    ctx, span := tracer.Start(ctx, "my.rpc.Method",
        trace.WithSpanKind(trace.SpanKindServer),
        trace.WithAttributes(
            semconv.RPCSystemKey.String("grpc"),
            semconv.RPCServiceKey.String("my.service.Name"),
            semconv.RPCMethodKey.String("Method"),
        ),
    )
    defer span.End()

    // ... Handle request ...
    return &pb.Response{}, nil
}
```

### HTTP/REST Services

For HTTP clients and servers, use the `httpconv` package to automatically extract standard attributes from the request object.

**Client Side:**

```go
import (
	"net/http"

	"go.opentelemetry.io/otel"
	"go.opentelemetry.io/otel/trace"
	"go.opentelemetry.io/otel/semconv/v1.37.0/httpconv"
)

func (c *Client) CallHTTP(req *http.Request) error {
	tracer := otel.Tracer("github.com/org/repo/pkg/service")

	// Use httpconv to extract standard attributes like http.method, http.url, etc.
	attrs := httpconv.ClientRequest(req)

	ctx, span := tracer.Start(req.Context(), "HTTP "+req.Method,
		trace.WithSpanKind(trace.SpanKindClient),
		trace.WithAttributes(attrs...),
	)
	defer span.End()

	// Perform the request...
	return nil
}
```

**Server Side:**

```go
import (
    "net/http"
    "go.opentelemetry.io/otel"
    "go.opentelemetry.io/otel/trace"
    "go.opentelemetry.io/otel/propagation"
    "go.opentelemetry.io/otel/semconv/v1.37.0/httpconv"
)

func (h *Handler) ServeHTTP(w http.ResponseWriter, r *http.Request) {
    tracer := otel.Tracer("github.com/org/repo/pkg/service")

    // 1. Extract context for distributed tracing propagation
    ctx := otel.GetTextMapPropagator().Extract(r.Context(), propagation.HeaderCarrier(r.Header))

    // 2. Extract standard server attributes
    attrs := httpconv.ServerRequest("my-server", r)

    // 3. Start Span
    ctx, span := tracer.Start(ctx, "HTTP "+r.Method,
        trace.WithSpanKind(trace.SpanKindServer),
        trace.WithAttributes(attrs...),
    )
    defer span.End()

    // ... Handle request ...
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
    semconv "go.opentelemetry.io/otel/semconv/v1.37.0"
)

// ...

span.SetAttributes(
    semconv.HTTPRequestMethodKey.String("GET"),
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
