# Go project structure

A simple, feature-oriented monolith can keep related behavior together without
requiring a fixed set of architectural layers. This is a conceptual layout, not
a mandatory scaffold:

```text
project/
├── cmd/
│   └── <app-name>/
│       └── main.go
├── internal/
│   ├── app/          # Optional bootstrap and wiring
│   ├── platform/     # Optional shared infrastructure
│   └── <feature>/    # Business packages as needed
├── migrations/      # Optional
├── sql/             # Optional
├── go.mod
├── go.sum           # When generated
├── Dockerfile       # Optional
└── Makefile         # Optional
```

`<app-name>` and `<feature>` are placeholders, not literal directory names.
Create directories only when their responsibilities exist. Do not predefine
business features from this example.

## Responsibilities

- `cmd/<app-name>/` contains an executable's entry point. Keep wiring there until
  an `internal/app` package makes it easier to follow or reuse.
- `internal/` restricts imports to the tree rooted at its parent directory.
  Use it for implementation packages that should not become an external API.
- Feature packages own cohesive application responsibilities. Keep related behavior,
  types, and tests together; split a package when its responsibilities warrant it.
- `internal/platform/` is optional infrastructure shared by actual consumers.
  Give its packages specific names and keep business policy out of them. Do not
  let it become a miscellaneous dumping ground.
- `migrations/` and `sql/` hold database changes and query files only if needed.
- `go.mod` describes the module. Keep `go.sum` when the Go tool generates it;
  deployment and convenience files depend on how the project is built and run.

## Start smaller

A small application may need only `main.go`, its tests, and `go.mod` at the root.
A reusable library may expose packages outside `internal/` and have no `cmd/`
directory at all.

Go packages, not individual source files, are the meaningful encapsulation units:
unexported names are accessible throughout their package. Splitting a file into
several files does not create a new boundary.

Let the architecture evolve with application complexity. Keep imports acyclic,
and add a boundary only when it improves a real dependency or responsibility.
Clean Architecture, Hexagonal Architecture, DDD, CQRS, event sourcing, and strict
modular monolith rules are not prerequisites for this layout.

See [Go conventions](conventions.md), [architecture fundamentals](../../fundamentals/architecture/README.md),
and the official [Organizing a Go module](https://go.dev/doc/modules/layout) guide.
