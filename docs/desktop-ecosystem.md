# FLAMORIS Desktop Ecosystem

This document is the cross-repository map for the Windows-oriented FLAMORIS creative applications and their shared .NET foundations.

It intentionally stays at the ecosystem level. Fast-changing feature status, implementation details, release gates, and product roadmaps remain authoritative in each application's own repository.

## At a glance

```text
Artwork
   |
   v
FLAMORIS Cutwork
image decomposition / repair / part preparation
   |
   | .flimg
   v
FLAMORIS 2D
mesh / rig / deformation / animation / shot export
   |
   | rendered media
   v
FLAMORIS Kachinco
timeline editing / compositing / final video assembly
```

This is a useful production path, not a mandatory runtime dependency chain. Each application remains independently usable and authoritative for its own documents and editing behavior.

## Applications

### FLAMORIS Cutwork

Repository: [flamoris-jp/flamoris-cutwork](https://github.com/flamoris-jp/flamoris-cutwork)

Cutwork prepares illustration material for animation.

Its responsibility includes:

- isolating parts that need to move;
- mask and repair workflows;
- exposing or rebuilding hidden regions;
- Parts / Layers organization;
- writing the FLAMORIS image project format used downstream.

Current high-level status:

- C# / .NET 10 / WPF is the primary production implementation;
- the current writer is `.flimg` v2, with v1 reading/migration support;
- self-contained Windows packaging exists;
- Live MCP uses the shared MCP Core boundary and the application's own Undo/Redo authority.

Detailed status and acceptance remain in the Cutwork README, roadmap, ADRs, and Issues.

### FLAMORIS 2D

Repository: [flamoris-jp/flamoris-2D](https://github.com/flamoris-jp/flamoris-2D)

FLAMORIS 2D turns layered artwork into short animated shots.

Its responsibility includes:

- PSD and Cutwork `.flimg` import;
- part arrangement;
- mesh authoring;
- bones, warp, skinning, and deformation;
- Key Arts and transitions;
- reusable animation clips and sequence authoring;
- preview and shot export.

Current high-level status:

- the Native .NET 10 / WPF Source -> Mesh -> Rig -> Deform -> Animation -> Preview -> Export workflow is a production candidate;
- the Native editor uses the existing Product Host / EditorSession authority rather than creating a second project model;
- Native Live MCP uses the shared MCP Core transport while edits remain in the same application history;
- Electron remains the default installed/released shell until Native cutover acceptance is complete.

Detailed migration evidence and release gates remain in the 2D repository.

### FLAMORIS Kachinco

Repository: [flamoris-jp/flamoris-kachinco](https://github.com/flamoris-jp/flamoris-kachinco)

Kachinco is the Windows-first timeline editor and programmable compositor for assembling and editing video.

Its responsibility includes:

- video/audio media import;
- timeline editing and playback;
- compositing and captions;
- reproducible AI-assisted editing through ordinary project operations;
- programmable Recipe-based rendering;
- export and final assembly.

The current repository is C#-based and supports the intentionally narrow initial media scope documented there, including MOV/MP4 video and WAV/MP3/M4A audio. MCP is a first-class editing surface and shares the application's project/history authority rather than maintaining a hidden AI timeline.

Detailed implementation status remains in the Kachinco README, architecture docs, Issues, and PRs.

## Shared foundations

The desktop applications share infrastructure without moving application-domain authority into common libraries.

```text
FLAMORIS 2D -----------+
FLAMORIS Cutwork ------+----> Flamoris.Mcp.Core ----> Flamoris.Logging
FLAMORIS Kachinco -----+
          |
          +-------------------------------> Flamoris.Logging
```

The exact PackageReference graph may evolve. The invariant is more important than the diagram: shared libraries provide infrastructure, while each application owns its domain state and editing semantics.

### Flamoris.Logging

Repository: [flamoris-jp/flamoris-logging](https://github.com/flamoris-jp/flamoris-logging)

`Flamoris.Logging` provides shared structured logging and diagnostics mechanics.

It does not own application events, documents, editor state, or MCP behavior.

Current public package:

```xml
<PackageReference Include="Flamoris.Logging" Version="1.0.0" />
```

The package is published on nuget.org and can be restored without FLAMORIS-specific GitHub Packages authentication.

### Flamoris.Mcp.Core

Repository: [flamoris-jp/flamoris-mcp-core](https://github.com/flamoris-jp/flamoris-mcp-core)

`Flamoris.Mcp.Core` provides reusable MCP transport, permission/capability, revocation, session attachment, request bounds, revision/conflict primitives, and related UI-neutral infrastructure.

Its core invariant is:

> MCP is an adapter over the host application's authoritative session, not a second editor.

Current public package:

```xml
<PackageReference Include="Flamoris.Mcp.Core" Version="1.1.0" />
```

The package is published on nuget.org and can be restored without FLAMORIS-specific GitHub Packages authentication.

## Authority and dependency rules

Across the desktop family:

1. **Each application owns its own document/project state.**
   Cutwork, 2D, and Kachinco do not delegate application authority to Commons, Logging, MCP Core, or an AI client.

2. **Each application owns its own history.**
   UI and MCP operations must converge on the application's ordinary mutation and Undo/Redo path. MCP must not create a parallel history.

3. **MCP Core owns infrastructure, not editing semantics.**
   Application-specific Commands, Queries, tools, schemas, file operations, and domain validation stay in the application.

4. **Logging observes rather than controls.**
   Logging failures must not become application-state failures or an alternative source of truth.

5. **Shared packages stay small.**
   Cross-application code belongs in a shared package only after a real common boundary has appeared.

6. **AI is a client of explicit capabilities.**
   AI-assisted editing must remain human-visible and use the same authoritative product operations as ordinary editing.

## Interoperability

The desktop applications are designed to meet at explicit file or capability boundaries rather than by sharing internal project models.

The clearest current production handoff is:

```text
Cutwork .flimg
      |
      v
FLAMORIS 2D
      |
      v
rendered shot media
      |
      v
Kachinco timeline
```

Cutwork and 2D therefore share a reviewed `.flimg` interchange contract, while Kachinco consumes rendered media rather than becoming an owner of 2D project state.

Future integrations should preserve the same principle: exchange explicit artifacts or bounded capabilities instead of reaching into another application's internal authority.

## Build and package distribution

The shared .NET packages are public on nuget.org:

- `Flamoris.Logging 1.0.0`
- `Flamoris.Mcp.Core 1.1.0`

The desktop repositories are expected to restore these packages without a GitHub Packages PAT, `GITHUB_TOKEN` package-read permission, or FLAMORIS-specific authenticated NuGet feed.

Each application repository owns its own exact build, test, packaging, and release instructions.

## Current-status discipline

This file is a map, not a second roadmap.

Use it for:

- repository responsibility;
- dependency direction;
- common architectural invariants;
- cross-application production flow.

Do not use it as the authority for:

- exact feature completion;
- open bugs;
- release readiness;
- test counts;
- implementation-specific commands;
- near-term roadmap ordering.

For those, follow the README, Issues, PRs, ADRs, and status documents in the owning repository.

## Related ecosystem

The desktop family can connect to the broader FLAMORIS AI ecosystem through explicit MCP and application boundaries. AI services remain separate from desktop product authority.

See [flamoris-jp/flamoris-ai](https://github.com/flamoris-jp/flamoris-ai) for the AI-facing repository map and cross-service architecture.

---

## 日本語

この文書は、FLAMORISのWindows / Desktop系アプリと共通基盤の「全体地図」です。

主な制作の流れは、

```text
イラスト
  ↓
Cutwork
  ↓ .flimg
FLAMORIS 2D
  ↓ 映像
Kachinco
  ↓
最終映像
```

です。ただし、これは便利な制作フローであって、各アプリが互いを必須依存にするという意味ではありません。

- **Cutwork** は素材の切り分け、マスク、修復、Parts / Layers、`.flimg` を担当します。
- **FLAMORIS 2D** はMesh、Rig、Deform、Animation、Preview、shot exportを担当します。
- **Kachinco** はtimeline編集、compositing、caption、AI-assisted editing、最終映像の組み立てを担当します。
- **Flamoris.Logging** は共通ログ・診断基盤です。
- **Flamoris.Mcp.Core** はMCP transport、permission、capability、revocation、live attachなどの共通基盤です。

重要なのは、**各アプリ自身がProject / Document / Undo / Redoのauthorityを持ち続ける**ことです。

MCPやAIは第二のeditorを作らず、各アプリの通常の編集経路を使います。Loggingも状態のauthorityにはなりません。

詳細な実装状況は各リポジトリを正とし、この文書には変化しにくい全体構造だけを置きます。
