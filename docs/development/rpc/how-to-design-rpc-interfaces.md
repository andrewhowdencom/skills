# How to Design RPC Interfaces

This guide outlines the standards and best practices for defining RPC (Remote Procedure Call) interfaces in this project.
These principles apply to various RPC technologies, including gRPC and RESTful APIs.

## Context Propagation

**Rule:** Every function that represents an RPC entry point or client call MUST accept a `context.Context` as its first
argument.

This allows the application to:
1.  Propagate cancellation signals (e.g., when a client disconnects or a timeout occurs).
2.  Pass request-scoped values (e.g., authentication tokens, trace IDs).

### Example (Go)

```go
// GOOD: Context is the first argument
func (s *Server) GetUser(ctx context.Context, req *pb.GetUserRequest) (*pb.GetUserResponse, error) {
    // ...
}

// BAD: No context
func (s *Server) GetUser(req *pb.GetUserRequest) (*pb.GetUserResponse, error) {
    // ...
}
```

## Distributed Tracing

**Rule:** If the application includes OpenTelemetry as a dependency, all RPCs MUST be instrumented with distributed tracing.

This ensures that the execution of the RPC can be tracked and visualized in a distributed tracing system (e.g., Jaeger,
Zipkin, Honeycomb).

### Implementation

1.  **Extract Context:** On the server side, extract the parent trace context from the incoming request (usually handled
    by middleware/interceptors, but ensure it's propagated to the function).
2.  **Start Span:** Start a new span for the RPC execution using the `context.Context`.
3.  **End Span:** Ensure the span is ended when the RPC completes (usually using `defer`).

### Example (Go with OpenTelemetry)

```go
import "go.opentelemetry.io/otel"

func (s *Server) GetUser(ctx context.Context, req *pb.GetUserRequest) (*pb.GetUserResponse, error) {
    // Use the global tracer provider or a specific one
    tracer := otel.Tracer("my-service")

    // Start a new span.
    // Ideally, the parent context is already extracted by middleware and passed in 'ctx'.
    ctx, span := tracer.Start(ctx, "GetUser")
    defer span.End()

    // ... business logic ...

    return response, nil
}
```
