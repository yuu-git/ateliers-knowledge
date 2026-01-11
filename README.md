# ateliers-knowledge

このリポジトリは、AIによる自動コード生成や学習を行うための資材を提供するプロジェクトです。  
コードベースではなく、テキストベースのリポジトリになります。

## 📦 インストール方法

### 🚀 ワンライナー（推奨）

最も簡単な方法です。1コマンドでセットアップが完了します。

```bash
curl -fsSL https://raw.githubusercontent.com/yuu-git/ateliers-knowledge/master/scripts/init-for-project.sh | bash
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
cp .submodules/ateliers-knowledge/scripts/update-ai-guidelines.sh scripts/
chmod +x scripts/update-ai-guidelines.sh
```

## 🔄 更新方法

### 方法1：手動更新スクリプト

必要な時に手動で更新します。

```bash
./scripts/update-ai-guidelines.sh
```

### 方法2：GitHub Actions（自動更新）

毎週月曜日9時に自動で更新されます。

```bash
# ワークフローファイルをコピー
mkdir -p .github/workflows
cp .submodules/ateliers-knowledge/.github/workflows/update-ai-guidelines.yml .github/workflows/
```

手動実行も可能：
1. GitHub リポジトリの「Actions」タブを開く
2. 「Update AI Guidelines」を選択
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

### AI生成ガイドライン

#### テスト生成（最重要）

- [xUnit Test Guidelines](ai-generation-guidelines/by-language/csharp/test-generation/xunit.md)
  - テスト命名規則（`TESTNAME_XXX_XXXXX`形式）
  - partial class による自動生成と手動テストの共存
  - nameof() を使用したリファクタリング対応
  - カバレッジ100%を目指すテスト観点

#### コードレビュー（参考用）

**注意**: 2024/03/17時点でGitHub Copilotはコードレビューガイドラインをまだサポートしていません

- [Code Quality Principles](ai-generation-guidelines/fundamentals/code-quality-principles.md): 全コードに適用される基本原則
- [ValueObject Review Guidelines](ai-generation-guidelines/by-language/csharp/code-review/value-object.md): DDD値オブジェクト向けレビュー基準

### AIトレーニングサンプル

#### C# コードパターン

- [Common Patterns](ai-training-samples/codes/csharp/common-patterns.md): C#の基本パターン（null チェック等）
- [LINQ Patterns](ai-training-samples/codes/csharp/linq-patterns.md): LINQの推奨パターン
- [DateTime Extensions Example](ai-training-samples/codes/csharp/datetime-extensions.md): 実装とテストの完全なサンプル

## 📂 ディレクトリ構造

```
ateliers-ai-assistants/
├─ scripts/                              # セットアップ・更新スクリプト
│  ├─ init-for-project.sh               # 初回セットアップ
│  └─ update-ai-guidelines.sh           # 手動更新
│
├─ .github/workflows/                    # GitHub Actions
│  └─ update-ai-guidelines.yml          # 自動更新ワークフロー
│
├─ ai-generation-guidelines/             # AI生成用ガイドライン
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
├─ ai-training-samples/                  # AIトレーニング用サンプル
│  ├─ codes/                            # コードサンプル
│  │  ├─ csharp/
│  │  │  ├─ common-patterns.md
│  │  │  ├─ linq-patterns.md
│  │  │  └─ datetime-extensions.md
│  │  ├─ powershell/
│  │  ├─ yaml/
│  │  └─ lua/
│  │
│  ├─ configs/                          # 設定ファイルサンプル（将来）
│  ├─ documents/                        # ドキュメントサンプル（将来）
│  └─ data/                             # データサンプル（将来）
│
├─ tool-specific/                        # ツール固有設定
│  ├─ github-copilot/
│  ├─ cursor/
│  └─ claude/
│
├─ docs/                                 # プロジェクト文書
│  ├─ project-info.md                   # プロジェクト詳細
│  └─ alternatives/                     # 代替方法の解説
│
├─ README.md                             # このファイル
├─ llms.txt                              # AI向けコンテキスト
└─ LICENSE.txt                           # MIT ライセンス
```

### 構造の特徴

#### 1. AI生成用ガイドライン（`ai-generation-guidelines/`）

- **fundamentals/**: 言語非依存の基本原則
- **by-language/**: 言語別のガイドライン（C#, PowerShell, YAML, Lua等）
- **by-framework/**: フレームワーク固有のガイドライン
- **by-tool/**: ツール固有のガイドライン（Git, Docker等）
- **meta/**: llms.txt などのメタ情報管理

#### 2. AIトレーニングサンプル（`ai-training-samples/`）

- **codes/**: プログラミング言語別のコードサンプル
- **configs/**: 設定ファイルのサンプル（将来追加予定）
- **documents/**: ドキュメントのサンプル（将来追加予定）
- **data/**: データファイルのサンプル（将来追加予定）

この構造により、新しい言語やツールの追加が容易になり、AIツールが目的のガイドラインを見つけやすくなっています。

## 🌿 ブランチ戦略

- **master**: 安定版（推奨）
- **develop**: 開発版

## ⚙️ 技術詳細

- **Primary Language**: C# (他言語も追加可能)
- **Test Framework**: xUnit
- **Design Approach**: Domain-Driven Design (DDD) 対応
- **AI Tools Supported**: GitHub Copilot / Cursor / Claude / Cline / その他LLM

## 🔮 今後の予定

以下のガイドラインは将来的に追加予定です：

### 言語別ガイドライン
- **JavaScript/TypeScript**: React, Node.js等のパターン
- **Python**: pytest を使用したテストパターン

### フレームワーク別ガイドライン
- **.NET**: ASP.NET Core, Blazor, Entity Framework
- **React**: コンポーネント設計、Hooks パターン

### ツール別ガイドライン
- **Git**: コミットメッセージ規約、ブランチ戦略
- **Docker**: Dockerfile ベストプラクティス
- **CI/CD**: GitHub Actions, Azure DevOps

## 📞 Contact

- GitHub: [@yuu-git](https://github.com/yuu-git)
- Repository: https://github.com/yuu-git/ateliers-knowledge

## 📝 Notes

- このリポジトリは**テキストベース**であり、実行可能なコードは含まれません
- パッケージではなく**サブモジュール**として利用することを想定しています
- AIツールが `.md` ファイルを直接参照できるように設計されています
- 2025年11月に大規模なディレクトリ構造の見直しを実施（AI非依存の設計）

## 📄 License

MIT License - see [LICENSE.txt](LICENSE.txt)

---

*Last Updated: 2025-11-15*  
*Structure: Multi-language AI-agnostic design*