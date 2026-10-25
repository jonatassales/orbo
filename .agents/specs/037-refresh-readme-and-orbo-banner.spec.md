# SPEC-037: Refresh the README and replace the Orbz banner

- Status: Implemented for source; no publication in this change
- Created: 2026-10-25
- Updated: 2026-10-25
- Mode: Prospective
- Owner: Jonatas Sales

## Problem

The consumer README still shows a banner that renders the retired wordmark "Orbz",
links its documentation to `neongate.com.br/docs/orbz/overview` (an `orbz` path from a
retired identity), and says nothing about the Node.js requirement or the options of
`npx orbo-voice --setup`. Several active engineering files still say OrbV (`orbv check`,
`./cli/orbv`, "OrbV maintainers"), and the ownership audit does not see them because it
skips `.agents/specs/` and `.agents/adrs/`.

## Scope

Revise `README.md`, replace `assets/images/readme-banner.png`, point the documentation
URL at the GitHub README, and correct stale identity text in active files. Historical
SPEC and ADR bodies are not rewritten. The GitHub social-preview image is a repository
setting and is not changed here. No runtime source changes.

## Requirements

1. The product is named **Orbo** throughout the README. `orbo-voice` appears only as the
   npm package name in install commands, imports, and package metadata.
2. The banner keeps its path and 1983x793 size and shows the wordmark "Orbo". The sphere
   artwork is the existing raster; only the wordmark area is replaced.
3. The README documents the Node.js requirement (`>=24`, SPEC-036), all four supported
   package managers, and the verified `orbo` setup options (`--project`,
   `--package-manager`, `--package-spec`, `--dry-run`, `--force`).
4. `package.json#homepage`, the README Documentation link, and the audits that pin them
   use `https://github.com/jonatassales/orbo#readme`.
5. `.agents/specs/workflow.md`, `.agents/specs/template.md`, `.agents/adrs/readme.md`, and
   `.cursor/hooks/feedback-edit.cjs` use the Orbo / `orbo` identity.
6. The README keeps the headings and tokens enforced by `documentation.audit.sh` and adds
   no repository-maintainer material (Rule 011 item 12).

## Acceptance criteria

- [x] `./cli/orbo audit` passes, including the documentation and ownership audits.
- [x] The rendered banner reads "Orbo", with no remnant of the old wordmark or a visible patch seam.
- [x] Documented setup options match the behavior of `orbo setup` in dry-run mode.
- [x] Every attribute, property, method, and event in the README tables exists in source.
- [x] No README text contains `orbz`, `orbv`, `orb-voice`, or `Orb Voice`.

## Evidence

Validation on 2026-10-25, Node.js 26.9.0:

- `./cli/orbo check` exited 0, including the documentation audit (hero order, consumer links,
  headings, API tokens) and the ownership audit (homepage `https://github.com/jonatassales/orbo#readme`).
- Banner: derived from the previous PNG by covering the old wordmark with a feathered patch
  interpolated from the surrounding background, then rendering "Orbo" (Avenir Next, "Orb" white and
  "o" `#8C60FC`, the sampled color of the former "z") with headless Chrome. The result is a
  1983x793 RGB PNG. Between the old and new images, the patch seam differs by at most 4 of 255
  per channel (average under 0.5), and glyph height matches the old wordmark exactly (y 548-609).
  The result was inspected at 2x zoom.
- `orbo setup --project <temp> --dry-run`, with and without `--package-manager` and
  `--package-spec`, printed the documented install commands and left `package.json` unchanged;
  `--package-spec left-pad` was rejected with status 2.
- Property, method, and event names in the README tables were matched against
  `src/element/element.types.ts` and `src/factories/element-class.factory.ts`.
- A tracked-file scan for `orbz`, `orbv`, `orb-voice`, `Orb Voice`, and `neongate.com.br` outside
  historical SPECs and ADRs found only the audit's intentionally split guard strings.
- Not verified: rendering on GitHub or npm, light and dark site themes, and the `npx` option
  forwarding (the CLI was run directly, not through `npx`).

## Related records

- ADRs: [ADR-0025](../adrs/0025-orbo-product-identity.adr.md).
- Rules: [Rule 001](../rules/001-package-contract.rule.md), [Rule 011](../rules/011-public-orb-installer.rule.md).
- SPECs: [SPEC-034](034-orbo-product-identity.spec.md), [SPEC-036](036-accept-node-24-or-newer.spec.md).

## Compatibility and risks

Documentation and an image only; no public API change. The banner is derived from the
previous image (the prior version stays in Git history), so the sphere art is unchanged.
The README no longer links to a dedicated documentation site; if one is published later,
the homepage, README link, and both audits must change together. The new banner is
rendered with Avenir Next, so it should be regenerated from a font-compatible source if
the wordmark is revised again. The GitHub social-preview image may still show the old
wordmark until it is updated by hand in repository settings.
