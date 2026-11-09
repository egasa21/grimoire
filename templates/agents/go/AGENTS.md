# Go coding agent instructions

## Workflow

1. Inspect the existing repository structure, instructions, build setup, and conventions.
2. Trace the relevant code paths, callers, tests, and configuration before editing.
3. Make the smallest coherent change that completes the task.
4. Preserve existing architectural decisions unless a concrete problem justifies changing them.
5. Reuse existing code and standard-library functionality; avoid unnecessary dependencies and abstractions.
6. Add or update tests for changed behavior where appropriate, including failure cases.
7. Format changed Go files with `gofmt` and run appropriate repository checks.
8. Report what changed, what verification ran, and any remaining limitations.

## Architecture and packages

- Follow idiomatic Go and the repository's established conventions.
- Prefer simple, cohesive packages organized by actual responsibility or business
  capability where appropriate. Keep exported APIs small and imports acyclic.
- Use `cmd/<app-name>/` for entry points and `internal/` for private implementation
  packages when they benefit the project. A root package may be sufficient.
- Treat directory layouts as guidance, not mandatory scaffolds. Placeholder names
  are not literal directories. Do not invent feature packages or add empty folders.
- Add bootstrap or shared infrastructure packages only when responsibilities need
  them. Avoid generic `common`, `utils`, or `interfaces` dumping grounds.
- Do not introduce architectural layers or patterns without a concrete need.
  Packages, rather than individual source files, provide encapsulation.

## Dependencies and abstraction

- Prefer explicit constructor injection for required dependencies and concrete
  structs by default. Avoid global mutable state.
- Introduce interfaces only for real substitution or dependency boundaries. Prefer
  small interfaces defined near their consumers; usually return concrete types.
- Prefer composition. Do not simulate inheritance or embed types merely to reuse code.
- Avoid dependency injection frameworks unless the project's needs justify them.

## Implementation

- Use descriptive Go names and consistent initialisms. Follow `gofmt` and address
  relevant `go vet` findings.
- Handle errors explicitly. Wrap with `fmt.Errorf` and `%w` when useful context
  and inspection of the underlying error belong in the caller's contract.
- Use `errors.Is` and `errors.As` where appropriate; do not match error strings.
  Do not panic for expected input or operational errors.
- Pass `context.Context` first through cancellation-aware operations. Preserve the
  caller's context, call derived cancel functions, and avoid storing contexts
  unnecessarily in structs. Do not use context values for optional parameters.
- Do not start uncontrolled goroutines. Define ownership, cancellation, termination,
  and completion waiting. Bound concurrent work and synchronize shared state.
- Prefer standard-library functionality when sufficient and compatible with the
  project's supported Go version.
- Validate configuration at an explicit boundary and pass parsed values to consumers.
- Follow the existing logging approach; use structured fields when logging is
  needed. Avoid logging the same error repeatedly as it propagates.

## Data and security

Apply these rules when the corresponding concerns exist; do not introduce a
database, authentication system, or other subsystem to satisfy this checklist.

- Treat external input as untrusted. Validate it at the appropriate boundary.
- Never log secrets or sensitive credentials. Keep them out of source control.
- Enforce authorization at the appropriate trust boundary when access is restricted.
- Use parameterized database queries for values. Allowlist dynamic identifiers;
  never construct SQL from unchecked external input.
- Handle transactions explicitly: arrange rollback on failure, use the transaction
  for all participating operations, and check commit errors.
- Maintain data integrity through validation and database constraints where applicable.
- Close database rows and other owned resources; check iteration and cleanup errors
  when they can affect correctness.
- Use idempotency where retries can cause duplicate side effects. Retry only when
  failure semantics and the operation's safety are understood.

## Testing and verification

- Test observable behavior rather than implementation details. Prefer table-driven
  tests when cases share a useful structure.
- Keep tests deterministic. Control time, randomness, and external state where needed;
  do not use sleeps as synchronization.
- Add integration tests where actual external dependencies affect correctness.
  Make setup, isolation, and cleanup explicit.
- Use the standard `testing` package and existing test helpers by default. Avoid
  unnecessary third-party test dependencies.
- Follow repository verification commands and module/workspace boundaries. Usually
  run `go test ./...` and `go vet ./...`; run `go test -race ./...` for concurrency
  changes where supported. A passing race check covers only executed paths.
- Do not claim a check passed if it was skipped or could not run. Explain the limitation.

## References

- [Go Code Review Comments](https://go.dev/wiki/CodeReviewComments)
- [Organizing a Go module](https://go.dev/doc/modules/layout)
- [Standard-library documentation](https://pkg.go.dev/std)
