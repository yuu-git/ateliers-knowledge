# llms.txt メンテナンスガイドライン

このドキュメントは、`llms.txt` ファイルの更新・メンテナンス方法を定義します。

## 📋 llms.txt とは

`llms.txt` は、AIツール（LLM: Large Language Model）がリポジトリの構造と内容を理解するための**索引ファイル**です。

### 目的

1. **構造の可視化**: ディレクトリ構造を明示
2. **主要ファイルへの案内**: 重要なガイドラインへの直接リンク
3. **使用方法の説明**: AI ツールごとの参照方法を提示
4. **メタ情報の提供**: リポジトリの目的・連絡先・ライセンス等

## 🔄 更新タイミング

以下のいずれかの変更があった場合、`llms.txt` を更新してください：

### 必須の更新

- ✅ ディレクトリ構造の変更（追加・削除・移動）
- ✅ 主要ガイドラインの追加・削除
- ✅ ファイルパスの変更
- ✅ リポジトリの目的の変更

### 推奨される更新

- 📝 新しい言語・フレームワークのサポート追加
- 📝 使用方法の変更（新しいAIツールのサポート等）
- 📝 連絡先情報の更新
- 📝 ブランチ戦略の変更

### 更新不要

- ❌ ガイドライン**内容**の細かい修正
- ❌ サンプルコードの追加・修正（構造変更を伴わない場合）
- ❌ README の説明の改善（llms.txt に影響しない場合）

## 📝 更新手順

### 1. 手動更新（推奨）

```bash
# 1. llms.txt を編集
vim llms.txt
# または
code llms.txt

# 2. 以下のセクションを確認・更新
#    - Directory Structure
#    - Core Guidelines
#    - Training Samples
#    - Last Updated 日付

# 3. 変更をコミット
git add llms.txt
git commit -m "docs: update llms.txt - [変更内容]"
```

### 2. AI による更新（実験的）

Claude や ChatGPT に以下を依頼できます：

```
以下のディレクトリ構造を基に、llms.txt の "Directory Structure" セクションを更新してください：

[tree コマンドの出力を貼り付け]
```

**注意**: AI が生成した内容は必ず人間がレビューしてください。

## ✅ 品質チェックリスト

llms.txt を更新した際は、以下をチェックしてください：

### 構造の正確性

- [ ] ディレクトリ構造が最新の状態を反映している
- [ ] 存在しないファイルへのリンクがない
- [ ] すべての主要ガイドラインへのリンクが機能する

### 内容の完全性

- [ ] 新しく追加されたガイドラインが記載されている
- [ ] 削除されたガイドラインへの参照が削除されている
- [ ] "Last Updated" 日付が正しい

### 可読性

- [ ] セクションが適切に整理されている
- [ ] 説明が簡潔で明確
- [ ] Markdown フォーマットが正しい

### AI ツール対応

- [ ] 主要な AI ツールの使用方法が記載されている
- [ ] ファイルパスが相対パスで正しく記載されている
- [ ] 階層構造が理解しやすい

## 📂 llms.txt の構造

### 必須セクション

1. **Overview**
   - リポジトリの基本情報
   - 最終更新日

2. **Directory Structure**
   - ツリー形式のディレクトリ構造
   - 各フォルダの簡潔な説明

3. **Core Guidelines**
   - 主要なガイドラインへのリンク
   - 各ガイドラインの簡潔な説明

4. **Training Samples**
   - サンプルコードへのリンク
   - 各サンプルの説明

5. **Usage in AI Tools**
   - AI ツールごとの参照方法
   - 具体的なコマンド例

### オプションセクション

- **Installation**: インストール方法
- **Updates**: 更新方法
- **Branch Strategy**: ブランチ戦略
- **Technical Details**: 技術的な詳細
- **Future Plans**: 今後の予定
- **Contact**: 連絡先

## 🎨 フォーマット規約

### ディレクトリ構造の表記

```
# Good: 明確な階層と説明
ai-generation-guidelines/             # AI生成用ガイドライン
  ├─ fundamentals/                    # 基本原則（言語非依存）
  ├─ by-language/                     # 言語別ガイドライン
  │  ├─ csharp/
  │  └─ powershell/

# Bad: 説明なし、階層が不明確
ai-generation-guidelines
fundamentals
by-language
csharp
powershell
```

### リンクの表記

```
# Good: 相対パス + 説明
- [xUnit Test Guidelines](ai-generation-guidelines/by-language/csharp/test-generation/xunit.md)
  - テスト命名規則
  - partial class 設計

# Bad: 説明なし、絶対パス
- [xUnit](https://github.com/yuu-git/ateliers-ai-assistants/blob/master/ai-generation-guidelines/by-language/csharp/test-generation/xunit.md)
```

### セクションの順序

1. Overview（概要）
2. Installation（インストール）
3. Updates（更新方法）
4. Directory Structure（構造）
5. Core Guidelines（ガイドライン）
6. Training Samples（サンプル）
7. Usage（使用方法）
8. 技術情報・今後の予定
9. Contact（連絡先）

## 🤖 AI による自動更新（将来）

将来的に、GitHub Actions で以下を自動化する予定です：

```yaml
# .github/workflows/update-llms-txt.yml（構想）
name: Update llms.txt

on:
  push:
    paths:
      - 'ai-generation-guidelines/**'
      - 'ai-training-samples/**'

jobs:
  update-llms:
    runs-on: ubuntu-latest
    steps:
      - name: Generate directory tree
      - name: Update llms.txt with AI
      - name: Create PR
```

## 📞 質問・提案

llms.txt の構造や内容について質問・提案がある場合：

- GitHub Issues で提案
- Pull Request で改善案を提出

## 🔗 関連ドキュメント

- [README.md](https://github.com/yuu-git/ateliers-knowledge/blob/master/README.md): リポジトリの基本情報
- [llms.txt](https://github.com/yuu-git/ateliers-knowledge/blob/master/llms.txt): 実際のファイル
- [Image Management](../../guidelines/image-management-guide): 画像ファイル管理ガイドライン

---

*Last Updated: 2025-11-15*  
*このガイドライン自体も改善が必要な場合は、遠慮なくPull Requestを送ってください*