# MILESTONES — Anubis-MCP 実行用マイルストーン

> 長期計画の正本は [ORDER.md](../ORDER.md)。本ファイルはその**実行用の分解**であり、状態・証拠の管理位置である。
> 内容の正（目的・優先順・出口条件）は ORDER.md を参照し、重複管理しない。既存構想文書
> `final-open-source-plan.md` の採否は MS-003 で扱い、原文は改変しない。

## MS-001: 検証ベースラインの確立（npm test 緑化）
Outcome: 依存導入から `npm test` 緑までがこのリポジトリで再現可能になり、手順が残課題欄・完了報告に記録される。
Acceptance: `npm ci`（または `npm install`）→ `npx prisma generate` → `npm test` が exit 0 で完了し、テスト件数が出力に含まれる。失敗がある場合は落ちる 1 件の修正を含めて緑化する（LOOP.md 優先順 1 と同一規律）。
Evidence: pending
Status: planned
依存: なし（最初の項目）
次の作業: 緑化を確認したら MS-002 に着手可能。2026-10-26 停止時評価（[ORDER.md](../ORDER.md)）により、前回 bounded-improvement run（run-6512b52c16f8・理由 unknown）の後の再開項目として本項が指定されている。

## MS-002: step-query.service.ts のリポジトリパターン TODO 解消
Outcome: `src/domains/workflow-rules/services/step-query.service.ts` のリポジトリ利用 TODO が解消され、既存 MCP ツール応答の互換性がテストで担保される。
Acceptance: 該当ファイル内の TODO コメントが消え、`npm test` が exit 0（MS-001 のベースラインと同等以上の合格件数）。
Evidence: pending
Status: planned
依存: MS-001（テストベースライン）
次の作業: 解消後、`grep -rn "TODO\|FIXME" src/` で残負債を再走査し、次の 1 件を LOOP.md の候補順に従って処理する。

## MS-003: レガシー構想（ローカル RAG）の採否決定
Outcome: `docs/files/final-open-source-plan.md` のローカルインテリジェンス構想について、実装・保留・削除のいずれかが証拠つきで決定され、ORDER.md の計画に反映される。
Acceptance: ORDER.md に採否判断と根拠（実装証拠またはオーナー判断）が記載され、判断に応じて本ファイルの該当項目参照が整合する。原文は改変しない。
Evidence: pending
Status: planned
依存: MS-001
次の作業: 採用なら段階的実装計画を ORDER.md に追記、非採用なら構想文書への判断注記を次回の許可された変更で行う。
