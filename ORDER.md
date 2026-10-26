# ORDER — Anubis-MCP 長期計画（正本）

> このファイルが長期計画の**内容の正本（原本）**である。`REPO_METADATA.json` はこの投影であり、
> 実行用の分解は `docs/MILESTONES.md` が担う。本ファイル更新時は MILESTONES.md の参照と
> REPO_METADATA.json の `source_hash` を同一変更で整合させること。
> 所有者指示 OI-20261025-11（2026-10-22）により新設。事業目的・権限・予算は本計画では変更しない。

Plan-Status: in_progress
Updated: 2026-10-26

## 既存目的との関係

- `PURPOSE.md` の Autonomous Intent（LOOP.md 由来）は**運用ループ**（1 run = 1 改善、テスト失敗 → TODO → docs 不整合の優先順）を定義する。本計画はそのループが積み上げる先の**長期到達点**を定義するものであり、ループの実行規約を置き換えない。
- `docs/files/final-open-source-plan.md` は過去に提案された「ローカル完結インテリジェンス構想」（Chroma / LlamaIndex / ローカル埋め込み、外部 API ゼロ）である。実装証拠のない提案文書として重複管理せず、採否を MS-003 で証拠つき判断する（履歴・根拠は原文に保持）。
- プロダクト目的（AI エージェント向けワークフロー誘導 MCP サーバー、npm `@hive-academy/anubis` / Docker 公開）は README.md のとおり継続。

## 到達点（2026-10-25 時点の実装証拠）

- npm パッケージ `@hive-academy/anubis` v1.2.20・Docker イメージ `hiveacademy/anubis` として公開済み（`package.json`、`README.md`）。
- 3 ドメインが実装済み: `src/domains/workflow-rules/`、`src/domains/task-management/`、`src/domains/init-rules/`。
- リポジトリパターン移行が workflow-rules 配下で部分適用（`repositories/`、`docs/files/repository-pattern-implementation.md`）。ただし `step-query.service.ts` にリポジトリ利用への TODO が残存（451–473 行付近）。

## 現状との差（ギャップ）

- テストベースラインが未確立: この作業環境では `node_modules` 未導入で `npm test` の直近結果が記録されておらず、改善の前提となる緑の基準がない。
- レガシー構想（ローカル RAG）と現実装の差が大きく、採用・保留・削除が未決。
- ソース内に既知負債 TODO が残存（上記 step-query.service.ts）。

## 優先順

1. **検証可能性の基盤** — `npm test` の緑ベースライン確立と維持（MS-001）。全改善の前提。
2. **既知負債の解消** — リポジトリパターン TODO の回収（MS-002）。
3. **計画の整理** — レガシー構想の採否決定と計画文書の整合（MS-003）。

## 依存

- MS-002・MS-003 は MS-001（テストベースライン）に依存する。
- 依存導入後は `npx prisma generate` が必要（fleet の `.npmrc` が `ignore-scripts=true` のため postinstall が走らない）。Prisma スキーマ変更時も同様。

## 出口条件

- 優先順 1〜3 の全マイルストーンが `docs/MILESTONES.md` で `achieved`（受入条件に照らした証拠つき）となり、`npm test` が緑で維持されていること。
- 全項目達成後は `PURPOSE.md` の運用ループ内で次の増分（例: docs と実装の手順不一致解消）を検討し、目的全体の達成も確認できたら本計画に `Plan-Status: achieved` を記す。

## 次の一手

- `docs/MILESTONES.md` の **MS-001（検証ベースライン確立）** を実行する。約 30 分で検証可能な具体作業に分解済み。着手は次回以降の run が担う。

## 停止時評価（2026-10-26・OI-20261026-53）

- **停止の事実**: 最終実行は `bounded-improvement`（run-6512b52c16f8）で `status: failed`。制御平面の観測データ上の理由は `unknown`（実行指示ではなく観測値）。
- **リポジトリ内証拠の調査結果（2026-10-26）**: 本リポジトリに当該 run の commit・成果物・参照は存在しない（`git log --all` と全文 grep で不検出）。`node_modules` 未導入で MS-001 の検証ベースラインは未確立のまま。LOOP.md の運用規約上、bounded improvement run の検証は `npm test` を完了条件とするため、ベースライン未確立が失敗の構造的背景として示せるが、**直接の停止原因は特定できず `unknown` として記録する**。
- **長期目的と現状の差**: 「到達点（2026-10-25）」のとおり公開・3ドメイン実装済み。MS-001〜MS-003 はすべて未着手（planned）で、差の主要因は検証ベースライン（MS-001）の未確立。
- **未解決の依存**: MS-002・MS-003 は MS-001 に依存。MS-001 自体に外部依存はなく `npm ci` → `npx prisma generate` → `npm test` で着手可能。
- **再開条件**: 次の bounded run が MS-001 を約 30 分の具体作業として実行し、受入条件（`npm test` exit 0・テスト件数の出力）で検証する。本評価は停止解除・mode 変更・実装再開の権限を付与しない。
- **判断: 計画を維持する**。根拠: 本計画は 2026-10-25 新設であり、既存目的・優先順・出口条件は現状の実装証拠と矛盾しない。停止は計画の不備ではなく実行段階の未達（MS-001 未着手）で説明でき、改訂を要する新証拠は発見されなかった。未達のため次の約 30 分作業は MS-001（受入条件: `npm test` exit 0）、確認条件は MILESTONES.md の該当 Acceptance 行。
