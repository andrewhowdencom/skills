# How to Write Go Code

## General Advice
For writing Go code, ensure that you follow out the best practices described by the [effective go] documentation,
and prefer convententions from the standard library wherever possible.

Where I want to release an application to GitHub, prefer to use the [open source goreleaser] to run these releases.
Do a cross compilation build, and upload binaries suitable for the common platforms.

[effective go]: https://go.dev/doc/effective_go
[the open source goreleaser]: https://goreleaser.com/

## Error Handling

For packages that are reused in the application (e.g. anything in `internal`), it is better to "wrap" errors that
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
