# Onirigiri workspace adapter

Standalone repository for `@riteofstring/onirigiri-t3`, consumed by T3's
`apps/spatial`. It exports `reconcileWorkspaceLayout` and its layout types
directly from TypeScript source. No separate build or runtime dependency is
needed. The package remains private.

## Development

Use the Node version in `.node-version` and the exact package manager in
`package.json`.

```sh
./code-polishyw setup
pnpm install --frozen-lockfile --ignore-scripts
pnpm test
./code-polishyw check --all
```

The six reconciliation tests run independently of T3. Code Polishy's sealed
JavaScript tooling supplies formatting, lint, type, dead-code and dependency
checks; no T3 checkout or root test configuration is required.

The complete adoption check is `./code-polishyw gate`. Supplemental hardening
and Gherkin are not configured. Source comments are disabled; non-local
rationale belongs in [the design document](docs/design/layout-adapter.md).

`pnpm build` creates a source-package archive in `.code-polishy-artifacts/package`.
It packages the public TypeScript files, README and license; it does not change
the source exports or publish the package.

For local T3 development, keep this checkout at the sibling path
`onirigiri-t3-adapter`. The spatial package consumes it with
`link:../../../onirigiri-t3-adapter`; its design document records the validated
adapter commit. Keep that checkout clean and on the recorded commit when
reproducing a T3 build. The package is compiled into the application, with no
runtime repository access.

## Layout reconciliation

A saved workspace layout is a list of project rows, each holding thread panes.
The layout outlives the data it refers to: projects are removed, threads are
archived or deleted, and new threads appear between live updates.
`reconcileWorkspaceLayout` brings a saved layout up to date with the live
projects and threads before the workspace renders it.

### Rules

- The user's arrangement wins. Saved rows keep their order, and saved panes
  keep their order within a row.
- Live data decides membership. A row survives only while its project is live,
  and a thread pane survives only in the row of its own live project while the
  thread is not archived. Everything else is dropped.
- New projects are appended and new threads lead. Projects without a saved
  row are added after the saved rows. Threads without a pane are added to the
  front of their project's row, since rows run from the most recent thread and
  a new thread is the most recent. Both keep the order the live data lists
  them in.
- Each project has one row and each thread one pane. The first saved
  occurrence wins; a thread found only in another project's row is treated as
  new in its own row.
- The result reconciles to itself, so the adapter can reconcile on every live
  update without the layout drifting.

### Boundaries

The function is pure and takes its own minimal types, not T3's or
Onirigiri's. `apps/spatial` maps T3's project and thread selectors to
`LiveWorkspace` and maps rows and panes to Onirigiri's layout, so neither
upstream's types nor Onirigiri's scene format leak into this package.
