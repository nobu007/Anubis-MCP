# AUTOMATION.md — Anubis-MCP

大ゴール: AIによる全自動開発の実現。人間より圧倒的に効率的に。(fleet共通)

## 発火 (Driver)
- crontab: 0本(2026-09-15 実測・全41行との突合)
- GitHub Actions: 4本
  - `deploy-pages.yml`
  - `docker-publish.yml`
  - `npm-publish.yml`
  - `release.yml`

## Kill Switch
`gh workflow disable deploy-pages.yml docker-publish.yml npm-publish.yml release.yml`

## Status
2026-09-15 初版整備(実測: crontab突合・workflow列挙)。fleet台帳: /home/jinno/business_notes/AUTOMATION.md
