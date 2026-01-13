# ateliers-knowledge

**Ateliers プロジェクト全体のナレッジベース - AI駆動開発とドキュメント駆動開発を支える知識リポジトリ**

このリポジトリは、AI による自動コード生成・学習のためのガイドラインとサンプル、および Ateliers プロジェクト全体の開発ガイドライン・ベストプラクティスを提供します。

## 📖 このリポジトリについて

### 目的

1. **AI 駆動開発の支援**: GitHub Copilot、Cursor、Claude などの AI ツールで参照可能なガイドライン
2. **ドキュメント駆動開発**: プロジェクト横断的な開発方針・ガイドラインの一元管理
3. **再利用可能なナレッジ**: サブモジュールとして各プロジェクトから参照可能
4. **Docusaurus 統合**: 静的サイト生成による技術文書の公開

### 特徴

- ✅ **テキストベース**: 実行可能なコードではなく、マークダウン形式のガイドライン
- ✅ **AI 最適化**: LLM が読み取りやすい構造と `llms.txt` による統合
- ✅ **サブモジュール設計**: Git サブモジュールとして各プロジェクトに組み込み
- ✅ **多言語対応**: C#、PowerShell、YAML、Lua など複数言語をサポート
- ✅ **画像管理**: 固有画像は `img/`、共通画像は `.assets/` で整理

## 📦 インストール方法

### 🚀 ワンライナー（推奨）

最も簡単な方法です。1コマンドでセットアップが完了します。

**bash:**
```bash
curl -fsSL https://raw.githubusercontent.com/yuu-git/ateliers-knowledge/master/scripts/init-for-project.sh | bash
```

**PowerShell:**
```powershell
irm https://raw.githubusercontent.com/yuu-git/ateliers-knowledge/master/scripts/init-for-project.ps1 | iex
```

このスクリプトは以下を自動実行します：
- ✅ サブモジュールの追加
- ✅ masterブランチへの切り替え
- ✅ 更新スクリプトのコピー
- ✅ GitHub Actions の設定（オプション）

### 🔧 手動セットアップ

詳細な制御が必要な場合は手動でセットアップできます。

```bash
# 1. サブモジュールとして追加
git submodule add https://github.com/yuu-git/ateliers-knowledge.git .submodules/ateliers-knowledge

# 2. サブモジュールを初期化
git submodule update --init --recursive

# 3. masterブランチに切り替え
cd .submodules/ateliers-knowledge
git checkout master
git pull origin master
cd ../..

# 4. 更新スクリプトをコピー（オプション）
mkdir -p scripts
cp .submodules/ateliers-knowledge/scripts/update-ateliers-knowledge.sh scripts/
cp .submodules/ateliers-knowledge/scripts/update-ateliers-knowledge.ps1 scripts/
chmod +x scripts/update-ateliers-knowledge.sh
```

## 🔄 更新方法

### 方法1：手動更新スクリプト

必要な時に手動で更新します。

**bash:**
```bash
./scripts/update-ateliers-knowledge.sh
```

**PowerShell:**
```powershell
.\scripts\update-ateliers-knowledge.ps1
```

### 方法2：GitHub Actions（自動更新）

毎日9時に自動で更新されます。

```bash
# ワークフローファイルをコピー
mkdir -p .github/workflows
cp .submodules/ateliers-knowledge/.github/workflows/update-ateliers-knowledge.yml .github/workflows/
```

手動実行も可能：
1. GitHub リポジトリの「Actions」タブを開く
2. 「Update Ateliers Knowledge」を選択
3. 「Run workflow」をクリック

### 方法3：直接コマンド

サブモジュールディレクトリで直接実行します。

```bash
cd .submodules/ateliers-knowledge
git checkout master
git pull origin master
cd ../..
```

## 🤖 AI ツールでの使用方法

### Cursor / Cline

```
@Docs .submodules/ateliers-knowledge/llms.txt
```

または、GitHub上のファイルを直接参照：

```
https://raw.githubusercontent.com/yuu-git/ateliers-knowledge/master/llms.txt
```

### GitHub Copilot

`.submodules/ateliers-knowledge` 内のファイルを開くことでコンテキストとして認識されます。

主要ファイル：
- `ai-generation-guidelines/by-language/csharp/test-generation/xunit.md`
- `ai-training-samples/codes/csharp/common-patterns.md`
- `ai-training-samples/codes/csharp/linq-patterns.md`

### Claude

会話の最初に以下を貼り付けてください：

```
このリポジトリのガイドラインに従ってください：
https://raw.githubusercontent.com/yuu-git/ateliers-knowledge/master/llms.txt
```

## 📚 コンテンツ

### 現在のコンテンツ

#### 開発ガイドライン（`guidelines/development/`）

プロジェクト横断的な開発ガイドライン：

**GitHub 運用**:
- [リポジトリ命名方針](guidelines/development/github/repository-naming-policy.md)
- [ドキュメント作成ガイド](guidelines/development/github/writing-guide.md)
- [CI/CD ガイドライン](guidelines/development/github/cicd/README.md)

**C# コーディング規約**:
- [名前空間の命名方針](guidelines/development/coding/csharp/names-of-namespaces.md)

#### AI 生成ガイドライン（`ai-generation-guidelines/`）

AI による自動コード生成のためのガイドライン集：

**テスト生成（最重要）**:
- [xUnit Test Guidelines](ai-generation-guidelines/by-language/csharp/test-generation/xunit.md)
  - テスト命名規則（`TESTNAME_XXX_XXXXX` 形式）
  - partial class による自動生成と手動テストの共存
  - nameof() を使用したリファクタリング対応
  - カバレッジ 100% を目指すテスト観点

**コードレビュー**:
- [Code Quality Principles](ai-generation-guidelines/fundamentals/code-quality-principles.md): 全コードに適用される基本原則
- [ValueObject Review Guidelines](ai-generation-guidelines/by-language/csharp/code-review/value-object.md): DDD 値オブジェクト向けレビュー基準

#### AI トレーニングサンプル（`ai-training-samples/`）

AI の学習用コードサンプル：
- [Common Patterns](ai-training-samples/codes/csharp/common-patterns.md): C# の基本パターン
- [LINQ Patterns](ai-training-samples/codes/csharp/linq-patterns.md): LINQ の推奨パターン
- [DateTime Extensions Example](ai-training-samples/codes/csharp/datetime-extensions.md): 実装とテストの完全なサンプル

### 今後追加予定のコンテンツ

#### 開発ガイドラインの拡充
- バージョニング戦略
- アーキテクチャ指針
- コミットメッセージ規約
- C# 例外設計・LINQ ガイドライン

#### ベストプラクティス（`practices/`）

実践的なハウツーと技術解説：
- .NET GitHub Actions 設定
- HTTP プロトコル基礎
- Git運用テクニック

## 📂 ディレクトリ構造

```
ateliers-knowledge/
├─ scripts/                              # セットアップ・更新スクリプト
│  ├─ init-for-project.sh               # 初回セットアップ (bash)
│  ├─ init-for-project.ps1              # 初回セットアップ (PowerShell)
│  ├─ update-ateliers-knowledge.sh      # 手動更新 (bash)
│  └─ update-ateliers-knowledge.ps1     # 手動更新 (PowerShell)
│
├─ .github/workflows/                    # GitHub Actions
│  └─ update-ateliers-knowledge.yml     # 自動更新ワークフロー
│
├─ ai-generation-guidelines/             # AI 生成用ガイドライン
│  ├─ fundamentals/                     # 基本原則（言語非依存）
│  │  ├─ naming-conventions.md
│  │  ├─ documentation-standards.md
│  │  └─ code-quality-principles.md
│  │
│  ├─ by-language/                      # 言語別ガイドライン
│  │  ├─ csharp/                       # C#
│  │  │  ├─ test-generation/
│  │  │  │  └─ xunit.md
│  │  │  ├─ code-review/
│  │  │  │  └─ value-object.md
│  │  │  └─ patterns/
│  │  │
│  │  ├─ powershell/                   # PowerShell
│  │  ├─ yaml/                         # YAML
│  │  └─ lua/                          # Lua
│  │
│  ├─ by-framework/                     # フレームワーク別
│  │  ├─ dotnet/
│  │  ├─ react/
│  │  └─ vue/
│  │
│  ├─ by-tool/                          # ツール別
│  │  ├─ git/
│  │  ├─ docker/
│  │  └─ ci-cd/
│  │
│  └─ meta/                             # メタガイドライン
│     └─ llms-txt/
│        └─ maintenance.md             # llms.txt メンテナンスガイド
│
├─ ai-training-samples/                  # AI トレーニング用サンプル
│  └─ codes/                            # コードサンプル
│     ├─ csharp/
│     │  ├─ common-patterns.md
│     │  ├─ linq-patterns.md
│     │  └─ datetime-extensions.md
│     ├─ powershell/
│     ├─ yaml/
│     └─ lua/
│
├─ guidelines/                           # 開発ガイドライン
│  └─ development/                      # 開発方針
│     ├─ github/                       # GitHub 運用
│     │  ├─ repository-naming-policy.md
│     │  ├─ writing-guide.md
│     │  └─ cicd/                      # CI/CD ガイドライン
│     │     ├─ README.md
│     │     ├─ submodules-auto-sync.md
│     │     └─ project-knowledge-sync.md
│     ├─ coding/                       # コーディング規約
│     │  └─ csharp/                    # C# 規約
│     └─ project/                      # プロジェクト管理
│
├─ tool-specific/                        # ツール固有設定
│  ├─ github-copilot/
│  ├─ cursor/
│  └─ claude/
│
├─ .assets/                              # 共通画像（複数ドキュメントから参照）
│  ├─ icons/                            # アイコン
│  ├─ diagrams/                         # 図表
│  ├─ screenshots/                      # スクリーンショット
│  └─ logos/                            # ロゴ
│
├─ README.md                             # このファイル
├─ llms.txt                              # AI 向けコンテキスト
└─ LICENSE.txt                           # MIT ライセンス
```

### 今後追加予定の構造

```
ateliers-knowledge/
├─ guidelines/                           # 開発ガイドライン（追加予定）
│  ├─ development/                      # 開発方針
│  │  ├─ github/                       # GitHub 運用
│  │  └─ coding/                       # コーディング規約
│  ├─ versioning/                       # バージョニング戦略
│  └─ architecture/                     # アーキテクチャ指針
│
└─ practices/                            # ベストプラクティス（追加予定）
   ├─ dotnet/                           # .NET 関連
   ├─ git/                              # Git運用
   └─ ai/                               # AI 活用方法
```

## 🌿 ブランチ戦略

- **master**: 安定版（推奨）
- **develop**: 開発版

## ⚙️ 技術詳細

- **Primary Language**: C# (他言語も追加可能)
- **Test Framework**: xUnit
- **Design Approach**: Domain-Driven Design (DDD) 対応
- **AI Tools Supported**: GitHub Copilot / Cursor / Claude / Cline / その他LLM

## 🔮 今後の予定

### コンテンツ追加

#### 開発ガイドラインの統合
- ateliers-dev からの `development-guidelines` 移行
- GitHub 運用ガイドライン（リポジトリ命名、ブランチ戦略）
- C# コーディング規約（名前空間、例外設計）
- バージョニング戦略

#### ベストプラクティスの充実
- .NET GitHub Actions パターン集
- HTTP プロトコル詳細解説
- Git タグ運用ガイド

#### 多言語対応の拡張
- **JavaScript/TypeScript**: React、Node.js のパターン
- **Python**: pytest を使用したテストパターン

### Docusaurus 統合
- サブモジュールとして ateliers-dev から参照
- 静的サイトとしての技術文書公開

## 📞 Contact

- GitHub: [@yuu-git](https://github.com/yuu-git)
- Repository: https://github.com/yuu-git/ateliers-knowledge

## 📝 Notes

- このリポジトリは **テキストベース** であり、実行可能なコードは含まれません
- パッケージではなく **サブモジュール** として利用することを想定しています
- AI ツールが `.md` ファイルを直接参照できるように設計されています
- 2025年11月: AI ツール非依存・多言語対応の構造に全面改訂
- 2026年1月: `ateliers-knowledge` にリポジトリ名変更、開発ガイドラインの統合開始

## 📄 License

MIT License - see [LICENSE.txt](LICENSE.txt)

---

*Last Updated: 2026-01-11*  
*Repository: ateliers-knowledge (formerly ateliers-ai-assistants)*  
*Purpose: AI-driven & Documentation-driven development knowledge base*