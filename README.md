# FLAMORIS Commons

Shared foundations, libraries, and architecture for the FLAMORIS ecosystem.

FLAMORIS Commons is the home for shared engineering foundations used across FLAMORIS applications and tools.
The repository starts intentionally small: common policies and project foundations come first, while reusable code and packages will be introduced only when their boundaries are clear.

## Philosophy

FLAMORIS is open-source software for creative work and AI-native production.

Use it however you like.

Commercial use is welcome and does not require permission.  
If you'd like, we'd be happy to hear what you used FLAMORIS for.  
This is completely optional.

FLAMORIS software is provided as-is.
We do not provide individual support or guaranteed assistance.

If you run into trouble, we encourage you to let your AI assistant read the repository, documentation, issues, and source code and help you solve it.

If FLAMORIS helps you or you find it interesting,
your support helps fund development and keeps the project growing. 🌱  
<sub>Mostly GPU bills.</sub>

---

## 方針

FLAMORISは、クリエイティブ制作とAIネイティブな制作環境のためのオープンソースソフトウェアです。

勝手に使ってください。  
改造しても、組み込んでも、面白いものや変なものを作ってもOKです。

商用作品や製品で使う場合も、許可は不要です。  
もしよければ「こんなのに使ったよ」と教えてもらえるとうれしいです。  
もちろん強制ではありません。

FLAMORISのソフトウェアは現状のまま提供されます。  
個別サポートや動作保証はありません。

困ったときは、README、ドキュメント、Issue、ソースコードをあなたのAIに読ませて、自己サポートしてもらってください。

もし、あなたのお役に立てたり、面白いと思っていただけたなら、  
開発費用をご支援いただけるとうれしいです。  
FLAMORISは元気になって育ちます。🌱  
<sub>主にGPU代とか。</sub>

## License

Code in this repository is licensed under the [Apache License 2.0](LICENSE), unless otherwise noted.

AI models, model weights, datasets, media, and other non-code assets may use separate licenses. Their applicable licenses must be stated alongside those assets.


## Desktop ecosystem

The Windows-oriented FLAMORIS creative applications are documented together in the [Desktop Ecosystem map](docs/desktop-ecosystem.md).

At a high level:

```text
Cutwork  ->  FLAMORIS 2D  ->  Kachinco
   \              |               /
    \             |              /
     +------ shared foundations --+
            Logging / MCP Core
```

The map explains application responsibility, production handoffs, shared .NET dependencies, and the rule that each application remains authoritative for its own document/project state and Undo/Redo history.

Implementation details and fast-changing status remain in each application's own repository.

---

## Windows screen observation / Windows画面観測

[**FLAMORIS Capture**](https://github.com/flamoris-jp/flamoris-capture) is an implemented Windows screen-observation tool: a reusable DLL, standalone EXE and MCP adapter over one approved window. It supports screenshots, bounded human-controlled recording, selected frames/contact sheets and opt-in target-window operation traces. The basic Windows/Hub workflow is confirmed; broader lifecycle/platform acceptance and release remain separate.

Cutwork and 2D reuse the screenshot-only embedded WPF component: the implemented application-lifetime policy gives observation a separate explicit sharing checkbox; MCP attachment alone does not authorize pixels (see the [coordinated lifecycle contract](docs/desktop-mcp-lifecycle.md)). Standalone Capture keeps its separate sharing/operation controls. Region/monitor capture, audio and input replay are outside the current implementation.

Capture observes actual UI, including third-party applications, alongside the `Cutwork → 2D → Kachinco` media pipeline. It leaves project/history ownership with each editor, MCP infrastructure with [MCP Core](https://github.com/flamoris-jp/flamoris-mcp-core), and server monitoring with [Observer](https://github.com/flamoris-jp/flamoris-observer). Current scope and acceptance: [Capture README](https://github.com/flamoris-jp/flamoris-capture) and [Windows acceptance](https://github.com/flamoris-jp/flamoris-capture/blob/main/docs/WINDOWS-ACCEPTANCE.md).

**日本語:** DLL＋独立EXE＋MCPの実装があり、許可した1ウィンドウの静止画・録画・選択フレーム・操作記録を扱います。Cutwork・2Dの自アプリ画面観測は、[共通ライフサイクル契約](docs/desktop-mcp-lifecycle.md)に従いMCP接続と画面共有許可を分離する実装が完了しています。基本動作の実機確認と、未確認の失効・保存・環境別受入は分けて記録し、stable判定・正式バイナリ配布も別に扱います。矩形・モニター撮影は将来機能です。

## Related repositories

- [FLAMORIS 2D](https://github.com/flamoris-jp/flamoris-2D) — 2D animation and character authoring
- [FLAMORIS Cutwork](https://github.com/flamoris-jp/flamoris-cutwork) — image decomposition, repair, and part preparation
- [FLAMORIS Kachinco](https://github.com/flamoris-jp/flamoris-kachinco) — AI-native video editing and compositing
- [FLAMORIS Capture](https://github.com/flamoris-jp/flamoris-capture) — Windows approved-window screenshots, bounded recording and MCP observation
- [FLAMORIS Logging](https://github.com/flamoris-jp/flamoris-logging) — shared structured logging and diagnostics foundation
- [FLAMORIS MCP Core](https://github.com/flamoris-jp/flamoris-mcp-core) — shared MCP infrastructure for FLAMORIS applications and tools
- [FLAMORIS organization configuration](https://github.com/flamoris-jp/.github) — shared GitHub profile and community health files

Shared infrastructure is split into dedicated repositories when its boundary is clear enough to stand on its own.
