---
title: サブモジュール自動同期
sidebar_position: 1
---

# サブモジュール自動同期パターン

Git サブモジュールを使ったドキュメント管理において、外部リポジトリの更新を自動的に検知・同期し、サイトを再デプロイするワークフローパターンです。

## 🎯 目的

- **自動化**: サブモジュールの更新を手動で行う必要をなくす
- **鮮度維持**: ドキュメントが常に最新の状態を保つ
- **トレーサビリティ**: 何がいつ更新されたかを明確に記録
- **効率化**: 複数のサブモジュールをまとめて管理

## 📦 適用例

### ateliers-dev リポジトリ

Docusaurus サイトで複数のナレッジリポジトリを統合：

```
ateliers-dev/
├── docs/
│   ├── ateliers-knowledge/          # サブモジュール
│   └── project-knowledge/
│       └── ateliers-ai-mcp/         # サブモジュール
└── .github/workflows/
    └── update-submodules-and-deploy.yml
```

**サブモジュール**:
- `docs/ateliers-knowledge` - 開発ガイドライン・AI生成ガイドライン
- `docs/project-knowledge/ateliers-ai-mcp` - プロジェクト固有のドキュメント

**更新頻度**: 毎日 UTC 0:00 (JST 9:00)

## 🔧 ワークフロー構成

### 基本的なフロー

```
1. サブモジュールの現在のコミットを取得
   ↓
2. サブモジュールを最新に更新
   ↓
3. 更新後のコミットを取得
   ↓
4. 変更を検出
   ↓
5. (変更がある場合) changelog を取得
   ↓
6. (変更がある場合) コミット＆プッシュ
   ↓
7. サイトをビルド
   ↓
8. (変更がある場合) デプロイ
   ↓
9. サマリーを生成
```

### トリガー設定

```yaml
on:
  # 毎日定期実行
  schedule:
    - cron: '0 0 * * *'  # UTC 0:00 = JST 9:00
  
  # 手動実行を許可
  workflow_dispatch:
```

**推奨**: 
- 定期実行は業務開始時刻に合わせる（JST 9:00 など）
- 緊急時やテスト用に `workflow_dispatch` を有効化

## 📝 実装パターン

### パターン1: 単一サブモジュール

最もシンプルな形式。1つのサブモジュールのみを管理します。

```yaml
- name: Get current commit
  id: current_commit
  run: |
    cd path/to/submodule
    echo "sha=$(git rev-parse HEAD)" >> $GITHUB_OUTPUT
    echo "short_sha=$(git rev-parse --short HEAD)" >> $GITHUB_OUTPUT

- name: Update submodule
  run: git submodule update --remote --merge path/to/submodule

- name: Get updated commit
  id: updated_commit
  run: |
    cd path/to/submodule
    echo "sha=$(git rev-parse HEAD)" >> $GITHUB_OUTPUT
    echo "short_sha=$(git rev-parse --short HEAD)" >> $GITHUB_OUTPUT

- name: Check for changes
  id: check_changes
  run: |
    if [ "${{ steps.current_commit.outputs.sha }}" == "${{ steps.updated_commit.outputs.sha }}" ]; then
      echo "has_changes=false" >> $GITHUB_OUTPUT
    else
      echo "has_changes=true" >> $GITHUB_OUTPUT
    fi
```

### パターン2: 複数サブモジュール（推奨）

複数のサブモジュールを効率的に管理します。ateliers-dev で採用している形式です。

**特徴**:
- 各サブモジュールを個別に処理
- 変更検出を各サブモジュールごとに実行
- いずれかに変更があればデプロイ
- コミットメッセージに全サブモジュールの変更を統合

**実装例**: [update-submodules-and-deploy.yml](https://github.com/yuu-git/ateliers-dev/blob/master/.github/workflows/update-submodules-and-deploy.yml)

### 変更検出の実装

```yaml
- name: Check for changes
  id: check_changes
  run: |
    if [ "${{ steps.current_commit.outputs.sha }}" == "${{ steps.updated_commit.outputs.sha }}" ]; then
      echo "has_changes=false" >> $GITHUB_OUTPUT
      echo "No changes detected in submodule"
    else
      echo "has_changes=true" >> $GITHUB_OUTPUT
      echo "Changes detected in submodule"
    fi
```

**ポイント**:
- SHA を比較して変更を検出
- 変更がない場合はデプロイをスキップ（コスト削減）
- ログに変更有無を明記

## 📊 変更履歴の記録

### Changelog の取得

```yaml
- name: Get change log
  if: steps.check_changes.outputs.has_changes == 'true'
  id: changelog
  run: |
    cd path/to/submodule
    CHANGELOG=$(git log --oneline ${{ steps.current_commit.outputs.sha }}..${{ steps.updated_commit.outputs.sha }})
    echo "log<<EOF" >> $GITHUB_OUTPUT
    echo "$CHANGELOG" >> $GITHUB_OUTPUT
    echo "EOF" >> $GITHUB_OUTPUT
    
    COMMIT_COUNT=$(git rev-list --count ${{ steps.current_commit.outputs.sha }}..${{ steps.updated_commit.outputs.sha }})
    echo "count=$COMMIT_COUNT" >> $GITHUB_OUTPUT
```

**出力例**:
```
abc1234 feat: add new guideline
def5678 docs: update README
```

### コミットメッセージ形式

**単一サブモジュール**:
```
chore: update documentation submodule

submodule-name: abc1234 → def5678 (3 commits)

Details:
## submodule-name
abc1234 feat: add new guideline
def5678 docs: update README
ghi9012 fix: correct typo
```

**複数サブモジュール**:
```
chore: update documentation submodules

ateliers-ai-mcp: abc1234 → def5678 (2 commits)
ateliers-knowledge: ghi9012 → jkl3456 (5 commits)

Details:

## ateliers-ai-mcp
abc1234 feat: add logging guide
def5678 docs: update README

## ateliers-knowledge
ghi9012 feat: add CI/CD guidelines
jkl3456 docs: update coding standards
...
```

## 🚀 デプロイ統合

### Docusaurus のビルド

```yaml
- name: Setup Node.js
  uses: actions/setup-node@v4
  with:
    node-version: '20'
    cache: 'npm'

- name: Install dependencies
  run: npm ci

- name: Build Docusaurus site
  run: npm run build
```

**ポイント**:
- 変更がない場合でもビルドは実行（検証目的）
- npm キャッシュを有効化してビルド時間を短縮

### GitHub Pages へのデプロイ

```yaml
- name: Deploy to GitHub Pages
  if: steps.check_changes.outputs.has_changes == 'true'
  uses: peaceiris/actions-gh-pages@v3
  with:
    personal_token: ${{ secrets.GH_TOKEN_DEPLOY }}
    publish_dir: ./build
    cname: your-domain.com
    commit_message: "docs: rebuild with updated submodules"
```

**条件**:
- いずれかのサブモジュールに変更がある場合のみ実行
- 変更がない場合はデプロイをスキップ

**必要な Secrets**:
- `GH_TOKEN_DEPLOY`: Personal Access Token (PAT)
  - 権限: `repo`, `workflow`
  - gh-pages ブランチへのプッシュに必要

## 📈 ワークフローサマリー

実行結果を可視化するための Summary を生成します。

### サマリーの構成

```yaml
- name: Create workflow summary
  run: |
    echo "## 📚 Submodules Update Summary" >> $GITHUB_STEP_SUMMARY
    echo "" >> $GITHUB_STEP_SUMMARY
    
    if [ "$HAS_ANY_CHANGES" == "true" ]; then
      echo "### ✅ Submodules Updated" >> $GITHUB_STEP_SUMMARY
      echo "" >> $GITHUB_STEP_SUMMARY
      
      echo "#### 🔧 submodule-name" >> $GITHUB_STEP_SUMMARY
      echo "- **From**: \`abc1234\`" >> $GITHUB_STEP_SUMMARY
      echo "- **To**: \`def5678\`" >> $GITHUB_STEP_SUMMARY
      echo "- **Changes**: 3 commit(s)" >> $GITHUB_STEP_SUMMARY
      echo "" >> $GITHUB_STEP_SUMMARY
      
      echo "<details><summary>Change Log</summary>" >> $GITHUB_STEP_SUMMARY
      echo "" >> $GITHUB_STEP_SUMMARY
      echo "\`\`\`" >> $GITHUB_STEP_SUMMARY
      echo "$CHANGELOG" >> $GITHUB_STEP_SUMMARY
      echo "\`\`\`" >> $GITHUB_STEP_SUMMARY
      echo "</details>" >> $GITHUB_STEP_SUMMARY
      
      echo "### 🚀 Deployment" >> $GITHUB_STEP_SUMMARY
      echo "Site rebuilt and deployed" >> $GITHUB_STEP_SUMMARY
    else
      echo "### ℹ️ No Changes" >> $GITHUB_STEP_SUMMARY
      echo "All submodules are already up to date." >> $GITHUB_STEP_SUMMARY
    fi
```

### 出力例

**変更あり**:
```
## 📚 Submodules Update Summary

### ✅ Submodules Updated

#### 🔧 ateliers-ai-mcp
- **From**: `abc1234`
- **To**: `def5678`
- **Changes**: 2 commit(s)

<details><summary>Change Log</summary>
```
abc1234 feat: add logging guide
def5678 docs: update README
```
</details>

#### 📖 ateliers-knowledge
- **From**: `ghi9012`
- **To**: `jkl3456`
- **Changes**: 5 commit(s)

### 🚀 Deployment
Site rebuilt and deployed to ateliers.dev
```

**変更なし**:
```
## 📚 Submodules Update Summary

### ℹ️ No Changes
All submodules are already up to date.

### ✅ Build Verified
Build completed successfully with no changes.
```

## 🛠️ トラブルシューティング

### サブモジュールが更新されない

**原因**: サブモジュールが detached HEAD 状態

**解決策**:
```bash
cd path/to/submodule
git checkout master
git pull origin master
```

### デプロイが失敗する

**原因1**: PAT の権限不足

**解決策**: `repo` と `workflow` 権限を付与

**原因2**: gh-pages ブランチが存在しない

**解決策**: 初回は手動で gh-pages ブランチを作成

### ビルドエラー

**原因**: サブモジュールの内容が Docusaurus の構文に違反

**解決策**:
1. ローカルで `npm run build` を実行
2. エラー内容を確認
3. サブモジュール側で修正

## 📚 参考リソース

### 実装例

- [ateliers-dev/workflows/update-submodules-and-deploy.yml](https://github.com/yuu-git/ateliers-dev/blob/master/.github/workflows/update-submodules-and-deploy.yml)

### 関連ドキュメント

- [Git Submodules](https://git-scm.com/book/en/v2/Git-Tools-Submodules)
- [GitHub Actions - Workflow syntax](https://docs.github.com/actions/using-workflows/workflow-syntax-for-github-actions)
- [peaceiris/actions-gh-pages](https://github.com/peaceiris/actions-gh-pages)

## 🔮 今後の拡張

### 考えられる改善

1. **並列処理**: 複数サブモジュールを並列で更新
2. **選択的更新**: 特定のサブモジュールのみ更新
3. **通知機能**: Slack や Discord への通知
4. **ロールバック**: 問題が発生した場合の自動ロールバック

### 他のユースケース

- API ドキュメントの自動生成と同期
- サンプルコードリポジトリの統合
- 翻訳リポジトリの同期

---

*このパターンは ateliers-dev プロジェクトで実際に運用されています。*
*実装例: [update-submodules-and-deploy.yml](https://github.com/yuu-git/ateliers-dev/blob/master/.github/workflows/update-submodules-and-deploy.yml)*
