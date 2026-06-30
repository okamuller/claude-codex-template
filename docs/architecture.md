# Architecture

## Design goal

このテンプレートの目的は、APIキーや自作Orchestratorを使わずに、GitHub PR上で以下の責務分担を成立させることです。

```text
Claude Code Web = implementation / auto-fix
Codex           = review
GitHub Actions  = CI
Human           = final merge decision
```

## Non-goals

このテンプレートでは、以下を扱いません。

```text
- GitHub ActionsからClaude APIを呼ぶ
- GitHub ActionsからOpenAI APIを呼ぶ
- PRレビュー結果をJSONで機械判定する
- 自動mergeする
- コメント数がゼロになるまで完全制御する
- 外部Orchestratorを動かす
```

## Event-driven loop

APIなし運用では、ループはGitHub上のイベントと各サービスの連携に委ねます。

```text
PR opened / synchronized
  ↓
GitHub CI runs
  ↓
Codex Automatic Review posts review comments
  ↓
Claude Auto-fix sees CI failures or review comments
  ↓
Claude pushes fixes to the same branch
  ↓
PR synchronized again
```

## Why PR-based, not Issue-based

Issue起票を必須にすると、GitHub ActionsやOrchestratorでIssue本文を解釈し、AIに渡す制御が必要になります。

このテンプレートでは、ユーザーがClaude Code Webへ直接実装依頼を出す前提です。そのため、PRを状態管理の中心に置きます。

```text
Prompt in Claude Code Web
  ↓
Branch
  ↓
Pull Request
  ↓
CI / Review / Auto-fix
```

## Why Codex should not fix

CodexにもPR修正を依頼すると、ClaudeとCodexが同じPRブランチを編集することになります。

その場合、以下の問題が起きます。

```text
- どちらが変更責任を持つのか曖昧になる
- 双方が相手の修正を上書きする可能性がある
- レビュー役と実装役の境界が崩れる
- 人間が差分の意図を追いにくくなる
```

そのため、このテンプレートでは以下に固定します。

```text
Claude fixes.
Codex reviews.
Human decides.
```

## Safety model

APIなし運用では、機械的な停止条件や最大ループ回数をGitHub Actionsで保証できません。

その代わり、以下で安全性を担保します。

```text
- CLAUDE.md に停止条件を明記
- AGENTS.md にCodexのレビュー範囲を明記
- CIはGitHub Actionsで必ず実行
- Branch protectionでmergeを制限
- Human reviewを必須化
- P0/P1以外のコメントを抑制
```

## Recommended repository policy

利用先リポジトリでは、以下を明文化してください。

```text
- 自動修正してよい範囲
- 自動修正してはいけない範囲
- 必須テストコマンド
- merge前に人間が見る観点
- セキュリティ、個人情報、認証、課金、deployの扱い
```

## Minimal files

必須ファイルは以下です。

```text
CLAUDE.md
AGENTS.md
.github/pull_request_template.md
.github/workflows/ci.yml
```

補助ドキュメントは以下です。

```text
docs/setup.md
docs/operation.md
docs/architecture.md
```
