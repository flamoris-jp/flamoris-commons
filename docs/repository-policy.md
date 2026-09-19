# FLAMORIS Repository Policy

This document defines the baseline repository policy for FLAMORIS projects.

It is intended to keep repositories consistent without forcing every project into the same implementation details.

## 1. Repository purpose

Each repository should have one clear responsibility.

Examples:

- application repository;
- shared library;
- infrastructure service;
- research or experimental project;
- organization configuration.

Avoid combining unrelated responsibilities only to reduce repository count.

## 2. Naming

Repository names should use lowercase kebab-case and normally begin with `flamoris-`.

Examples:

- `flamoris-2D`
- `flamoris-cutwork`
- `flamoris-kachinco`
- `flamoris-commons`
- `flamoris-mcp-hub`

Public product names may use normal branding and capitalization.

.NET assemblies and packages should prefer the `Flamoris.*` namespace family.

## 3. Default branch and changes

The default branch should be `main`.

Substantial changes should normally be developed through a branch and Pull Request.

Direct changes to `main` should be reserved for small administrative changes when appropriate.

Force-pushes to protected shared branches should be avoided.

## 4. Issues

Use Issues for:

- bugs;
- implementation work;
- architecture decisions that need discussion;
- cross-repository changes;
- production QA findings.

Small maintenance changes do not always require a dedicated Issue.

Issue descriptions should state the actual problem and desired outcome rather than prescribing unnecessary implementation detail.

## 5. Pull Requests

Pull Requests should:

- stay focused;
- explain intent and impact;
- include relevant tests;
- avoid unrelated cleanup;
- preserve compatibility unless intentionally changed;
- avoid automatic merge when explicit review is requested.

## 6. Documentation

Each production repository should normally include:

- `README.md`
- `LICENSE`
- `CONTRIBUTING.md`
- `SECURITY.md`
- `AGENTS.md` when AI-assisted development is expected

Additional architecture documents should live under `docs/`.

## 7. Licensing

FLAMORIS code is generally released under the Apache License 2.0 unless a repository states otherwise.

Non-code assets are not automatically covered by the code license.

AI models, weights, datasets, images, audio, video, fonts, and third-party assets must carry their applicable licenses or references separately.

Commercial use of Apache-2.0 licensed FLAMORIS code does not require permission.

If someone uses FLAMORIS in a commercial work or product, we would be happy to hear what they made, but this is completely optional.

## 8. Support philosophy

FLAMORIS software is provided as-is.

There is no guaranteed individual support or response-time commitment.

Users are encouraged to use:

- README files;
- documentation;
- Issues;
- tests;
- source code;
- diagnostics and logs;
- their own AI assistant

for self-support.

## 9. Security

Do not commit or publish:

- API keys;
- access tokens;
- private keys;
- passwords;
- production secrets;
- sensitive personal information.

Security-sensitive reports should use private vulnerability reporting when available.

Logging and diagnostics must redact secrets and avoid collecting unnecessary personal data.

## 10. Dependencies

Add dependencies deliberately.

Prefer:

- stable and maintained libraries;
- explicit licenses;
- minimal transitive dependency weight;
- packages with clear long-term ownership.

Shared packages must not introduce application-specific dependencies.

## 11. Shared-code extraction

Code should move into `flamoris-commons` or another shared repository only when:

- the need has appeared in more than one real application; or
- the component is clearly ecosystem infrastructure by nature.

Prefer proven duplication over premature abstraction.

A shared component should have:

- a clear public contract;
- a defined dependency direction;
- tests;
- versioning expectations;
- migration notes when replacing application-local implementations.

## 12. Generated files and artifacts

Do not commit build outputs, local caches, secrets, machine-specific state, or large generated artifacts unless the repository explicitly requires them.

GitHub Actions artifacts should use short retention periods unless there is a documented reason to keep them longer.

## 13. AI-assisted development

AI tools may be used for implementation, review, testing, documentation, and support.

AI-generated changes must still be reviewed for:

- correctness;
- security;
- licensing;
- architecture;
- performance;
- compatibility.

The repository remains the source of truth, not prior chat context.

## 14. Public release readiness

Before making a repository public, review at minimum:

- README and project description;
- license;
- third-party licenses;
- secrets and history;
- personal information;
- issue and PR content;
- generated artifacts;
- CI permissions;
- security configuration.

The goal is a repository that can be understood, built, and evaluated by someone who was not part of the original development process.
