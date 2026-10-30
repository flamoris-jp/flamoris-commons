# Desktop MCP lifecycle and independent consent

Tracking: [Commons #8](https://github.com/flamoris-jp/flamoris-commons/issues/8). This contract intentionally replaces the older policy where connecting an embedded Capture component also shared the owner window. Implementation candidates and CI are in the owning PRs; this document does not claim workstation deployment or physical acceptance.

| Application | Owning Issue / PR | Connection and target | Content permission |
| --- | --- | --- | --- |
| Capture | [#11](https://github.com/flamoris-jp/flamoris-capture/issues/11) / [#12](https://github.com/flamoris-jp/flamoris-capture/pull/12) | Connect before selecting a target; status queries work with all sharing OFF | Separate screen and operation controls; epochs bind pending/historical disclosure |
| Cutwork | [#64](https://github.com/flamoris-jp/flamoris-cutwork/issues/64) / [#65](https://github.com/flamoris-jp/flamoris-cutwork/pull/65) | Connect with no image; file changes keep the grant and change document tokens | Independent owner-window checkbox; AI save grants remain document-scoped |
| FLAMORIS 2D Native | [#164](https://github.com/flamoris-jp/flamoris-2D/issues/164) / [#165](https://github.com/flamoris-jp/flamoris-2D/pull/165) | Attach when the native application starts, before document creation/open | Independent owner-window checkbox; native workspace remains editing authority |
| Kachinco | [#120](https://github.com/flamoris-jp/flamoris-kachinco/issues/120) / [#121](https://github.com/flamoris-jp/flamoris-kachinco/pull/121) | Empty project, New/Open/Undo-to-empty keep the connection | Authorized project preview/export; uploads/downloads are reset on target replacement |
| KineticTypo | Kachinco #120 / #121 | Independent executable; startup blank composition does not require a saved file | Existing composition grant/guard; no implicit desktop observation |
| StudioClient: PONPON / AudioAnalyzer | Current AGENTS/README inspected | No MCP surface implemented; no connection lifecycle to migrate | No new remote surface introduced |

MCP Core/Wpf [#17](https://github.com/flamoris-jp/flamoris-mcp-core/pull/17) supplies the reusable opt-in application-lifetime grant and request/content validation. Product/session/document/history remain in each application. Existing document-scoped API callers retain their defaults.

## Independent states

- Connection: saved method/credentials, explicit Stop and shutdown. New settings auto-connect locally in read-only mode; previously saved OFF remains an opt-out. Configured providers honor their startup setting.
- Content consent: screen, recording and operation capabilities remain host-owned and independently revocable. Connecting/reconnecting never enables them. Withdrawal prevents new, pending and stored-content disclosure; it does not terminate the transport.
- Target: `mcp.context` reports `hasDocument`, runtime, current target token/revision and busy state. Document tools return `no_document` without turning absence into a connection failure. Reopen of the same file receives a fresh target token.

Every request pins the target at admission. Mutations require current runtime/document/revision guards; final query disclosure also checks the target and any bound host content consent. Old transfers and save/preview leases must not follow a replacement target. No automatic replay of ambiguous mutations is introduced.

## Coordinated rollout and acceptance

1. Review/test MCP Core #17, merge and publish Core/Wpf 1.4.0 using the existing release pipeline.
2. Review/test and merge Capture #12.
3. Advance the Cutwork/2D Capture gitlinks to the accepted Capture main commit (their review candidates currently pin the exact tested feature commit), then merge the application PRs after required CI. Kachinco is independent of Capture.
4. Consumer CI currently builds pinned MCP source into a temporary local feed, allowing pre-publication validation without copying source/DLLs into runtime. Remove those temporary CI steps when the coordinated package is on nuget.org and recheck ordinary package restores.
5. Perform physical Windows checks: startup/no file, connect/reconnect, sharing toggles, content withdrawal during work, file open/close/switch/same-file reopen, stale guards, shutdown. CI and real-user acceptance remain separate evidence.

Issue #8 remains open until the owning implementations, coordinated publication/update and physical acceptance are recorded.
