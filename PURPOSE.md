# PURPOSE — Anubis-MCP

<!-- BEGIN: GENERATED -->

計画確認日: 2026-10-24。基準: `13d44028c1cd0f11aa5c90d0b1dec0a611f9fb69`。

## 目的と変更してはいけない条件

- **目的:** AIエージェントが MCP 対応開発環境で、役割・手順・タスク状態に沿った開発を進められるよう、Anubis が構造化された workflow guidance と追跡機能を提供する。
- **利用者が得る成果:** プロジェクトの workflow を開始し、次の手順を取得し、進捗・完了結果を保存して確認できる。
- **成功条件:** 実際に公開する MCP 操作が有効な入力を受け、DB の状態と矛盾しない guidance / progress を返し、異常時も成功を偽らない。実行するのはホスト側の利用者・エージェントであり、Anubis の guidance は対象プロジェクトのコードを代わりに実行しない。
- **保護条件:** 既存の MCP tool 名・入力互換性、Prisma schema/migration と利用者 DB の保全、Node.js `>=18`、npm `>=8` を維持する。破壊的 DB 操作、秘密の読み出し、リリース・deploy・外部送信は別途明示的に許可されない限り行わない。
- **責務境界:** Anubis は MCP server、workflow/task 操作、ローカル DB の初期化・migration/seed を担う。MCP client、対象プロジェクトでのコード実行、NPM/Docker/GitHub Pages の公開基盤は外部担当。

## 全体構造と現在地

|実際の経路/責務|コード・契約|実装/受入/運転の状態|証拠と観測日|残る原因|
|---|---|---|---|---|
|導入・起動とローカルDB|`package.json`, `src/cli.ts`, `src/utils/database-config.ts`, `prisma/`|実装済み。Node/npm 要件と migration/seed 経路をコードで確認。クリーン環境および既存利用者DBでの現行運転受入は未確認。|`package.json`; `src/cli.ts`; 2026-10-24|インストール後の起動・更新の再現可能な受入証拠がこの確認では得られていない。|
|MCP workflow guidance と進行|`src/domains/workflow-rules/mcp-operations/`, `services/`, `repositories/`|tool 群と repository 経路は実装済み。単体テストは一部 repository/service に存在。client 互換の live 受入は未確認。|`src/domains/workflow-rules/workflow-rules.module.ts`; `src/domains/workflow-rules/mcp-operations/`; `src/domains/workflow-rules/services/step-query.service.ts`; 2026-10-24|`getRoleStepStatistics` と `getStepExecutionHistory` はゼロ値/空配列の仮実装のまま。|
|タスク・subtask・review/research 操作|`src/domains/task-management/`|service/repository 実装済み。全 MCP 入出力を通す現在の受入範囲は未確認。|`src/domains/task-management/task-management.module.ts`; 2026-10-24|利用者視点の境界ケースを含む受入証拠の不足。|
|agent rules 配布|`src/domains/init-rules/`|`init_rules` とテンプレートが実装済み。対応 agent への現行受入は未確認。|`src/domains/init-rules/mcp-operations/init-rules-mcp.service.ts`; 2026-10-24|client ごとの生成結果・再実行安全性の証拠不足。|
|公開・配布|`.github/workflows/`, `AUTOMATION.md`|Pages/NPM/Docker/release workflow が定義済み。現行稼働・公開結果はこの作業で確認していない。|`AUTOMATION.md` (2026-09-15 の観測); `.github/workflows/`; 2026-10-24|古い運転観測と現在の workflow 実行証拠の差。公開操作は今回の計画範囲外。|

## 以前の計画から変える判断

|以前の前提・順序|最新の事実|判断|理由・影響先|
|---|---|---|---|
|memory-bank が記す v1.2.11 の達成数・品質数値を現況として扱う|対象 package は v1.2.20。現在のコードには未実装の step progress query がある。memory-bank 文書は v1.2.11 表記。|変更|古い成功記録は過去の観測のまま保持し、現在の能力判定にはコードと現行テストを使う。|
|Repository Pattern 完了後に広範な最適化・機能拡張を並べる|`step-query.service.ts` に具体的な TODO と空結果の実装が2件ある。|優先を変更|既存実装の再移行や根拠の薄い性能作業より、利用可能な repository を使ったこの境界の正しさを先に受け入れる。|
|ダッシュボードの作業を新たに起票する|GitHub PR #56 `websocket-live-dashboard` が OPEN。古い更新日時のため最新の有効性は未確認。|後回し|同じ画面/APIを重複実装しない。実行時にPRと最新 main を再確認してから着手可否を決める。|

## 到達状態から逆算した段階

|段階|利用者が得る成果|必要な証拠|未達原因・依存|次の実行波に進む条件|
|---|---|---|---|---|
|1. Workflow progress の真実性|role/step の進捗照会が保存済み記録に一致する。|status 別の期待値を持つ unit test と対象 Jest suite の成功。|2つの StepQueryService placeholder。|`docs/MILESTONES.md` の MS-001 が各 AC と配達 gate を満たす。|
|2. MCP 利用者受入|実際の MCP client が主要 workflow を開始し、状態を進め、結果を照会できる。|公開 tool schema、成功/失敗応答、永続化を一連で確認した独立 acceptance。|現在のテスト・live client evidence がこの計画基準で未確認。|MS-001 完了後、対象 client / 実行環境を特定する独立 acceptance 範囲と権限が確定する。|
|3. 配布・更新信頼性|新規導入・既存利用者の更新がデータを保全し、配布物を再現できる。|clean install と既存DB更新の結果、実際の配布 workflow/run 記録。|今回、実環境のDB更新・公開 run は実施も確認もしていない。|第2段階の利用者経路と対応 release 条件が確定してから必要部分だけを計画する。|

現在の実行波は [docs/MILESTONES.md](docs/MILESTONES.md) に定義する。約30分は標準作業枠であり、外部待ち・修正・暦時間の保証ではない。遠い段階の実装詳細は入力と必要性が確定した時に分割する。

<!-- END: GENERATED -->
<!-- BEGIN: HUMAN -->
<!-- END: HUMAN -->
