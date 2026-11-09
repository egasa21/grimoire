# Code review checklist

Apply the items relevant to the change. Ask for evidence proportional to its risk.

- [ ] **Correctness:** Does the change meet the requirement, including boundary
  cases, invalid input, and partial failure?
- [ ] **Readability and maintainability:** Are names and responsibilities clear?
  Does it follow existing conventions without unnecessary abstractions or dependencies?
- [ ] **Error handling:** Are failures propagated or handled usefully? Are resources
  released and partial changes recovered safely?
- [ ] **Security:** Is external input validated? Are authorization checks at trust
  boundaries correct, and are secrets and sensitive data protected?
- [ ] **Concurrency, if relevant:** Are shared state, ownership, cancellation,
  synchronization, and shutdown safe? Is concurrent work bounded?
- [ ] **Database correctness, if relevant:** Are queries parameterized, constraints
  sufficient, transactions complete, and migrations safe for existing data?
  Can retries duplicate side effects?
- [ ] **Testing:** Do deterministic tests cover observable behavior, failures, and
  the regression? Do external dependencies need integration coverage? Have the
  appropriate checks passed?
- [ ] **Performance:** Is resource use reasonable for expected load and input sizes?
  Is there evidence for optimizations, and are avoidable repeated operations removed?
- [ ] **Observability, if relevant:** Can operators diagnose failures with useful
  logs, metrics, or traces without exposing sensitive data or adding excessive noise?
- [ ] **Backward compatibility:** Are callers, public contracts, stored data, and
  configuration still supported? Is any breaking change documented with a rollout
  or migration plan?

For Go-specific checks, see [Go conventions](../../languages/go/conventions.md).
