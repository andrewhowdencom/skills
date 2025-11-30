# How to Design RPC Interfaces

This guide outlines the standards and best practices for defining RPC (Remote Procedure Call) interfaces in this project.
These principles apply to various RPC technologies, including gRPC and RESTful APIs.

## Context Propagation

**Rule:** Every function that represents an RPC entry point or client call MUST accept a `context.Context` as its first
argument.

This allows the application to:
1.  Propagate cancellation signals (e.g., when a client disconnects or a timeout occurs). See [How to Implement Resilient RPCs](./how-to-implement-resilient-rpcs.md) for more on timeouts.
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
Zipkin, Honeycomb). For detailed instructions, see [How to Instrument Code](../instrumentation/how-to-instrument-code.md) and [Explanation: Observability Strategy](../instrumentation/explanation-observability-strategy.md).

### Implementation

1.  **Dependency Injection:** The server should hold a reference to a `trace.Tracer`. This tracer should be initialized
    with the name of the package (e.g., `github.com/org/repo/pkg/service`).
2.  **Extract Context:** On the server side, extract the parent trace context from the incoming request (usually handled
    by middleware/interceptors, but ensure it's propagated to the function).
3.  **Start Span:** Start a new span for the RPC execution using the injected tracer.
4.  **End Span:** Ensure the span is ended when the RPC completes (usually using `defer`).

### Example (Go with OpenTelemetry)

```go
import (
    "context"
    "go.opentelemetry.io/otel/trace"
    pb "path/to/proto"
)

type Server struct {
    tracer trace.Tracer
    // ... other dependencies
}

func NewServer(tp trace.TracerProvider) *Server {
    return &Server{
        // Name the tracer after the library/package
        tracer: tp.Tracer("github.com/your-org/your-app/pkg/your-service"),
    }
}

func (s *Server) GetUser(ctx context.Context, req *pb.GetUserRequest) (*pb.GetUserResponse, error) {
    // Start a new span using the injected tracer.
    // Ideally, the parent context is already extracted by middleware and passed in 'ctx'.
    ctx, span := s.tracer.Start(ctx, "GetUser")
    defer span.End()

    // ... business logic ...

    return &pb.GetUserResponse{}, nil
}
```
