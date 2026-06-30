# Prompt Templates

## Initial implementation prompt

Claude Code Webに直接貼る標準プロンプトです。

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

## Existing PR auto-fix prompt

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

## Codex manual review prompt

Codex Automatic Reviewが有効でない場合、PRコメントで手動実行します。

```text
@codex review for P0/P1 regressions, missing tests, security risks, permission changes, dependency changes, and behavior changes. Follow AGENTS.md. Do not fix the PR.
```

## Human review checklist prompt

人間レビュー時にClaudeへ説明させたい場合:

```text
このPRの変更内容を、人間レビュー用に要約してください。

出力:
- 変更概要
- 変更ファイル
- 要件への対応
- 実行済み検証
- 未検証事項
- Codexコメントへの対応
- merge前に人間が確認すべき点

mergeはしないでください。
```
