# LLM Wiki

長期圧縮コンテキスト。原則: contracts `llm-wiki-discipline`（手本: defuddle
`docs/llm-wiki/`）。正本を複製せず、検証済み知識を出典付きで圧縮する。

## Verified Knowledge

- 成果物: AIワークフローに段階的ガイダンスを与えるMCPサーバー。上流 OSS（Hive-Academy/Anubis-MCP）の fork であり、`package.json` は上流パッケージ `@hive-academy/anubis` のまま。（出典: `README.md`、`package.json`、git remote `upstream` = https://github.com/Hive-Academy/Anubis-MCP.git）
- カスタマイズ: 上流スナップショット（2025-07 時点、v1.2.20 前後）の上に自組織独自 4 コミットのみ（zero-trust package install policy・chain stage 収束と作業ツリー回収の記録・llm-wiki スキャフォールド）。upstream 追跡参照がローカルに存在しないため先行数は確定不能（fetch 禁止）で、作者・日付分析による「4 コミット」は推定。（出典: git log --format='%h %ad %an %s' による作者・日付分析 / git remote upstream=Hive-Academy/Anubis-MCP は追跡参照なし）
