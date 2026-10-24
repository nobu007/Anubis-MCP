# Anubis-MCP — マイルストーンと小作業

<!-- BEGIN: GENERATED -->

計画基準: 2026-10-24 / `13d44028c1cd0f11aa5c90d0b1dec0a611f9fb69`。
長期方針: [PURPOSE.md](../PURPOSE.md)。実装・受入正本: `src/domains/workflow-rules/services/step-query.service.ts`、`src/domains/workflow-rules/repositories/interfaces/step-progress.repository.interface.ts`、`src/domains/workflow-rules/repositories/implementations/step-progress.repository.ts`、`package.json` の Jest 設定。
専任ワーカー: [DOT.md](../DOT.md)。以下の状態は計画基準時点の観測であり、実行時に最新 main/PR/run から再確認する。

## 構造と実行する範囲

利用者・MCP client → MCP tool (`src/domains/workflow-rules/mcp-operations/`) → domain service (`services/`) → repository interface/implementation → Prisma/SQLite (`prisma/`) が workflow progress の経路。今回の実行波は `StepQueryService` の未実装 query 2件と、それを担保する unit test に限る。DB schema、公開 tool schema、UI、実行基盤、release workflow は変更しない。

この基準で `getRoleStepStatistics` は `RoleStepStatistics` 型のすべての数値を0に固定し、`getStepExecutionHistory` は常に空配列を返す。両者は `StepQueryService` 内部以外にコード上の呼出しを確認できなかった。公開API/外部consumerがないとは断定しない。

## マイルストーン

|ID|利用者が得る能力・成果|対象WP|完了ゲート|標準作業枠|
|---|---|---|---|---|
|MS-001|role と step の進捗照会が既存の保存記録を反映し、空の成功値を返さない。|WP-001 → WP-002|各事前ACに対応する失敗可能な単体テストが通り、変更ファイルのみを通常の許可済み経路で配達。|2 × 約30分|
|MS-002|選定した MCP client が主要 workflow を起動・進行・照会できる根拠を得る。|WP-003 (conditional)|対象 client と許可された acceptance 環境が特定され、状態遷移と異常応答を含む独立 acceptance evidence を保存。|入力確定後に見積り|

依存: `WP-001 → WP-002 → MS-001 → WP-003`。WP-001 の結果が `not_needed` なら WP-002 も `not_needed` とし、MS-001 は実装不要の根拠を示して閉じる。WP-003 は対象 client/権限未確定のため pending であり、実行対象や試験例を創作しない。並行作業は同一 `StepQueryService` を編集するWP間で行わない。

2枠×約30分は標準 active-time の単純和。CI・PR・外部待ち、追加修正、後続 acceptance は別で、暦時間保証や目的達成までの残工数ではない。

## 作業カード

### WP-001 | step ごとの実行履歴を永続化済み progress から返す（約30分）

- **主旨**: 現在の空配列 placeholder をなくし、step の履歴照会を実データへ接続する。
- **ゴール・成果物**: `StepQueryService.getStepExecutionHistory(stepId)` が指定 step の全 `WorkflowStepProgress` を返す実装と回帰 test。
- **入力・対象**: `src/domains/workflow-rules/services/step-query.service.ts`; `IStepProgressRepository.findByStepId`; `StepProgressRepository.findByStepId`; `src/domains/workflow-rules/repositories/implementations/step-progress.repository.spec.ts` または同ディレクトリの適切な spec。
- **作業**: 最新 `main`/PR と当該コードを再確認。progress repository を注入し、その既存 query を使う。既存型を保ち、保存 repository が返す最新開始順を維持する。
- **完了条件（事前AC）**: (1) non-empty step fixture の全記録を返すことを test が判定する、(2) `stepId` が repository へ渡る、(3) repository の結果順と内容を変更・捏造しない、(4) エラーを空配列の成功へ握りつぶさない。
- **必要検証・証拠**: `npm test -- --runInBand --runTestsByPath domains/workflow-rules/services/step-query.service.spec.ts` の実際の対象 test 結果（Jest `rootDir: src` に対するパス）。差分と commit/PR の実記録を使用する。
- **依存・着手条件**: MS-001; 読み取り時に未実装 placeholder が残っていること。既に正しく実装され、AC を満たす場合は `not_needed` とし、その commit/test evidence を記録する。
- **担当・変更範囲**: Dot。対象 service と必要最小の test/repository wiring のみ。PR #56 は dashboard に関する未着地作業として着手前に再確認し、step history の重複変更があれば waiting にする。
- **待ち・失敗・再開**: テスト失敗は実出力を保存して一度原因を変えて修正。依存・環境不足はその原因だけ waiting にし、次の独立 ready WP を探す。過去の成功証拠は同条件なら再利用。
- **条件付き不要の判定**: 最新実装が既に上記ACを満たし、同一条件の有効なテスト結果が確認できるときだけ not_needed。

### WP-002 | role の進捗統計を既存 summary へ接続する（約30分）

- **主旨**: role ごとの進捗が固定ゼロではなく repository の既存集計と一致するようにする。
- **ゴール・成果物**: `StepQueryService.getRoleStepStatistics(roleId)` の実データ実装と status 別回帰 test。
- **入力・対象**: `src/domains/workflow-rules/services/step-query.service.ts`; `IStepProgressRepository.getProgressSummary`; `StepProgressSummary` と `StepProgressRepository.getProgressSummary` の既存定義。
- **作業**: WP-001 配達後、最新コードと既存 summary の status 集計を確認し、repository summary を `RoleStepStatistics` へ明示的に写像する。
- **完了条件（事前AC）**: (1) roleId が repository へ渡る、(2) total/completed/failed/inProgress が fixture の期待値と一致、(3) 未知/ゼロデータを成功扱いして定義を改変しない、(4) repository error を 0 件の成功へ変換しない。
- **必要検証・証拠**: 同じ `step-query.service.spec.ts` の status ごとの mock data と、変更内容を含む最小の対象 test 実行結果。
- **依存・着手条件**: WP-001 が配達済み、または同じ service の変更競合が解消。最新 main に既にACを満たす実装があれば not_needed。
- **担当・変更範囲**: Dot。`StepQueryService` と必要最小の test のみ。DB query の新設・schema変更・統計定義の拡張は禁止。
- **待ち・失敗・再開**: summary の現行意味と公開consumerの期待が矛盾する場合、推測で意味を変えず当該WPを waiting にし、確認した契約差を残す。
- **条件付き不要の判定**: 現行実装と有効 test がすべてACを満たす場合のみ not_needed。

### WP-003 | 主要 MCP workflow の利用者受入境界を確定する（conditional / 入力確定まで pending）

- **主旨**: 実際の client、tool schema、DB state が連続する受入範囲を確定し、live 受入の有無を判定する。
- **ゴール・成果物**: 対象 client、許可環境、開始→進行→完了の具体的 tool/state 列、失敗時の期待値、利用可能な acceptance evidence を列挙した事前AC判定。新しい常設文書や台帳は作らない。
- **入力・対象**: `src/domains/workflow-rules/mcp-operations/`, `src/domains/task-management/`, `src/domains/init-rules/`, `package.json`, 既存 CI/PR/run records。
- **作業**: 実際の公開 tool と既存 test を対応付け、対象 client を既存利用者情報から特定できなければその欠落を明示する。認可された acceptance が可能な場合だけ後続実行候補を確定する。
- **完了条件（事前AC）**: client 名/版、操作と期待 state、異常系、実行権限、証拠保存先が各々出典付きで特定される。未確認項目は unknown のまま残る。
- **必要検証・証拠**: tool schema/実装/test と実 acceptance 記録の相互参照。計画・PRの存在だけを live acceptance としない。
- **依存・着手条件**: MS-001 後。利用者/client/許可が未特定なら調査範囲を広げず pending。
- **担当・変更範囲**: Dot は既存 repo と許可済み記録を読む。外部 client 呼出し、顧客情報保存、deploy、公開、署名発行は行わない。
- **待ち・失敗・再開**: 外部許可・実 client・記録へのアクセスがない場合は waiting と解除条件を記録し、構造的に未接続の能力として報告する。
- **条件付き不要の判定**: 同条件の現行 acceptance record が全ACを満たす場合だけ不要。証拠のない確認済み扱いは禁止。

## 完了と再計画

- 状態語: `pending / ready / in_progress / waiting / done / not_needed`。`done` は事前ACと必要な配達 gate の evidence が揃った時だけ。
- WP-001 または WP-002 が既達成なら、有効な同条件証拠を再利用して重複実行しない。failure は完了WPへ混ぜない。
- MS-001 完了条件: WP-001 と WP-002 がそれぞれ done または根拠付き not_needed で、受け入れ済みの実装が通常の配達経路に載る。commit、PR、test 結果は実行元記録から取得する。
- 条件付き不要時の経路: WP-001 が not_needed の時 WP-002 の dependency を解除し、独立にAC判定する。WP-003 は MS-001 完了後にのみ ready になり得る。
- 比較WP: 不要。性能改善や新旧比較は本 wave のACではない。
- runtime進捗・raw evidence: 既存 TAS run/task/PR/test records。repository に新しい実行台帳を追加しない。
- 追加修正は原因と対象WPに束縛し、別課題へ広げない。
- 次の計画波: WP-003 に必要な利用者/client/権限が揃い、その acceptance 境界が定義できた時だけ作成する。全件完了でも価値のない作業を補充しない。

<!-- END: GENERATED -->
<!-- BEGIN: HUMAN -->
<!-- END: HUMAN -->
