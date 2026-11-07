# Layout adapter boundary

This repository owns only the pure row reconciliation function and its minimal
input/output types. It has no runtime dependencies. T3 owns selectors, route
changes, pane lifecycle, persistence and the mapping into the engine's scene.
The reusable Onirigiri engine owns layout, camera and presentation behavior.
Neither repository is an input to this package's tests or static checks.

The reconciliation rules are recorded in the [README](../../README.md#rules).
Saved order is preserved for surviving identities, live data decides membership,
new threads precede saved panes, and the result is idempotent. Inputs are not
mutated. Tests exercise these observable results through the public export.

The package retains its existing ESM TypeScript source exports and
`sideEffects: false`. A consumer bundles the same function directly; the
repository boundary introduces no runtime loader, process, network request,
copy of application state or extra asynchronous transition. Build output and
chunk placement must be compared in T3 whenever packaging changes.

The repository has one governed module because its source, types and tests have
one purpose and no independent runtime boundary. The six-case unit suite covers
the complete package in focused and full profiles. There are no external inputs,
application UI capabilities, supplemental suites or Gherkin files. Code Polishy
owns the shared static tooling. Vitest 5.0.3 is the independent test runner,
retaining the consumer's Vitest 5 generation with an aged fixed release.
The build provider produces the TypeScript source-package archive, so development
checks need no second compiler or alternate runtime entry point.

The source was extracted from `packages/onirigiri-t3` at T3 commit
`b13e3c7b8e39259e0049ed3086eb499b83960966`. Reconciliation logic and public
exports remain unchanged. The original MIT license is retained in `LICENSE`.
