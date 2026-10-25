# Release context

Intentional package payload consists of `dist/`, the POSIX shell `cli/`, and
npm's automatic root metadata files. Source maps are disabled. Provider secrets,
tests, Git hooks, `.agents/`, and `.audits/` must not enter the package. The CLI
is included only to provide the `orbo` package binary and explicit npx project
setup.

Commit messages follow Conventional Commits. Release planning maps `fix` and
`perf` to patch changes, `feat` to minor changes, and `!` or `BREAKING CHANGE`
to major changes. `package.json#version` must be canonical SemVer. A staged
version change must move forward relative to `HEAD`.

`package.json#engines.node` is `>=24`: Node.js 24 or any newer release may install
and run the package (SPEC-036). `.nvmrc` pins the development and CI toolchain and
is not the consumer contract. An exact-major `engines` value makes strict package
managers such as pnpm reject installs on newer Node.js releases.

A release-oriented source change runs `orbo check`; CI also lints commit history
through the checked-in Orbo entry point and runs `npm pack --dry-run`. The
`prepack` lifecycle delegates to the same Orbo gate. New exports, attributes,
properties, methods, events, package binaries, and entry points are compatibility
commitments and require an explicit SPEC plus an ADR when the commitment is
architectural.

`package.json#scripts` does not mirror the Orbo command surface. A root-only local
`pnpm:devPreinstall` hook provisions the managed source-checkout launcher, `setup`
is its recovery bridge, and `prepack` is the npm release gate. After local source
setup, engineering commands use `orbo <command>` directly.

Consumer project setup remains explicit through
`npx orbo-voice --setup`. Standard dependency lifecycles
`preinstall`, `install`, `postinstall`, and `prepare` do not provision repository
state or a user launcher for application consumers. Harness-score remains
explicit engineering-only tooling and is never part of the runtime API.

ADR-0025/SPEC-034 make Orbo the product, `orbo-voice` the npm package, `orbo`
the CLI, and `<orb-o>` the element. GitHub metadata targets `jonatassales/orbo`.
ADR-0022/SPEC-031 and ADR-0024/SPEC-033 record the previous distribution
identity. ADR-0023/SPEC-032 record a rejected unscoped rename. ADR-0021/SPEC-030
record the earlier rejected package name. ADR-0018/SPEC-027 are historical records of the previous
scoped npm name. ADR-0019/SPEC-028 restored NeonGate (`neongate`) as the canonical default.
SPEC-035 removes the account-derived compatibility alias from current source. This
is a breaking change and needs a separately approved major release. Keep the
current published version immutable and do not publish by merging a metadata PR.
Configure the publisher for `jonatassales/orbo`; npm and GitHub account names do
not need to match. Never overwrite a published version or move its release tag.

Agent runtime guardrails deny autonomous package publication and require human approval for tag, push, merge/rebase, and PR-merge boundaries. These hooks supplement, but do not replace, Orbo checks, Git hooks, and CI.
