# Go conventions

These are recommended defaults. Match established repository conventions unless
there is a concrete reason to change them. Correctness and security are requirements,
regardless of the chosen layout.

## Names and packages

Use short, descriptive, lowercase package names. Avoid repetitive exported names:
`cache.New` reads better than `cache.NewCache`. Use `MixedCaps` for identifiers
and keep initialisms consistent, such as `userID` and `HTTPClient`.

Keep packages cohesive and exports small. Organize around actual responsibilities
or business capabilities; avoid catch-all packages such as `utils` and `common`.
Source files help navigation, but packages determine encapsulation.

Use `gofmt` for formatting. The official [Go Code Review Comments](https://go.dev/wiki/CodeReviewComments)
cover naming, interfaces, and other review conventions.

## Dependencies and abstraction

Prefer concrete structs and explicit constructor parameters for required
dependencies. Wire them at the application's composition point. Avoid global
mutable state and dependency injection frameworks without a concrete need.

Compose behavior through fields and methods instead of simulating inheritance.
Embedding also promotes methods into a type's API; use it deliberately.

Define a small interface near its consumer when substitution or a real boundary
requires it. Constructors usually return concrete types. Do not create an
interface for every struct or build layers for hypothetical future implementations.
See the Go wiki's [interface guidance](https://go.dev/wiki/CodeReviewComments#interfaces).

## Errors

Handle errors explicitly: recover, translate, or return them. Add useful operation
context rather than repeating the same message at every level. For example:

```go
if err != nil {
    return fmt.Errorf("read settings: %w", err)
}
```

Use `%w` when callers should inspect the underlying error with `errors.Is` or
`errors.As`. Wrapping exposes that error as part of the API contract; translate
implementation details when they should stay private. Avoid matching error text.
Reserve panics for exceptional programming failures, not expected input errors.

See [Working with Errors in Go](https://go.dev/blog/go1.13-errors).

## Context and concurrency

Pass `context.Context` as the first parameter to cancellation-aware operations.
Propagate the caller's context rather than replacing it with `context.Background()`.
Create a root context at the operation's entry point when needed, and call the
cancel function returned by a derived context. Avoid storing contexts in structs
or using context values as optional function parameters.

Before starting a goroutine, identify its owner, termination condition, and how
the owner waits for completion. Cancellation is cooperative: blocking operations
must observe it. Bound concurrent work, synchronize shared state, and define who
closes channels. Do not use sleeps as synchronization.

References: [`context`](https://pkg.go.dev/context),
[Go concurrency pipelines and cancellation](https://go.dev/blog/pipelines).

## Logging and configuration

Prefer the existing logger; otherwise standard-library
[`log/slog`](https://pkg.go.dev/log/slog) is a structured logging option.
Use stable field names and meaningful levels. Log failures at the boundary that
handles them, avoiding repeated logs as an error propagates. Never log secrets,
credentials, or unfiltered sensitive payloads.

Read configuration at startup or an explicit reload boundary. Parse and validate
it once, then pass typed values to consumers. Use `os.LookupEnv` when unset and
empty have different meanings. Document required values and defaults; fail clearly
on invalid required configuration. Keep secrets out of source control.

## Database access, when applicable

Use parameterized queries for values; placeholder syntax depends on the driver.
Never interpolate untrusted values into SQL. Dynamic identifiers require an
explicit allowlist or another safe construction strategy.

Reuse `sql.DB` as a connection pool. Close query rows, check scan errors and
`rows.Err()`, and use context-aware operations where supported.

Keep transactions short and tied to a complete consistency requirement. Begin
explicitly, arrange rollback on failure, execute all participating operations
through the transaction, and check the commit error. Do not mix pool operations
into a transaction. Enforce invariants with database constraints as well as
application validation. Retry only with understood failure semantics and protect
side effects from duplication where necessary.

References: [SQL injection prevention](https://go.dev/doc/database/sql-injection),
[executing transactions](https://go.dev/doc/database/execute-transactions),
[`database/sql`](https://pkg.go.dev/database/sql).

## Tests and maintainability

Use the standard `testing` package by default. Table-driven tests help when cases
share a meaningful setup and assertion; separate tests can be clearer for different
behaviors. Test observable outcomes, including failures and boundary cases.

Keep tests deterministic: control time, randomness, and external state where they
matter. Use integration tests for behavior that depends on a real database,
filesystem, protocol, or other external system. Keep their setup and cleanup explicit.

Run the repository's checks, usually `go test ./...` and `go vet ./...`, from the
appropriate module or workspace. For concurrency changes, run `go test -race ./...`
where supported. The race detector only checks executed paths; a passing run is
not proof that all concurrent behavior is safe. Measure a demonstrated performance
problem before adding complexity.

References: [`testing`](https://pkg.go.dev/testing),
[`go vet`](https://pkg.go.dev/cmd/vet),
[race detector](https://go.dev/doc/articles/race_detector).

See also [project structure](project-structure.md) and the
[language-independent review checklist](../../practices/code-review/checklist.md).
