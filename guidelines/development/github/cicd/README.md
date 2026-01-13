---
title: CI/CD ガイドライン
sidebar_position: 0
---

# CI/CD ガイドライン

GitHub Actions を使った CI/CD パイプラインの運用方針と実装パターンを説明します。

## 📋 目的

- **自動化**: 手動作業を減らし、人的ミスを防ぐ
- **一貫性**: 同じ処理を常に同じ方法で実行する
- **透明性**: 変更履歴とデプロイ状況を可視化する
- **効率化**: 開発者がコードに集中できる環境を整える

## 📚 コンテンツ

### 実装パターン

- [サブモジュール自動同期](submodules-auto-sync.md) - Git サブモジュールの自動更新とデプロイ（Docusaurus など）
- [プロジェクトナレッジ同期](project-knowledge-sync.md) - ナレッジベースの自動更新（ビルド・デプロイなし）

## 🎯 基本方針

### 1. トリガーの設計

ワークフローは以下のトリガーを組み合わせて設計します：

- **schedule**: 定期実行（毎日、毎週など）
- **workflow_dispatch**: 手動実行（緊急時やテスト用）
- **push**: コード変更時の自動実行
- **pull_request**: PR 作成時の検証

### 2. ジョブの構成

各ジョブは以下の構成を意識します：

1. **チェックアウト**: リポジトリの取得
2. **変更検出**: 実際に変更があるか確認
3. **処理実行**: ビルド、テスト、デプロイなど
4. **結果通知**: サマリーやコミットメッセージで結果を共有

### 3. 変更管理

- **コミットメッセージ**: Conventional Commits に準拠
  - `chore:` - 運用・設定変更
  - `docs:` - ドキュメント更新
  - `feat:` - 新機能追加
  - `fix:` - バグ修正

- **変更履歴**: 詳細なログを残す
  - 何が変わったか（commit SHA）
  - どれだけ変わったか（変更数）
  - 具体的な変更内容（changelog）

### 4. エラーハンドリング

- **冪等性**: 複数回実行しても同じ結果になる
- **ロールバック**: 失敗時は前の状態に戻せる
- **通知**: 失敗時は明確なエラーメッセージを表示

## 🔐 セキュリティ

### Secrets 管理

- **GITHUB_TOKEN**: GitHub Actions が自動生成（一般的な操作）
- **Personal Access Token (PAT)**: クロスリポジトリ操作やデプロイ時
  - `GH_TOKEN_DEPLOY`: デプロイ専用トークン
  - 権限は最小限に設定

### 権限設定

```yaml
permissions:
  contents: write    # リポジトリへの書き込み
  pages: write       # GitHub Pages へのデプロイ
  id-token: write    # OIDC 認証
```

## 📊 モニタリング

### ワークフローサマリー

GitHub Actions の Summary 機能を活用し、実行結果を可視化します：

```bash
echo "## 📚 実行サマリー" >> $GITHUB_STEP_SUMMARY
echo "- **変更**: X commits" >> $GITHUB_STEP_SUMMARY
```

### 実行履歴

- Actions タブで過去の実行履歴を確認
- 失敗時はログを確認して原因を特定
- 定期的にワークフローの効率を見直す

## 🚀 ベストプラクティス

### 1. 段階的なロールアウト

新しいワークフローは以下の順で導入：

1. **手動実行のみ** (`workflow_dispatch`) でテスト
2. **限定的な自動実行** (特定ブランチのみ) で検証
3. **全面適用** (全ブランチ、定期実行)

### 2. ドキュメント化

- ワークフローの目的を明記
- トリガー条件を説明
- 必要な Secrets を列挙
- トラブルシューティング情報を追加

### 3. 依存関係の管理

- Actions のバージョンを固定（`@v4` など）
- 定期的に最新版へのアップデート
- 破壊的変更に注意

## 🔗 関連リソース

- [GitHub Actions 公式ドキュメント](https://docs.github.com/actions)
- [Workflow syntax](https://docs.github.com/actions/using-workflows/workflow-syntax-for-github-actions)
- [Conventional Commits](https://www.conventionalcommits.org/)

## 📝 更新履歴

- 2025-01-XX: 初版作成（サブモジュール自動同期・プロジェクトナレッジ同期パターン追加）

---

*このガイドラインは Ateliers プロジェクトの運用経験に基づいています。*
