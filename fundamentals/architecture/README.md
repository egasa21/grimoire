# Architecture fundamentals

Architecture is a set of decisions about responsibilities, dependencies, and
operational constraints. Start with the simplest design that meets the actual
requirements.

## Choose boundaries from the problem

- Group behavior and data that change together. Separate responsibilities when
  doing so makes ownership or dependencies clearer.
- Keep dependency direction understandable. A boundary should reduce the amount
  a reader needs to know to make a change.
- Choose deployment boundaries for concrete needs such as independent releases,
  isolation, or scaling. Each boundary adds communication and operational work.
- Record consequential decisions with their context, trade-offs, and reasons to
  revisit them. A short note is often enough.

## Evolve with evidence

Follow a representative operation through the design: who owns the data, what
can fail, and how does recovery work? Account for consistency, latency, and
resource limits before adding more moving parts.

Patterns are tools, not prerequisites. Introduce a layer or abstraction when it
solves an observed problem and has a clearer cost than the alternatives.

See the [Go monolith layout](../../languages/go/project-structure.md) for one
concrete application and the [review checklist](../../practices/code-review/checklist.md)
for change-level questions.
