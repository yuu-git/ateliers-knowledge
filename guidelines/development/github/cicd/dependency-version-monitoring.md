---
title: 外部依存関係のバージョン自動監視
category: CI/CD ガイドライン
description: GitHub Actions を使用して外部依存関係の新バージョンをチェックし、自動的に Issue を作成する方法について解説します。
tags: [GitHub Actions, CI/CD, 依存関係管理, 自動化, バージョン監視]
---
# 外部依存関係のバージョン自動監視

このドキュメントでは、GitHub Actions を使用して外部依存関係（NuGet パッケージ、GitHub リリース、npm パッケージなど）の新バージョンを自動的に監視し、更新があった場合に Issue を作成する方法について説明します。

## 概要

### 目的

- **手動チェックの削減**: 定期的な依存関係のバージョンチェックを自動化
- **迅速な対応**: 新しいバージョンがリリースされたらすぐに通知
- **トレーサビリティ**: Issue としてバージョン更新履歴を記録
- **チーム連携**: 自動的にラベルやアサインを設定して担当者に通知

### 監視可能な依存関係

- GitHub リリース（例: VOICEVOX Core、他の OSS ライブラリ）
- NuGet パッケージ
- npm パッケージ
- Docker イメージ
- その他 API で取得可能なバージョン情報

## 基本的な実装パターン

### パターン 1: GitHub Releases の監視

GitHub でホストされているプロジェクトの最新リリースを監視します。

**対象例**:
- VOICEVOX Core
- Marp CLI
- その他の OSS ライブラリ

**実装例**:

```yaml
# .github/workflows/check-external-dependencies.yml
name: Check External Dependencies

on:
  schedule:
    # 毎日 AM 9:00 (UTC) に実行 = JST 18:00
    - cron: '0 9 * * *'
  workflow_dispatch: # 手動実行も可能

jobs:
  check-github-releases:
    runs-on: ubuntu-latest
    
    steps:
    - uses: actions/checkout@v4
    
    - name: Check VOICEVOX Core
      id: voicevox
      run: |
        # GitHub API で最新リリースを取得
        LATEST=$(curl -s https://api.github.com/repos/VOICEVOX/voicevox_core/releases/latest | jq -r .tag_name)
        echo "latest_version=$LATEST" >> $GITHUB_OUTPUT
        echo "Latest VOICEVOX Core: $LATEST"
    
    - name: Get current version from docs
      id: current
      run: |
        # ドキュメントから現在のバージョンを抽出
        CURRENT=$(grep -oP 'VoicevoxCoreVersion = "\K[^"]+' docs/setup.md | head -1)
        echo "current_version=$CURRENT" >> $GITHUB_OUTPUT
        echo "Current version: $CURRENT"
    
    - name: Check if update needed
      id: check
      run: |
        LATEST="${{ steps.voicevox.outputs.latest_version }}"
        CURRENT="${{ steps.current.outputs.current_version }}"
        
        if [ "$LATEST" != "$CURRENT" ]; then
          echo "update_needed=true" >> $GITHUB_OUTPUT
          echo "Update needed: $CURRENT -> $LATEST"
        else
          echo "update_needed=false" >> $GITHUB_OUTPUT
          echo "Already up to date"
        fi
    
    - name: Create Issue
      if: steps.check.outputs.update_needed == 'true'
      uses: actions/github-script@v7
      with:
        script: |
          const latestVersion = '${{ steps.voicevox.outputs.latest_version }}';
          const currentVersion = '${{ steps.current.outputs.current_version }}';
          
          const issueBody = `## 🎉 VOICEVOX Core 新バージョンがリリースされました
          
          ### バージョン情報
          
          - **現在のバージョン**: ${currentVersion}
          - **最新バージョン**: ${latestVersion}
          - **リリースページ**: https://github.com/VOICEVOX/voicevox_core/releases/tag/${latestVersion}
          
          ### 更新が必要なファイル
          
          - [ ] \`docs/setup.md\`
          - [ ] \`setup.ps1\`
          - [ ] \`.github/workflows/ci.yml\`
          
          ### 確認手順
          
          1. [リリースノート](https://github.com/VOICEVOX/voicevox_core/releases/tag/${latestVersion}) を確認
          2. Breaking changes がないか確認
          3. テスト環境で動作確認
          4. ドキュメントを更新
          `;
          
          await github.rest.issues.create({
            owner: context.repo.owner,
            repo: context.repo.repo,
            title: `🔔 VOICEVOX Core ${latestVersion} がリリースされました`,
            body: issueBody,
            labels: ['dependencies', 'voicevox', 'enhancement']
          });
```

### パターン 2: NuGet パッケージの監視

NuGet.org でホストされているパッケージの最新バージョンを監視します。

**実装例**:

```yaml
jobs:
  check-nuget-packages:
    runs-on: ubuntu-latest
    
    steps:
    - uses: actions/checkout@v4
    
    - name: Check NuGet Package
      id: nuget
      run: |
        PACKAGE_NAME="Newtonsoft.Json"
        
        # NuGet API で最新バージョンを取得
        LATEST=$(curl -s "https://api.nuget.org/v3-flatcontainer/${PACKAGE_NAME}/index.json" | jq -r '.versions[-1]')
        
        echo "latest_version=$LATEST" >> $GITHUB_OUTPUT
        echo "package_name=$PACKAGE_NAME" >> $GITHUB_OUTPUT
        echo "Latest $PACKAGE_NAME: $LATEST"
    
    - name: Get current version from csproj
      id: current
      run: |
        # csproj から現在のバージョンを抽出
        CURRENT=$(grep -oP '<PackageReference Include="Newtonsoft.Json" Version="\K[^"]+' src/MyProject/MyProject.csproj)
        echo "current_version=$CURRENT" >> $GITHUB_OUTPUT
        echo "Current version: $CURRENT"
    
    - name: Create Issue if update needed
      if: steps.nuget.outputs.latest_version != steps.current.outputs.current_version
      uses: actions/github-script@v7
      with:
        script: |
          const packageName = '${{ steps.nuget.outputs.package_name }}';
          const latestVersion = '${{ steps.nuget.outputs.latest_version }}';
          const currentVersion = '${{ steps.current.outputs.current_version }}';
          
          await github.rest.issues.create({
            owner: context.repo.owner,
            repo: context.repo.repo,
            title: `📦 ${packageName} ${latestVersion} が利用可能です`,
            body: `現在: ${currentVersion} → 最新: ${latestVersion}\n\nhttps://www.nuget.org/packages/${packageName}/${latestVersion}`,
            labels: ['dependencies', 'nuget']
          });
```

### パターン 3: npm パッケージの監視

**実装例**:

```yaml
jobs:
  check-npm-packages:
    runs-on: ubuntu-latest
    
    steps:
    - uses: actions/checkout@v4
    
    - name: Check npm Package
      id: npm
      run: |
        PACKAGE_NAME="@marp-team/marp-cli"
        
        # npm registry で最新バージョンを取得
        LATEST=$(npm view $PACKAGE_NAME version)
        
        echo "latest_version=$LATEST" >> $GITHUB_OUTPUT
        echo "package_name=$PACKAGE_NAME" >> $GITHUB_OUTPUT
        echo "Latest $PACKAGE_NAME: $LATEST"
    
    - name: Get current version from package.json
      id: current
      run: |
        CURRENT=$(jq -r '.dependencies["@marp-team/marp-cli"]' package.json)
        echo "current_version=$CURRENT" >> $GITHUB_OUTPUT
        echo "Current version: $CURRENT"
    
    - name: Create Issue if update needed
      if: steps.npm.outputs.latest_version != steps.current.outputs.current_version
      uses: actions/github-script@v7
      with:
        script: |
          const packageName = '${{ steps.npm.outputs.package_name }}';
          const latestVersion = '${{ steps.npm.outputs.latest_version }}';
          const currentVersion = '${{ steps.current.outputs.current_version }}';
          
          await github.rest.issues.create({
            owner: context.repo.owner,
            repo: context.repo.repo,
            title: `📦 ${packageName} ${latestVersion} が利用可能です`,
            body: `現在: ${currentVersion} → 最新: ${latestVersion}\n\nhttps://www.npmjs.com/package/${packageName}`,
            labels: ['dependencies', 'npm']
          });
```

## 高度な実装パターン

### パターン A: 複数の依存関係を一度にチェック

**実装例**:

```yaml
jobs:
  check-all-dependencies:
    runs-on: ubuntu-latest
    
    strategy:
      matrix:
        dependency:
          - name: "VOICEVOX Core"
            type: "github"
            repo: "VOICEVOX/voicevox_core"
            current_file: "docs/setup.md"
            pattern: 'VoicevoxCoreVersion = "\K[^"]+'
          
          - name: "Marp CLI"
            type: "npm"
            package: "@marp-team/marp-cli"
            current_file: "package.json"
            pattern: '\.dependencies\["@marp-team/marp-cli"\]'
          
          - name: "Newtonsoft.Json"
            type: "nuget"
            package: "Newtonsoft.Json"
            current_file: "src/MyProject/MyProject.csproj"
            pattern: '<PackageReference Include="Newtonsoft.Json" Version="\K[^"]+'
    
    steps:
    - uses: actions/checkout@v4
    
    - name: Check version
      id: check
      run: |
        # 依存関係の種類に応じてバージョンを取得
        case "${{ matrix.dependency.type }}" in
          github)
            LATEST=$(curl -s https://api.github.com/repos/${{ matrix.dependency.repo }}/releases/latest | jq -r .tag_name)
            ;;
          npm)
            LATEST=$(npm view ${{ matrix.dependency.package }} version)
            ;;
          nuget)
            LATEST=$(curl -s "https://api.nuget.org/v3-flatcontainer/${{ matrix.dependency.package }}/index.json" | jq -r '.versions[-1]')
            ;;
        esac
        
        CURRENT=$(grep -oP '${{ matrix.dependency.pattern }}' ${{ matrix.dependency.current_file }} | head -1)
        
        echo "latest_version=$LATEST" >> $GITHUB_OUTPUT
        echo "current_version=$CURRENT" >> $GITHUB_OUTPUT
        
        if [ "$LATEST" != "$CURRENT" ]; then
          echo "update_needed=true" >> $GITHUB_OUTPUT
        else
          echo "update_needed=false" >> $GITHUB_OUTPUT
        fi
    
    - name: Create Issue
      if: steps.check.outputs.update_needed == 'true'
      uses: actions/github-script@v7
      with:
        script: |
          await github.rest.issues.create({
            owner: context.repo.owner,
            repo: context.repo.repo,
            title: `🔔 ${{ matrix.dependency.name }} ${{ steps.check.outputs.latest_version }} が利用可能です`,
            body: `現在: ${{ steps.check.outputs.current_version }} → 最新: ${{ steps.check.outputs.latest_version }}`,
            labels: ['dependencies']
          });
```

### パターン B: 自動でブランチ作成 + Pull Request

新しいバージョンが見つかったら、自動的に更新ブランチを作成して PR を作成します。

**実装例**:

```yaml
- name: Create update branch and PR
  if: steps.check.outputs.update_needed == 'true'
  env:
    GH_TOKEN: ${{ github.token }}
  run: |
    git config user.name "github-actions[bot]"
    git config user.email "github-actions[bot]@users.noreply.github.com"
    
    BRANCH="update/voicevox-core-${{ steps.check.outputs.latest_version }}"
    git checkout -b $BRANCH
    
    # ドキュメントを自動更新
    sed -i 's/VoicevoxCoreVersion = "[^"]*"/VoicevoxCoreVersion = "${{ steps.check.outputs.latest_version }}"/' docs/setup.md
    
    git add .
    git commit -m "chore: update VOICEVOX Core to ${{ steps.check.outputs.latest_version }}"
    git push origin $BRANCH
    
    # PR を作成
    gh pr create \
      --title "Update VOICEVOX Core to ${{ steps.check.outputs.latest_version }}" \
      --body "Auto-generated PR for dependency update" \
      --label "dependencies"
```

### パターン C: Slack/Discord 通知

**Slack 通知の例**:

```yaml
- name: Notify Slack
  if: steps.check.outputs.update_needed == 'true'
  uses: slackapi/slack-github-action@v1
  with:
    payload: |
      {
        "text": "🔔 VOICEVOX Core ${{ steps.check.outputs.latest_version }} がリリースされました！",
        "blocks": [
          {
            "type": "section",
            "text": {
              "type": "mrkdwn",
              "text": "*VOICEVOX Core 新バージョン*\n現在: ${{ steps.check.outputs.current_version }}\n最新: ${{ steps.check.outputs.latest_version }}"
            }
          },
          {
            "type": "actions",
            "elements": [
              {
                "type": "button",
                "text": {
                  "type": "plain_text",
                  "text": "リリースノートを見る"
                },
                "url": "https://github.com/VOICEVOX/voicevox_core/releases/tag/${{ steps.check.outputs.latest_version }}"
              }
            ]
          }
        ]
      }
  env:
    SLACK_WEBHOOK_URL: ${{ secrets.SLACK_WEBHOOK_URL }}
```

**Discord 通知の例**:

```yaml
- name: Notify Discord
  if: steps.check.outputs.update_needed == 'true'
  run: |
    curl -X POST "${{ secrets.DISCORD_WEBHOOK_URL }}" \
      -H "Content-Type: application/json" \
      -d '{
        "content": "🔔 VOICEVOX Core ${{ steps.check.outputs.latest_version }} がリリースされました！",
        "embeds": [{
          "title": "新バージョンが利用可能です",
          "description": "現在: ${{ steps.check.outputs.current_version }}\n最新: ${{ steps.check.outputs.latest_version }}",
          "color": 3447003,
          "url": "https://github.com/VOICEVOX/voicevox_core/releases/tag/${{ steps.check.outputs.latest_version }}"
        }]
      }'
```

### パターン D: リリースノートを取得して Issue に含める

**実装例**:

```yaml
- name: Get release notes
  if: steps.check.outputs.update_needed == 'true'
  id: release_notes
  run: |
    NOTES=$(curl -s https://api.github.com/repos/VOICEVOX/voicevox_core/releases/latest | jq -r .body)
    
    # 複数行を扱うため、EOF を使用
    echo "notes<<EOF" >> $GITHUB_OUTPUT
    echo "$NOTES" >> $GITHUB_OUTPUT
    echo "EOF" >> $GITHUB_OUTPUT

- name: Create Issue with release notes
  if: steps.check.outputs.update_needed == 'true'
  uses: actions/github-script@v7
  with:
    script: |
      const releaseNotes = `${{ steps.release_notes.outputs.notes }}`;
      
      const issueBody = `## 🎉 VOICEVOX Core 新バージョンがリリースされました
      
      ### バージョン情報
      
      - **現在のバージョン**: ${{ steps.check.outputs.current_version }}
      - **最新バージョン**: ${{ steps.check.outputs.latest_version }}
      
      ### リリースノート
      
      ${releaseNotes}
      
      ### 更新手順
      
      1. テスト環境で動作確認
      2. ドキュメントを更新
      3. CI/CD パイプラインを更新
      `;
      
      await github.rest.issues.create({
        owner: context.repo.owner,
        repo: context.repo.repo,
        title: `🔔 VOICEVOX Core ${{ steps.check.outputs.latest_version }} がリリースされました`,
        body: issueBody,
        labels: ['dependencies', 'voicevox']
      });
```

## ベストプラクティス

### 1. 実行頻度の設定

**推奨**:
- **毎日 1 回**: 安定した依存関係（例: 月1回程度しか更新されない）
- **毎週 1 回**: あまり頻繁に更新されない依存関係
- **手動実行のみ**: 特定のタイミングでチェックしたい場合

```yaml
on:
  schedule:
    # 毎日 AM 9:00 (UTC)
    - cron: '0 9 * * *'
  
  # または毎週月曜日
  schedule:
    - cron: '0 9 * * 1'
  
  # 手動実行も可能
  workflow_dispatch:
```

### 2. Issue の重複を防ぐ

同じバージョンで複数の Issue が作成されないようにします。

```yaml
- name: Check existing issues
  id: existing
  run: |
    EXISTING=$(gh issue list --label "dependencies" --search "VOICEVOX Core ${{ steps.check.outputs.latest_version }}" --json number --jq length)
    
    if [ "$EXISTING" -gt "0" ]; then
      echo "exists=true" >> $GITHUB_OUTPUT
    else
      echo "exists=false" >> $GITHUB_OUTPUT
    fi
  env:
    GH_TOKEN: ${{ github.token }}

- name: Create Issue
  if: steps.check.outputs.update_needed == 'true' && steps.existing.outputs.exists == 'false'
  uses: actions/github-script@v7
  with:
    script: |
      # Issue 作成処理
```

### 3. ラベルとアサインの活用

**推奨ラベル**:
- `dependencies`: 依存関係の更新
- `automated`: 自動生成された Issue
- パッケージ名（例: `voicevox`, `nuget`, `npm`）
- 優先度（例: `priority:low`, `priority:medium`, `priority:high`）

```yaml
await github.rest.issues.create({
  owner: context.repo.owner,
  repo: context.repo.repo,
  title: `🔔 VOICEVOX Core ${latestVersion} がリリースされました`,
  body: issueBody,
  labels: ['dependencies', 'voicevox', 'automated', 'priority:medium'],
  assignees: ['your-username']  // 自動アサイン
});
```

### 4. Breaking Changes の検出

リリースノートから Breaking Changes を検出して強調表示します。

```yaml
- name: Check for breaking changes
  id: breaking
  run: |
    NOTES=$(curl -s https://api.github.com/repos/VOICEVOX/voicevox_core/releases/latest | jq -r .body)
    
    if echo "$NOTES" | grep -iq "breaking"; then
      echo "has_breaking=true" >> $GITHUB_OUTPUT
      echo "⚠️ Breaking changes detected!"
    else
      echo "has_breaking=false" >> $GITHUB_OUTPUT
    fi

- name: Create Issue with warning
  if: steps.check.outputs.update_needed == 'true'
  uses: actions/github-script@v7
  with:
    script: |
      const hasBreaking = '${{ steps.breaking.outputs.has_breaking }}' === 'true';
      const title = hasBreaking 
        ? `⚠️ VOICEVOX Core ${latestVersion} (Breaking Changes あり)`
        : `🔔 VOICEVOX Core ${latestVersion} がリリースされました`;
      
      const labels = hasBreaking
        ? ['dependencies', 'voicevox', 'breaking-change', 'priority:high']
        : ['dependencies', 'voicevox', 'priority:medium'];
      
      await github.rest.issues.create({
        owner: context.repo.owner,
        repo: context.repo.repo,
        title: title,
        body: issueBody,
        labels: labels
      });
```

## トラブルシューティング

### GitHub API のレート制限

**症状**: API のレート制限に達してエラーが発生する

**解決方法**:
- `GITHUB_TOKEN` を使用する（認証付きリクエストはレート制限が緩い）
- キャッシュを活用する
- 実行頻度を減らす

```yaml
- name: Check with authentication
  run: |
    LATEST=$(curl -H "Authorization: token ${{ secrets.GITHUB_TOKEN }}" \
      -s https://api.github.com/repos/VOICEVOX/voicevox_core/releases/latest \
      | jq -r .tag_name)
```

### バージョン番号の抽出が失敗する

**症状**: grep や jq でバージョンが正しく抽出できない

**解決方法**:
- 正規表現パターンを見直す
- デバッグ出力を追加する
- 抽出結果を検証する

```yaml
- name: Extract version with validation
  run: |
    CURRENT=$(grep -oP 'VoicevoxCoreVersion = "\K[^"]+' docs/setup.md | head -1)
    
    # バージョンが空でないか確認
    if [ -z "$CURRENT" ]; then
      echo "Error: Failed to extract current version"
      exit 1
    fi
    
    # セマンティックバージョニング形式か確認
    if ! echo "$CURRENT" | grep -qP '^\d+\.\d+\.\d+$'; then
      echo "Warning: Version format may be incorrect: $CURRENT"
    fi
    
    echo "current_version=$CURRENT" >> $GITHUB_OUTPUT
```

### Issue が作成されない

**症状**: ワークフローは成功するが Issue が作成されない

**解決方法**:
- `GITHUB_TOKEN` の権限を確認（`issues: write` が必要）
- ワークフローのログを確認
- 条件式を見直す

```yaml
# .github/workflows/check-dependencies.yml
permissions:
  contents: read
  issues: write  # Issue 作成に必要
```

## 実装チェックリスト

- [ ] ワークフローファイルを作成
- [ ] 実行頻度を設定（cron または workflow_dispatch）
- [ ] 依存関係の種類に応じた取得ロジックを実装
- [ ] 現在のバージョンを抽出するロジックを実装
- [ ] バージョン比較ロジックを実装
- [ ] Issue 作成ロジックを実装
- [ ] ラベルとアサインを設定
- [ ] Issue の重複チェックを実装（推奨）
- [ ] Breaking Changes の検出（推奨）
- [ ] 通知機能の実装（任意: Slack/Discord）
- [ ] エラーハンドリングを実装
- [ ] テスト実行（workflow_dispatch で手動実行）
- [ ] ドキュメントに記載

## サンプルリポジトリ

実装例を含むサンプルリポジトリ（架空）:

```
https://github.com/example/dependency-monitoring-examples
```

## 参考リンク

### GitHub Actions 公式ドキュメント

- [Workflow syntax](https://docs.github.com/en/actions/using-workflows/workflow-syntax-for-github-actions)
- [Events that trigger workflows](https://docs.github.com/en/actions/using-workflows/events-that-trigger-workflows)
- [GitHub Script Action](https://github.com/actions/github-script)

### API ドキュメント

- [GitHub REST API - Releases](https://docs.github.com/en/rest/releases/releases)
- [NuGet API](https://learn.microsoft.com/en-us/nuget/api/overview)
- [npm Registry API](https://github.com/npm/registry/blob/master/docs/REGISTRY-API.md)

### 関連ガイドライン

- [CI/CD パイプライン設計ガイド](./pipeline-design-guide.md)
- [GitHub Actions セキュリティガイド](./github-actions-security.md)

## まとめ

### 重要ポイント

1. **定期的な自動チェックで手動作業を削減**
2. **Issue として記録してトレーサビリティを確保**
3. **ラベルとアサインで効率的な管理**
4. **Breaking Changes の検出で重大な変更を見逃さない**
5. **重複チェックで Issue の乱立を防ぐ**

### クイックスタート

```yaml
# .github/workflows/check-dependencies.yml
name: Check Dependencies

on:
  schedule:
    - cron: '0 9 * * *'
  workflow_dispatch:

jobs:
  check:
    runs-on: ubuntu-latest
    steps:
    - uses: actions/checkout@v4
    - name: Check version
      # バージョンチェックロジック
    - name: Create Issue
      # Issue 作成ロジック
```

---

**更新履歴**

| 日付 | バージョン | 変更内容 |
|------|-----------|---------|
| 2024-12-XX | 1.0 | 初版作成 |
