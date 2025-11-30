# How to Implement Resilient RPCs

This guide provides step-by-step instructions on implementing timeouts and retries with exponential backoff and jitter in Go to make your RPCs more resilient to transient failures. These principles apply to various RPC technologies, including gRPC and RESTful APIs (see [How to Design RPC Interfaces](./how-to-design-rpc-interfaces.md)).

## Timeouts and Deadlines

The most critical step towards resilience is to set a deadline on every outgoing request. This prevents your service from blocking indefinitely and causing cascading failures. In Go, this is typically achieved using the `context` package. See [Reference: Default Timeouts for RPCs](./reference-default-timeouts.md) for recommended values.

## Retries with Exponential Backoff and Jitter

Many failures are transient. In these cases, retrying the request after a short delay is often successful. However, retrying immediately can make a bad situation worse. The best practice is to implement retries with exponential backoff and add jitter (randomness) to the backoff duration to avoid thundering herd problems.

## gRPC

### Timeouts

In Go, you can use the `context` package to set a timeout on a gRPC request.

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

### Retries

While you can write a custom interceptor, a robust, production-ready implementation is provided by libraries like `grpc_retry` from `go-grpc-middleware`.

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

## RESTful APIs

### Timeouts

For RESTful APIs, you can set a timeout on the `http.Client`.

```go
package main

import (
	"net/http"
	"time"
)

func main() {
	client := &http.Client{
		Timeout: 30 * time.Second,
	}

	// ...
}
```

### Retries

The `go-retryablehttp` library provides a simple way to add retries to your HTTP clients.

Here's an example of how to use `go-retryablehttp`:

```go
import (
	"log"

	"github.com/hashicorp/go-retryablehttp"
)

func main() {
	client := retryablehttp.NewClient()
	client.RetryMax = 10

	resp, err := client.Get("https://example.com")
	if err != nil {
		log.Fatal(err)
	}

	// ...
}
```
