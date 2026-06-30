# Claude + Codex PR Loop Template

APIキーを使わず、Claude Code on the web と Codex GitHub integration を組み合わせて、PR単位の実装・レビュー・自動修正ループを運用するためのテンプレートリポジトリです。

このテンプレートは、次の前提で設計しています。

```text
Human
  ↓
Claude Code Web
  - 直接実装依頼
  - cloud VMで作業
  - ai/... branch作成
  - PR作成
  - Auto-fix有効化
  ↓
GitHub CI
  - test / lint / build
  ↓
Codex GitHub Automatic Review
  - AGENTS.mdに従ってP0/P1中心にレビュー
  ↓
Claude Auto-fix
  - CI失敗とCodexレビューコメントに反応
  - 同じPRブランチに修正push
  ↓
再度CI / Codex Review
  ↓
Human
  - conversation確認
  - branch protection確認
  - merge
```

## What this template includes

```text
CLAUDE.md                         # Claude Code Web / Auto-fix向け作業規約
AGENTS.md                         # Codex Review向け共通ガイドライン
.github/pull_request_template.md  # PR本文テンプレート
.github/workflows/ci.yml          # APIキー不要の汎用CI
docs/setup.md                     # 初期設定手順
docs/operation.md                 # 日常運用手順
docs/architecture.md              # 構成思想と責務分担
```

## What this template intentionally does not include

```text
- Claude API key
- OpenAI API key
- GitHub PAT
- AIをGitHub Actionsから直接呼び出すworkflow
- 自作Orchestrator
- 自動merge
```

APIを使わない方針のため、AIの実行制御はGitHub Actionsではなく、Claude Code Web、Claude Auto-fix、Codex GitHub integrationの各サービス側の連携に委ねます。

## Quick start

1. このリポジトリをテンプレートとして新規リポジトリを作成する。
2. 対象リポジトリに Claude GitHub App を接続する。
3. 対象リポジトリに Codex GitHub integration を接続する。
4. Codex settingsでAutomatic reviewsを有効化する。
5. `main`ブランチにBranch protectionを設定する。
6. Claude Code Webに実装依頼を出す。
7. Claudeが作成したPRでCI、Codex Review、Claude Auto-fixの挙動を確認する。

詳細は[`docs/setup.md`](docs/setup.md)を参照してください。

## Recommended role split

| Actor | Responsibility |
|---|---|
| Human | 要件提示、最終レビュー、merge判断 |
| Claude Code Web | 実装、PR作成、CI失敗・レビューコメントへのAuto-fix |
| Codex | PR差分レビュー、P0/P1の指摘 |
| GitHub Actions | CIのみ |
| Branch protection | 最終ゲート |

## Important operating principle

ClaudeとCodexの両方に同じPRを修正させないでください。

```text
Claude = implementation / autofix
Codex  = review only
Human  = final decision
```

Codexに`@codex fix`を依頼すると、ClaudeとCodexの双方が同じPRブランチを編集し、責任境界が崩れます。このテンプレートでは、Codexはレビュー専任として扱います。
