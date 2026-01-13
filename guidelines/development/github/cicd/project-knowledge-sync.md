---
title: プロジェクトナレッジ同期
sidebar_position: 2
---

# プロジェクトナレッジ同期パターン

開発プロジェクトで共通のナレッジベース（設計原則・アーキテクチャガイドなど）をサブモジュールとして参照し、自動的に最新版に同期するワークフローパターンです。

## 🎯 目的

- **知識の一元管理**: プロジェクト横断的なナレッジを1箇所で管理
- **自動同期**: 各プロジェクトが常に最新のナレッジを参照できる
- **一貫性の維持**: 全プロジェクトで同じ設計原則・ガイドラインを共有
- **AI 対応**: AI ツールが最新のプロジェクト知識を参照できる

## 📦 適用例

### Ateliers MCP プロジェクト群

複数の MCP プロジェクトで共通のプロジェクトナレッジを共有：

```
ateliers-ai-mcp-services/
├── .submodules/
│   └── ateliers-ai-mcp-projectbase/    # サブモジュール
│       ├── architecture/               # アーキテクチャ設計
│       ├── design-principles/          # 設計原則
│       └── llms.txt                    # AI向けコンテキスト
└── .github/workflows/
    └── update-project-knowledge.yml

ateliers-ai-mcp-tools/
├── .submodules/
│   └── ateliers-ai-mcp-projectbase/    # 同じナレッジベース
└── .github/workflows/
    └── update-project-knowledge.yml

ateliers-ai-mcp-core/
├── .submodules/
│   └── ateliers-ai-mcp-projectbase/    # 同じナレッジベース
└── .github/workflows/
    └── update-project-knowledge.yml
```

**共有ナレッジ**: `ateliers-ai-mcp-projectbase`
- MCP プロジェクト共通の設計原則
- アーキテクチャガイドライン
- AI ツール向けのコンテキスト情報

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
7. サマリーを生成
```

**特徴**:
- ビルドやデプロイは不要（ナレッジ参照のみ）
- 軽量で高速な実行
- 複数プロジェクトに同じワークフローを適用可能

### トリガー設定

```yaml
on:
  # 毎日定期実行
  schedule:
    - cron: '0 0 * * *'  # UTC 0:00 = JST 9:00
  
  # 手動実行を許可
  workflow_dispatch:
```

## 📝 実装パターン

### 完全な実装例

```yaml
name: Update Project Knowledge

on:
  schedule:
    - cron: '0 0 * * *'
  workflow_dispatch:

jobs:
  update-submodule:
    runs-on: ubuntu-latest
    
    permissions:
      contents: write
    
    steps:
      # 1. リポジトリとサブモジュールをチェックアウト
      - name: Checkout repository
        uses: actions/checkout@v4
        with:
          token: ${{ secrets.GITHUB_TOKEN }}
          submodules: true
      
      # 2. 現在のサブモジュールコミットを取得
      - name: Get current submodule commit
        id: current_commit
        run: |
          cd .submodules/ateliers-ai-mcp-projectbase
          CURRENT_SHA=$(git rev-parse HEAD)
          CURRENT_SHORT_SHA=$(git rev-parse --short HEAD)
          echo "sha=$CURRENT_SHA" >> $GITHUB_OUTPUT
          echo "short_sha=$CURRENT_SHORT_SHA" >> $GITHUB_OUTPUT
          cd ../..
      
      # 3. サブモジュールを最新に更新
      - name: Update submodule to latest
        run: |
          git submodule update --remote .submodules/ateliers-ai-mcp-projectbase
      
      # 4. 更新後のサブモジュールコミットを取得
      - name: Get updated submodule commit
        id: updated_commit
        run: |
          cd .submodules/ateliers-ai-mcp-projectbase
          UPDATED_SHA=$(git rev-parse HEAD)
          UPDATED_SHORT_SHA=$(git rev-parse --short HEAD)
          echo "sha=$UPDATED_SHA" >> $GITHUB_OUTPUT
          echo "short_sha=$UPDATED_SHORT_SHA" >> $GITHUB_OUTPUT
          cd ../..
      
      # 5. 変更を検出
      - name: Check for changes
        id: check_changes
        run: |
          if [ "${{ steps.current_commit.outputs.sha }}" == "${{ steps.updated_commit.outputs.sha }}" ]; then
            echo "has_changes=false" >> $GITHUB_OUTPUT
            echo "No changes detected"
          else
            echo "has_changes=true" >> $GITHUB_OUTPUT
            echo "Changes detected"
          fi
      
      # 6. 変更履歴を取得（変更がある場合のみ）
      - name: Get change log
        if: steps.check_changes.outputs.has_changes == 'true'
        id: changelog
        run: |
          cd .submodules/ateliers-ai-mcp-projectbase
          CHANGELOG=$(git log --oneline ${{ steps.current_commit.outputs.sha }}..${{ steps.updated_commit.outputs.sha }})
          echo "log<<EOF" >> $GITHUB_OUTPUT
          echo "$CHANGELOG" >> $GITHUB_OUTPUT
          echo "EOF" >> $GITHUB_OUTPUT
          
          COMMIT_COUNT=$(git rev-list --count ${{ steps.current_commit.outputs.sha }}..${{ steps.updated_commit.outputs.sha }})
          echo "count=$COMMIT_COUNT" >> $GITHUB_OUTPUT
          cd ../..
      
      # 7. コミット＆プッシュ（変更がある場合のみ）
      - name: Commit and push if changed
        if: steps.check_changes.outputs.has_changes == 'true'
        run: |
          git config user.name "github-actions[bot]"
          git config user.email "github-actions[bot]@users.noreply.github.com"
          git add .submodules/ateliers-ai-mcp-projectbase
          git commit -m "chore: update project knowledge submodule" \
                     -m "Updated from ${{ steps.current_commit.outputs.short_sha }} to ${{ steps.updated_commit.outputs.short_sha }} (${{ steps.changelog.outputs.count }} commits)"
          git push
      
      # 8. サマリーを生成
      - name: Create summary
        run: |
          if [ "${{ steps.check_changes.outputs.has_changes }}" == "true" ]; then
            echo "## ✅ Project Knowledge サブモジュールを更新しました" >> $GITHUB_STEP_SUMMARY
            echo "" >> $GITHUB_STEP_SUMMARY
            echo "### 📊 更新情報" >> $GITHUB_STEP_SUMMARY
            echo "" >> $GITHUB_STEP_SUMMARY
            echo "- **更新前**: \`${{ steps.current_commit.outputs.short_sha }}\`" >> $GITHUB_STEP_SUMMARY
            echo "- **更新後**: \`${{ steps.updated_commit.outputs.short_sha }}\`" >> $GITHUB_STEP_SUMMARY
            echo "- **コミット数**: ${{ steps.changelog.outputs.count }} commits" >> $GITHUB_STEP_SUMMARY
            echo "" >> $GITHUB_STEP_SUMMARY
            echo "### 📝 変更履歴" >> $GITHUB_STEP_SUMMARY
            echo "" >> $GITHUB_STEP_SUMMARY
            echo '```' >> $GITHUB_STEP_SUMMARY
            echo "${{ steps.changelog.outputs.log }}" >> $GITHUB_STEP_SUMMARY
            echo '```' >> $GITHUB_STEP_SUMMARY
          else
            echo "## ℹ️ Project Knowledge は既に最新版です" >> $GITHUB_STEP_SUMMARY
            echo "" >> $GITHUB_STEP_SUMMARY
            echo "現在のコミット: \`${{ steps.current_commit.outputs.short_sha }}\`" >> $GITHUB_STEP_SUMMARY
          fi
```

## 🔑 重要なポイント

### 1. サブモジュールの配置

**推奨パス**: `.submodules/<submodule-name>`

```bash
# セットアップ（初回のみ）
git submodule add https://github.com/user/knowledge-repo.git .submodules/knowledge-repo
git submodule update --init --recursive
```

**理由**:
- `.submodules/` にまとめることで管理が容易
- プロジェクトのソースコードと明確に分離
- 複数のサブモジュールを追加しても整理されている

### 2. 権限設定

```yaml
permissions:
  contents: write    # サブモジュール更新のコミット＆プッシュに必要
```

**最小権限の原則**:
- このパターンでは `contents: write` のみで十分
- `pages: write` や `id-token: write` は不要

### 3. コミットメッセージ

```yaml
git commit -m "chore: update project knowledge submodule" \
           -m "Updated from abc1234 to def5678 (3 commits)"
```

**形式**:
- 1行目: Conventional Commits に準拠（`chore:`）
- 2行目: 更新範囲を明記（commit SHA の変化とコミット数）

## 📊 ワークフローサマリー

### 出力例（変更あり）

```
## ✅ Project Knowledge サブモジュールを更新しました

### 📊 更新情報

- **更新前**: `abc1234`
- **更新後**: `def5678`
- **コミット数**: 3 commits

### 📝 変更履歴

```
abc1234 feat: add new architecture guideline
bcd2345 docs: update design principles
cde3456 fix: correct typo in llms.txt
```

### 🔍 詳細確認

```bash
# サブモジュールディレクトリに移動
cd .submodules/ateliers-ai-mcp-projectbase

# 変更履歴を確認
git log --oneline -10

# 特定のコミットの詳細を確認
git show <commit-hash>
```
```

### 出力例（変更なし）

```
## ℹ️ Project Knowledge は既に最新版です

現在のコミット: `abc1234`
```

## 🔄 手動更新スクリプト

GitHub Actions だけでなく、ローカルでの手動更新スクリプトも併用すると便利です。

### PowerShell スクリプト例

```powershell
#######################################
# Project Knowledge 更新スクリプト
#######################################

$SUBMODULE_PATH = ".submodules/ateliers-ai-mcp-projectbase"
$BRANCH = "master"

$ErrorActionPreference = "Stop"

Write-Host "🔄 Project Knowledge を更新中..." -ForegroundColor Blue

# サブモジュールの存在確認
if (-not (Test-Path $SUBMODULE_PATH)) {
    Write-Host "⚠️  エラー: サブモジュールが見つかりません" -ForegroundColor Yellow
    exit 1
}

# サブモジュールディレクトリに移動
Push-Location $SUBMODULE_PATH

try {
    # 更新前のコミットハッシュを取得
    $oldCommit = git rev-parse --short HEAD

    # 最新版を取得
    Write-Host "📥 最新版をダウンロード中..." -ForegroundColor Blue
    git pull origin $BRANCH

    # 更新後のコミットハッシュを取得
    $newCommit = git rev-parse --short HEAD

    if ($oldCommit -ne $newCommit) {
        Write-Host "✅ 更新完了！" -ForegroundColor Green
        Write-Host ""
        Write-Host "変更内容: $oldCommit → $newCommit"
        Write-Host ""
        Write-Host "詳細を確認:"
        Write-Host "  git log $oldCommit..$newCommit --oneline"
    } else {
        Write-Host "✅ 既に最新版です" -ForegroundColor Green
    }

} finally {
    Pop-Location
}
```

**使用方法**:
```powershell
# スクリプトを scripts/ に配置
.\scripts\update-project-knowledge.ps1
```

## 🆚 パターン比較

### プロジェクトナレッジ同期 vs サブモジュール自動同期

| 項目 | プロジェクトナレッジ同期 | サブモジュール自動同期 |
|------|------------------------|---------------------|
| **目的** | ナレッジ参照の最新化 | ドキュメントサイトの更新 |
| **対象** | 設計原則・アーキテクチャ | ドキュメント・ガイドライン |
| **ビルド** | なし | あり（Docusaurus など） |
| **デプロイ** | なし | あり（GitHub Pages など） |
| **実行時間** | 短い（1-2分） | 長い（5-10分） |
| **適用例** | MCP プロジェクト群 | ateliers-dev |

### 使い分けの基準

**プロジェクトナレッジ同期を選ぶ場合**:
- サブモジュールが AI や開発者の参照用
- ビルド・デプロイ不要
- 軽量で高速な同期が必要
- 多数のプロジェクトで同じナレッジを参照

**サブモジュール自動同期を選ぶ場合**:
- サブモジュールが Web サイトの一部
- ビルド・デプロイが必要
- 訪問者に公開する情報
- Docusaurus などの静的サイトジェネレータを使用

## 🛠️ トラブルシューティング

### サブモジュールが更新されない

**原因**: detached HEAD 状態

**解決策**:
```bash
cd .submodules/ateliers-ai-mcp-projectbase
git checkout master
git pull origin master
```

### ローカルとリモートの差異

**原因**: ローカルで手動更新したが、GitHub に反映されていない

**解決策**:
```bash
# サブモジュールの変更をコミット
git add .submodules/ateliers-ai-mcp-projectbase
git commit -m "chore: update project knowledge"
git push
```

### ワークフローが実行されない

**原因**: cron 設定のタイムゾーン間違い

**確認**:
- GitHub Actions の cron は UTC
- JST 9:00 = UTC 0:00 = `'0 0 * * *'`

## 📚 参考リソース

### 実装例

- [ateliers-ai-mcp-services/workflows/update-project-knowledge.yml](https://github.com/yuu-git/ateliers-ai-mcp-services/blob/master/.github/workflows/update-project-knowledge.yml)
- [ateliers-ai-mcp-tools/workflows/update-project-knowledge.yml](https://github.com/yuu-git/ateliers-ai-mcp-tools/blob/master/.github/workflows/update-project-knowledge.yml)

### 手動更新スクリプト

- [ateliers-ai-mcp-projectbase/scripts/update-project-knowledge.ps1](https://github.com/yuu-git/ateliers-ai-mcp-projectbase/blob/master/scripts/update-project-knowledge.ps1)

### ナレッジベース

- [ateliers-ai-mcp-projectbase](https://github.com/yuu-git/ateliers-ai-mcp-projectbase) - MCP プロジェクト共通のナレッジベース

## 🔮 今後の拡張

### 考えられる改善

1. **条件付き同期**: 特定のファイルが変更された場合のみ同期
2. **通知機能**: 更新時に Slack や Discord に通知
3. **複数ナレッジベース**: 異なるナレッジベースを用途別に管理
4. **バージョン管理**: 特定のタグ・バージョンに固定する機能

### 他のユースケース

- チーム共通のコーディング規約
- セキュリティガイドライン
- テンプレート集
- ツール設定ファイル（`.editorconfig`, `tsconfig.json` など）

---

*このパターンは Ateliers MCP プロジェクト群で実際に運用されています。*
*実装例: [update-project-knowledge.yml](https://github.com/yuu-git/ateliers-ai-mcp-services/blob/master/.github/workflows/update-project-knowledge.yml)*
