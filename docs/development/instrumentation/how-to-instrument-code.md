# How-to: Instrument Code

This guide provides practical steps for adding instrumentation to your code. For the underlying philosophy and strategy, see [Explanation: Observability Strategy](./explanation-observability-strategy.md).

## How to Initialize Instrumentation

Instrumentation (Tracer and Meter) should be injected as dependencies into your service structs. This allows for better testing and configuration. Use the functional options pattern for initialization.

```go
import (
	"go.opentelemetry.io/otel"
	"go.opentelemetry.io/otel/metric"
	"go.opentelemetry.io/otel/trace"
)

const instrumentationName = "github.com/org/repo/pkg/service"

type Service struct {
	tracer trace.Tracer
	meter  metric.Meter
}

type Option func(*Service)

// WithTracerProvider configures the service with a specific tracer provider.
func WithTracerProvider(tp trace.TracerProvider) Option {
	return func(s *Service) {
		s.tracer = tp.Tracer(instrumentationName)
	}
}

// WithMeterProvider configures the service with a specific meter provider.
func WithMeterProvider(mp metric.MeterProvider) Option {
	return func(s *Service) {
		s.meter = mp.Meter(instrumentationName)
	}
}

func NewService(opts ...Option) *Service {
	// Initialize with defaults (global providers)
	s := &Service{
		tracer: otel.Tracer(instrumentationName),
		meter:  otel.Meter(instrumentationName),
	}
	for _, opt := range opts {
		opt(s)
	}
	return s
}
```

## How to Start a Span

When starting a new operation, use the injected tracer to create a span named after the business logic.

```go
// GOOD: Named after business logic
ctx, span := s.tracer.Start(ctx, "calculate-tax")
defer span.End()

// BAD: Named after transport
ctx, span := s.tracer.Start(ctx, "GET /tax")
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
ctx, span := s.tracer.Start(ctx, "call-downstream-service",
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

When making remote procedure calls (RPCs), you should mark your spans with the appropriate `SpanKind` (Client or Server) and use standard semantic attributes. See also [How to Design RPC Interfaces](../rpc/how-to-design-rpc-interfaces.md) for interface design guidelines.

> **Note:** Where possible, prefer using helper functions from semantic convention libraries (e.g., `httpconv` for HTTP) to automatically determine attributes from existing objects. Always use the most recent version of semantic conventions (currently `v1.37.0`).

### gRPC Services

For gRPC, use the semantic convention constants. Use the `Key` constants to create attributes.

**Client Side:**

```go
import (
	"context"

	"go.opentelemetry.io/otel/trace"
	semconv "go.opentelemetry.io/otel/semconv/v1.37.0"
)

func (c *Client) CallRPC(ctx context.Context) error {
	// Use semconv constants for attributes
	ctx, span := c.tracer.Start(ctx, "my.rpc.Method",
		trace.WithSpanKind(trace.SpanKindClient),
		trace.WithAttributes(
			semconv.RPCSystemKey.String("grpc"),
			semconv.RPCServiceKey.String("my.service.Name"),
			semconv.RPCMethodKey.String("Method"),
			// Add peer info if available
			semconv.NetPeerNameKey.String("users-service"),
			semconv.NetPeerPortKey.Int(8080),
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
    ctx, span := s.tracer.Start(ctx, "my.rpc.Method",
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

	"go.opentelemetry.io/otel/trace"
	"go.opentelemetry.io/otel/semconv/v1.37.0/httpconv"
)

func (c *Client) CallHTTP(req *http.Request) error {
	// Use httpconv to extract standard attributes like http.method.
	// NOTE: Ensure `http.url` is NOT recorded as it may contain PII.
	// Use `http.path` with placeholders instead.
	attrs := httpconv.ClientRequest(req)

	ctx, span := c.tracer.Start(req.Context(), "HTTP "+req.Method,
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
    // 1. Extract context for distributed tracing propagation
    ctx := otel.GetTextMapPropagator().Extract(r.Context(), propagation.HeaderCarrier(r.Header))

    // 2. Extract standard server attributes
    // NOTE: Filter out `http.url` to avoid PII/tokens; use `http.path` with placeholders.
    attrs := httpconv.ServerRequest("my-server", r)

    // 3. Start Span
    ctx, span := h.tracer.Start(ctx, "HTTP "+r.Method,
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

Use the `semconv` package to ensure you are using standard attribute names. Always use the most recent version of the semantic conventions (currently `v1.37.0`).

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
