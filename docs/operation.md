# Operation Guide

このドキュメントは、APIキーを使わずにClaude Code WebとCodex Automatic ReviewをPR上で接続する運用手順です。

## Standard flow

```text
Human
  ↓
Claude Code Webに直接実装依頼
  ↓
Claudeがai/...ブランチを作成
  ↓
ClaudeがPR作成
  ↓
Claude Auto-fixを有効化
  ↓
CI実行
  ↓
Codex Automatic Review
  ↓
P0/P1コメントまたはCI失敗があればClaude Auto-fixが修正
  ↓
CIとCodex Reviewが再実行
  ↓
人間が確認してmerge
```

## Prompt template for Claude Code Web

```text
このリポジトリで以下を実装してください。

要件:
- ...

制約:
- 既存設計に合わせる
- 余計なファイルは触らない
- dependency updateはしない
- CI・認証・権限・deployまわりは触らない

作業:
1. ai/<short-task-name> ブランチを作成
2. 実装
3. 関連テストまたはlintを実行
4. PR作成
5. PR本文にSummary / Changed files / Verification / Risks / Not verifiedを書く
6. PRのAuto-fixを有効にする
7. Codex reviewとCI結果を待ち、P0/P1コメントまたはCI失敗があれば同じPRブランチに修正してください
8. 同じ問題で3回失敗したら停止し、人間レビューが必要とコメントしてください
9. mergeはしないでください
```

## Prompt template for existing PR auto-fix

既存PRにAuto-fixを有効化したい場合:

```text
このPRを監視し、CI失敗またはP0/P1レビューコメントがあれば修正してください。

制約:
- 同じPRブランチにpushしてください
- 新しいPRは作らないでください
- レビューコメントまたはCI失敗に関係する最小修正だけ行ってください
- 依存関係、認証、CI権限、deploy設定は明示指示がない限り変更しないでください
- 同じ問題で3回失敗したら停止し、人間レビューが必要とコメントしてください
- mergeはしないでください
```

## Codex review policy

Codexはレビュー専任です。

Expected behavior:

```text
- P0/P1のみGitHub review commentとして残す
- P2は必要最小限に留める
- 修正はClaudeに任せる
```

Avoid:

```text
@codex fix ...
```

## Human merge checklist

merge前に人間が確認します。

```text
- [ ] PR scope is clear
- [ ] CI is green
- [ ] Codex P0/P1 comments are resolved
- [ ] Claude Auto-fix did not make unrelated changes
- [ ] Dependency changes are intentional
- [ ] CI/deploy/auth/permission changes are intentional
- [ ] PR body contains verification and risks
- [ ] Branch protection requirements are satisfied
```

## When to stop the loop

次の場合はClaude Auto-fixを止め、人間レビューに切り替えます。

```text
- 同じ指摘が3回以上再発する
- Codexの指摘が曖昧
- 修正に仕様判断が必要
- 修正に依存関係更新が必要
- 認証・認可・CI権限・deploy・課金に触る
- ClaudeがPR範囲外のファイルを変更し始めた
```

## Recommended labels

APIなし運用では必須ではありませんが、手動管理用に以下を推奨します。

```text
ai:auto-fix-enabled
ai:review-clean
ai:needs-human
ai:stop
risk:high
```

ラベルは人間の状態管理用です。GitHub ActionsでAIを自動起動する用途では使いません。
