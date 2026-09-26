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

この文書は、FLAMORISのWindows / Desktop系アプリと共通.NET基盤の**横断地図**です。

ここでは変化しにくい責任範囲、authority、依存方向、アプリ間の受け渡しを整理します。細かな実装状況、release gate、未解決Issue、直近roadmapは各Repositoryを正とします。

## ひと目で見る制作フロー

```text
イラスト
  |
  v
FLAMORIS Cutwork
画像の切り分け / 修復 / パーツ準備
  |
  | .flimg
  v
FLAMORIS 2D
Mesh / Rig / Deform / Animation / shot export
  |
  | rendered media
  v
FLAMORIS Kachinco
timeline編集 / compositing / 最終映像の組み立て
```

これは便利な制作フローであって、必須のruntime依存関係ではありません。各アプリは単独でも利用でき、それぞれが自分のDocument / Project / 編集挙動のauthorityを持ちます。

## 🎨 制作アプリ

### FLAMORIS Cutwork

Repository: [flamoris-jp/flamoris-cutwork](https://github.com/flamoris-jp/flamoris-cutwork)

Cutworkは、アニメーションに使うイラスト素材を準備するアプリです。

主な責任範囲:

- 動かしたいパーツの切り分け
- Mask / repair workflow
- 隠れた領域の露出・再構築
- Parts / Layersの整理
- 下流で使うFLAMORIS image project formatの書き出し

現在の大きな構成:

- C# / .NET 10 / WPFがproduction implementation
- 現在のwriterは `.flimg` v2、v1 read/migrationも維持
- self-contained Windows packagingあり
- Live MCPは共通MCP Coreを使うが、Undo/RedoのauthorityはCutwork本体に残る

詳細な実装状況とacceptanceはCutwork側のREADME、roadmap、ADR、Issueを正とします。

### FLAMORIS 2D

Repository: [flamoris-jp/flamoris-2D](https://github.com/flamoris-jp/flamoris-2D)

FLAMORIS 2Dは、レイヤー素材から短いアニメーションshotを作るアプリです。

主な責任範囲:

- PSD / Cutwork `.flimg` import
- part配置
- mesh authoring
- bone / warp / skinning / deformation
- Key Arts / transition
- reusable AnimationClip / Sequence authoring
- preview / shot export

現在の大きな構成:

- Native .NET 10 / WPFの Source → Mesh → Rig → Deform → Animation → Preview → Export はproduction candidate
- Native editorは既存Product Host / EditorSessionをauthorityとして使い、別のproject modelを作らない
- Native Live MCPは共通MCP Core transportを使い、編集は同じapplication historyへ入る
- Native cutover acceptanceが完了するまではElectronがdefault installed/released shell

詳細なmigration evidenceとrelease gateは2D側を正とします。

### FLAMORIS Kachinco

Repository: [flamoris-jp/flamoris-kachinco](https://github.com/flamoris-jp/flamoris-kachinco)

Kachincoは、映像を組み立てるWindows-firstのtimeline editor / programmable compositorです。

主な責任範囲:

- video / audio media import
- timeline editing / playback
- compositing / caption
- 通常のproject操作を使う再現可能なAI-assisted editing
- Recipeベースのprogrammable rendering
- export / final assembly

現在のRepositoryはC#ベースで、初期media scopeは意図的に限定しています。MCPはfirst-class editing surfaceですが、AI専用の別timelineを持たず、アプリ本体のproject/history authorityを共有します。

詳細な実装状況はKachinco側のREADME、architecture docs、Issue、PRを正とします。

## 🧱 共通.NET基盤

Desktopアプリは共通インフラを使いますが、application-domain authorityを共通libraryへ移しません。

```text
FLAMORIS 2D -----------+
FLAMORIS Cutwork ------+----> Flamoris.Mcp.Core ----> Flamoris.Logging
FLAMORIS Kachinco -----+
          |
          +-------------------------------> Flamoris.Logging
```

実際のPackageReference graphは将来変わる可能性があります。重要なのは、**shared libraryはinfraを担当し、各applicationがdomain stateとediting semanticsを持つ**という不変条件です。

### Flamoris.Logging

Repository: [flamoris-jp/flamoris-logging](https://github.com/flamoris-jp/flamoris-logging)

`Flamoris.Logging` は共通のstructured logging / diagnostics基盤です。

Application event、Document、editor state、MCP behaviorのauthorityにはなりません。

現在のpublic package:

```xml
<PackageReference Include="Flamoris.Logging" Version="1.0.0" />
```

nuget.orgから公開され、FLAMORIS固有のGitHub Packages認証なしでrestoreできます。

### Flamoris.Mcp.Core

Repository: [flamoris-jp/flamoris-mcp-core](https://github.com/flamoris-jp/flamoris-mcp-core)

`Flamoris.Mcp.Core` はMCP transport、permission / capability、revocation、session attach、request bounds、revision/conflictなど、UI-neutralな共通インフラを提供します。

中心となる不変条件:

> **MCPはhost applicationのauthoritative sessionへのadapterであり、第二のeditorではない。**

現在のpublic package:

```xml
<PackageReference Include="Flamoris.Mcp.Core" Version="1.1.0" />
```

こちらもnuget.orgから公開され、FLAMORIS固有のGitHub Packages認証なしでrestoreできます。

## Authority / dependency rules

Desktop familyでは次のルールを守ります。

1. **各applicationが自分のDocument / Project stateを持つ。**  
   Cutwork、2D、KachincoはCommons、Logging、MCP Core、AI clientへapplication authorityを委譲しません。

2. **各applicationが自分のhistoryを持つ。**  
   UIとMCPのoperationは通常のmutation / Undo / Redo経路へ合流します。MCP専用の並行historyは作りません。

3. **MCP Coreはinfraを持ち、editing semanticsは持たない。**  
   Application-specific Command、Query、tool、schema、file operation、domain validationは各application側です。

4. **Loggingは観測するが制御しない。**  
   Logging failureがapplication-state failureや別source of truthにならないようにします。

5. **Shared packageは小さく保つ。**  
   実際に複数applicationで共通境界が現れてからshared packageへ切り出します。

6. **AIは明示的なcapabilityのclient。**  
   AI-assisted editingも人間から見える通常のproduct operationを通り、同じauthorityを使います。

## 🔄 Interoperability / 受け渡し

Desktopアプリは内部project modelを共有するのではなく、明示的なartifactまたはbounded capabilityで接続します。

現在もっとも明確なproduction handoffは:

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

Cutworkと2Dはreview済みの `.flimg` interchange contractでつながります。Kachincoは2Dの内部project stateを所有せず、rendered mediaを受け取ります。

将来の統合も同じ原則で、他applicationの内部authorityへ直接入り込まず、明示的なartifactまたはcapabilityを交換します。

## 📦 Build / package distribution

共通.NET packageはnuget.orgで公開します。

- `Flamoris.Logging 1.0.0`
- `Flamoris.Mcp.Core 1.1.0`

Desktop repositoryはGitHub PackagesのPATやpackage-read用 `GITHUB_TOKEN`、FLAMORIS専用authenticated NuGet feedなしでrestoreできることを前提にします。

各application固有の正確なbuild / test / packaging / release手順は、そのapplication自身のRepositoryを正とします。

## 🧭 Current-status discipline / ステータス情報の置き場所

この文書は**地図**であって、第二のroadmapではありません。

ここに置くもの:

- Repository responsibility
- dependency direction
- 共通architecture invariant
- cross-application production flow

ここを正にしないもの:

- exact feature completion
- open bug
- release readiness
- test count
- implementation-specific command
- near-term roadmap ordering

それらは各RepositoryのREADME、Issue、PR、ADR、status documentを正とします。

## 🤖 FLAMORIS AI ecosystemとの関係

Desktop familyは、明示的なMCP / application boundaryを通して、より広いFLAMORIS AI ecosystemと接続できます。

AI serviceはDesktop product authorityとは別です。

- 組織全体の入口: [flamoris-jp/.github](https://github.com/flamoris-jp/.github)
- AI側のRepository map / cross-service architecture: [flamoris-jp/flamoris-ai](https://github.com/flamoris-jp/flamoris-ai)

つまり、Desktop側は「制作物を持つアプリ」、AI側は「明示されたcapabilityを使うclient / service」として境界を保ちます。🌱
