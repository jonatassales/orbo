# SPEC-036: Accept Node.js 24 or newer

- Status: Implemented for source; no publication in this change
- Created: 2026-10-25
- Updated: 2026-10-25
- Mode: Prospective
- Owner: Jonatas Sales

## Problem

`orbo-voice@1.1.1` declares `engines.node` as `24.x`. A consuming pnpm workspace on
Node.js 26.9.0 fails installation with `ERR_PNPM_UNSUPPORTED_ENGINE` (wanted
`{"node":"24.x"}`). The repository's own `orbo bootstrap`, `orbo doctor`, and package
audit also require exactly major 24, so the source checkout cannot be set up on a newer
Node.js release either.

## Scope

Relax the Node.js requirement to "24 or newer" in the package manifest, the engineering
CLI, the package audit, and the documented contract. `.nvmrc` (`24.15.0`) and CI keep
pinning the development toolchain. Package version moves to `1.1.2`. No tag, no
publication, no change to runtime source.

## Requirements

1. `package.json#engines` is exactly `{ "node": ">=24" }`.
2. `orbo bootstrap` and `orbo doctor` accept any Node.js major version `>=24` and reject
   older versions and a missing or unparsable Node.js with an actionable message.
3. `.audits/package.audit.sh` enforces the new value and rejects additional engines.
4. Rule 001 and the release context state the floor and that `.nvmrc` is not the
   consumer contract.
5. `package.json#version` is `1.1.2`.

## Acceptance criteria

- [x] `./cli/orbo doctor` passes its Node check on Node.js 26.9.0 (it failed before).
- [x] `./cli/orbo audit` and `./cli/orbo check` pass.
- [x] Packing and installing the tarball with pnpm on Node.js 26.9.0 no longer raises `ERR_PNPM_UNSUPPORTED_ENGINE`.
- [x] The package payload is unchanged apart from the version.

## Evidence

Validation on 2026-10-25, Node.js 26.9.0 and pnpm 10.32.1 (repository) / 12.4.2 (consumer projects):

- `./cli/orbo doctor` reported `FAIL node — v26.9.0` before the change and
  `PASS node — v26.9.0` after it. With dependencies installed using `--ignore-scripts`, its only
  remaining failure was `git-tooling`, because hook activation was intentionally skipped.
- `./cli/orbo check` exited 0: lint, typecheck, tests, build, and all repository audits, including
  `Node engine accepts Node.js 24 or newer`.
- Installing the packed tarball into an empty project with strict engines (`pnpm add
  --config.engine-strict=true`): the control with `engines.node` set to `24.x` failed with
  `ERR_PNPM_UNSUPPORTED_ENGINE ... wanted: {"node":"24.x"} (current: {"node":"26.9.0"})`; the
  `>=24` tarball installed. `npm install --engine-strict` gave `EBADENGINE` for the control and
  succeeded for `>=24`. Without the strict flag, pnpm 12.4.2 installed both.
- `semver.satisfies` accepts 24.15.0, 25.0.0 and 26.9.0 and rejects 23.11.0 for `>=24`.
- The packed tarball holds 41 files (`dist/`, `cli/`, `LICENSE`, `README.md`, `package.json`), with no
  `assets/`, source maps, `.agents/`, or `.audits/`. `./cli/orbo git version-check` passed for `1.1.2`.
- Not exercised: Node.js 25, the published registry artifact, and CI on a Node.js release other than
  the `.nvmrc` version.

## Related records

- ADRs: none; this relaxes a contract and changes no architecture.
- Rules: [Rule 001](../rules/001-package-contract.rule.md), [Rule 011](../rules/011-public-orb-installer.rule.md).
- SPECs: [SPEC-035](035-retire-account-derived-identity.spec.md).

## Compatibility and risks

Widening `engines` is backward compatible for installers. Node.js releases newer than
24 are accepted without being exercised in CI, which runs the `.nvmrc` version only; the
runtime library is a browser Web Component, and the CLI is POSIX shell with small inline
Node programs. `main` also carries the unreleased SPEC-035 removal of the account-derived
preset alias. SPEC-035 requires a major release for that change, so publishing this fix
as a patch (`1.1.2`) would ship the alias removal in a patch release. The release is
human-controlled; confirm the version before publishing.
