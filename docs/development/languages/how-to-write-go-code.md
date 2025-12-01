# How to Write Go Code

## General Advice
For writing Go code, ensure that you follow the best practices described by the [effective go] documentation,
and prefer conventions from the standard library wherever possible.

Where I want to release an application to GitHub, prefer to use the [open source goreleaser] to run these releases.
Do a cross compilation build, and upload binaries suitable for the common platforms. This should typically be done via [GitHub Actions](../ci/how-to-use-github-actions.md).

[effective go]: https://go.dev/doc/effective_go
[the open source goreleaser]: https://goreleaser.com/

## Reusable Libraries

We maintain a collection of reusable libraries in `andrewhowdencom/stdlib`. Before implementing common functionality, check the [Standard Library Index](reference-stdlib-index.md) to see if a solution already exists.

If you are developing a library that is intended to be used across multiple applications, it should be placed in `andrewhowdencom/stdlib` and added to the index.

### Testable Examples

For Go libraries, examples in documentation should be **Testable Examples**. These are snippets of Go code that are compiled and run as part of the package's test suite (in `_test.go` files, functions starting with `Example`).

*   Write `Example` functions to demonstrate how to use the package's API.
*   These examples appear in the GoDoc automatically.
*   Reference or include these examples in your `README.md` to ensure that the documentation stays in sync with the code and is verified by tests.

## Error Handling

For packages that are reused in the application (e.g. anything in `internal` which usually follows [Hexagonal Architecture](../architecture/explanation-hexagonal-architecture.md)), it is better to "wrap" errors that
are returned upstream so that unit tests, and if necessary, business logic, can examine them. For example,
instead of code that does:

```go
func DoSomething() (string, error) {

	// Assume this error was returned from a client
	return "", fmt.Errorf("upstream error")
}
```

Define a new error value at the top of the package, and use that to represent this error. For example,

```go
// Err* are common errors
var (
	ErrUpstreamFailure = errors.New("dependency failed")
)

func DoSomething() (string, error) {

	// Assume this error was returned from a client
	return "", fmt.Errorf("%w: %s", ErrUpstreamFailure, fmt.Errorf("upstream error"))
}
```

## Object Construction

When creating constructors for objects (structs) in Go, prefer the **Functional Options Pattern** as recommended in [How to Manage Dependencies](../architecture/how-to-manage-dependencies.md). This approach uses variadic arguments to handle optional configuration while keeping the API clean and extensible.

*   **Required Arguments:** Pass required dependencies as explicit, typed arguments to the constructor function (e.g., `NewServer`). To avoid stutter, prefer naming the function `New` if the package creates a single object (e.g., `server.New` instead of `server.NewServer`).
*   **Optional Arguments:** Use variadic arguments (e.g., `...Option`) for optional configuration. Options should return an `error` to allow for validation.
*   **Defaults:** The struct should be initialized with sane defaults for any optional arguments that are not provided.

### Example

```go
package server

import (
	"errors"
	"time"
)

type Server struct {
	host    string
	port    int
	timeout time.Duration
}

// Option defines a functional option for configuring the Server.
type Option func(*Server) error

// WithTimeout sets a custom timeout for the server.
func WithTimeout(d time.Duration) Option {
	return func(s *Server) error {
		if d <= 0 {
			return errors.New("timeout must be positive")
		}
		s.timeout = d
		return nil
	}
}

// New creates a new Server with the given required arguments and options.
// Required: host, port
// Optional: timeout (defaults to 30s)
func New(host string, port int, opts ...Option) (*Server, error) {
	s := &Server{
		host:    host,
		port:    port,
		timeout: 30 * time.Second, // Sane default
	}

	for _, opt := range opts {
		if err := opt(s); err != nil {
			return nil, err
		}
	}

	return s, nil
}
```
