# How to Implement Resilient RPCs

This guide provides step-by-step instructions on implementing timeouts and retries with exponential backoff and jitter in Go to make your RPCs more resilient to transient failures.

## Timeouts and Deadlines

The most critical step towards resilience is to set a deadline on every outgoing request. In Go, this is achieved using the `context` package. By creating a `context` with a timeout, you tell the gRPC client to abandon the request if a response is not received within the specified duration.

```go
package main

import (
	"context"
	"log"
	"time"

	"google.golang.org/grpc"
	pb "path/to/your/proto" // Your protobuf package
)

func main() {
	conn, err := grpc.Dial("downstream-service:50051", grpc.WithInsecure())
	if err != nil {
		log.Fatalf("did not connect: %v", err)
	}
	defer conn.Close()

	c := pb.NewYourServiceClient(conn)

	// Create a context with a 2-second deadline.
	ctx, cancel := context.WithTimeout(context.Background(), 2*time.Second)
	defer cancel()

	r, err := c.YourMethod(ctx, &pb.YourRequest{Name: "world"})
	if err != nil {
		// This will fire if the deadline is exceeded.
		log.Fatalf("could not call method: %v", err)
	}
	log.Printf("Response: %s", r.GetMessage())
}
```

## Retries with Exponential Backoff and Jitter

Many failures are transient. In these cases, retrying the request after a short delay is often successful. However, retrying immediately can make a bad situation worse.

The best practice is to implement retries with exponential backoff. This means the client waits for a progressively longer duration between each failed attempt. Adding jitter (randomness) to the backoff duration can help to avoid thundering herd problems.

While you can write a custom interceptor, a robust, production-ready implementation is provided by libraries like `grpc_retry` from `go-grpc-middleware`. This allows you to easily configure retry policies for specific gRPC status codes and set a maximum number of attempts.

Here's an example of how to use `grpc_retry` with an exponential backoff policy:

```go
import (
	"time"

	"github.com/grpc-ecosystem/go-grpc-middleware/retry"
	"google.golang.org/grpc"
	"google.golang.org/grpc/codes"
)

func main() {
	opts := []retry.CallOption{
		retry.WithBackoff(retry.BackoffExponential(100 * time.Millisecond)),
		retry.WithCodes(codes.NotFound, codes.Aborted),
	}

	conn, err := grpc.Dial("downstream-service:50051",
		grpc.WithStreamInterceptor(retry.StreamClientInterceptor(opts...)),
		grpc.WithUnaryInterceptor(retry.UnaryClientInterceptor(opts...)),
	)
	if err != nil {
		log.Fatalf("did not connect: %v", err)
	}
	defer conn.Close()

	// ...
}
```
