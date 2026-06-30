# Setup Guide

このテンプレートは、APIキーなしでClaude Code WebとCodex GitHub integrationを組み合わせるための初期構成です。

## 1. Create a repository from this template

このリポジトリをGitHub template repositoryとして設定し、利用先リポジトリを作成します。

推奨する利用先リポジトリ設定:

```text
Settings
  → General
  → Template repository: enabled
```

## 2. Connect Claude Code Web

利用先リポジトリにClaude GitHub Appを接続します。

必要な理由:

- Claude Code WebがクラウドVM上でリポジトリをcloneするため
- Claudeがbranchを作成し、PRを作るため
- Claude Auto-fixがPRのCI失敗やレビューコメントに反応するため

このテンプレートでは、GitHub ActionsからClaude APIを呼びません。

## 3. Connect Codex GitHub integration

利用先リポジトリにCodex GitHub integrationを接続します。

推奨設定:

```text
Codex settings
  → Code review
  → Automatic reviews: enabled
```

Codexにはレビューだけを担当させます。修正はClaude Auto-fixに任せます。

Avoid:

```text
@codex fix ...
```

これを使うと、ClaudeとCodexの双方が同じPRを編集し、責務境界が崩れます。

## 4. Configure branch protection

`main`ブランチに保護ルールを設定します。

Recommended:

```text
Require a pull request before merging
Require approvals: 1
Require status checks to pass
  - CI / Repository health
  - CI / Node.js checks, if applicable
  - CI / Python checks, if applicable
Require conversation resolution before merging
Do not allow auto-merge by bots
```

このテンプレートでは、merge判断は必ず人間が行います。

## 5. Confirm the CI workflow

初期状態では、`.github/workflows/ci.yml` は以下を行います。

- `CLAUDE.md`、`AGENTS.md`、PRテンプレートの存在確認
- Markdownファイルが空でないことの確認
- `package.json` がある場合のみNode.js checks
- `pyproject.toml`または`requirements.txt`がある場合のみPython checks

利用先プロジェクトに合わせて、CIを追加・変更してください。

## 6. First test run

Claude Code Webに以下のような小さなタスクを依頼して、PRループを確認します。

```text
このリポジトリでREADMEに1文だけ追記してください。

制約:
- ai/test-pr-loop ブランチを作成してください。
- PRを作成してください。
- PR本文はテンプレートに従ってください。
- PR作成後、Auto-fixを有効にしてください。
- Codex reviewとCI結果を確認し、P0/P1コメントまたはCI失敗があれば同じPRブランチに修正してください。
- mergeはしないでください。
```

期待結果:

```text
Claude PR作成
  ↓
CI実行
  ↓
Codex Automatic Review
  ↓
問題なければ人間がmerge判断
```

## 7. Repository-specific customization

利用先リポジトリでは、以下を必ずプロジェクト事情に合わせて編集してください。

```text
CLAUDE.md
AGENTS.md
.github/workflows/ci.yml
.github/pull_request_template.md
```

特に、以下はプロジェクトごとに明記してください。

- 実行すべきテストコマンド
- 触ってはいけないディレクトリ
- 自動修正してはいけない領域
- セキュリティ・認証・課金・CI・deployの扱い
- 事実性が重要なドキュメントの正本
