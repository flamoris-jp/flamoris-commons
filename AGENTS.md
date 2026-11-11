# AGENTS.md

This repository contains shared foundations for the FLAMORIS ecosystem.

AI agents and human contributors should treat this repository as infrastructure, not as a dumping ground for convenience abstractions.

## Core principles

1. **Keep Commons small**
   - Only move code here when it is genuinely shared across multiple FLAMORIS applications or clearly belongs to ecosystem-wide infrastructure.
   - Do not extract code merely because two implementations look similar.

2. **Preserve application authority**
   - FLAMORIS 2D, Cutwork, Kachinco, Studio, and other applications remain authoritative for their own domain state and product behavior.
   - Shared libraries must not silently create a second source of truth.

3. **Prefer explicit boundaries**
   - Keep transport, logging, diagnostics, security, identity, and reusable primitives separate from application-specific commands, tools, and UI.
   - Avoid circular dependencies between shared packages and applications.

4. **Stable behavior over clever abstraction**
   - Prefer simple, testable contracts over speculative frameworks.
   - Do not generalize future requirements that have not yet appeared in real implementations.

5. **Security and privacy are part of architecture**
   - Secrets, credentials, local transport permissions, diagnostic data, and personal information must be handled deliberately.
   - Never log secrets, tokens, private keys, or sensitive user data.
   - Prefer least-privilege access and bounded resource use.

6. **AI-native, human-authoritative**
   - AI-assisted development is welcome.
   - Humans remain responsible for reviewing behavior, security, licensing, and compatibility.
   - Agents should inspect repository documentation, tests, issues, and current code before proposing large changes.

## Change workflow

Before implementing a substantial change:

- read this file;
- read README.md, CONTRIBUTING.md, SECURITY.md, and relevant docs;
- inspect current implementation and tests;
- check whether the proposed abstraction already exists elsewhere in FLAMORIS;
- identify the dependency direction before moving code.

For cross-application changes, prefer an Issue that records:

- the shared problem;
- current duplicate implementations;
- proposed boundary;
- migration plan;
- compatibility risks.

## Testing

Changes should include tests where practical.

Shared infrastructure should favor deterministic tests and explicit contracts.

When behavior differs by platform or runtime, document the supported environment and add targeted tests rather than relying on assumptions.

## Licensing

Unless stated otherwise, code in this repository is licensed under Apache License 2.0.

Do not add third-party code, models, model weights, datasets, fonts, media, or generated assets unless their licenses are compatible and clearly documented.

## Support

FLAMORIS does not provide guaranteed individual support.

When diagnosing problems, use the repository, documentation, tests, logs, and source code as the primary source of truth. AI-assisted self-support is encouraged.

## Audio/video storage policy

Do not commit audio or video files (including MP3, MP4, WAV, FLAC, M4A, OGG, MOV, WebM and equivalents), regardless of size, ownership, license, or whether they are generated demos or test samples. This also prohibits Git LFS pointers, renamed/encoded media and media hidden inside archives. Do not bypass the rule with `git add -f` or ignore exceptions.

Store media outside Git in approved artifact/media storage. Commit references, SHA-256 digests, provenance, analysis results, scripts and small text/symbolic fixtures instead. Generate temporary synthetic media during tests when needed. Licensing review does not waive this storage policy. MIDI/project data and images retain their existing repository-specific rules.

The `.gitignore` covers common audio/video extensions case-insensitively. Where installed, the `Media policy / No tracked audio or video` PR check also detects force-added files and LFS pointers by filename; it is not a content/archives scanner or a server-side push restriction. Do not rewrite history or remove external assets as part of this rule. See the [shared policy](https://github.com/flamoris-jp/flamoris-commons/blob/main/docs/repository-policy.md#121-audio-and-video-storage).
