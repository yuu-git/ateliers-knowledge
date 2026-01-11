# ateliers-knowledge プロジェクトについて

## 概要

**ateliers-knowledge** は、Ateliers プロジェクト全体のナレッジベースリポジトリです。

AI 駆動開発（AI-Driven Development）とドキュメント駆動開発（Documentation-Driven Development）を支援するため、以下のコンテンツを提供します：

- **AI 生成ガイドライン**: GitHub Copilot、Cursor、Claude などの AI ツールで参照可能なコード生成・テスト生成ガイドライン
- **開発ガイドライン**: プロジェクト横断的なコーディング規約、GitHub 運用方針など
- **ベストプラクティス**: 実践的なハウツーと技術解説
- **トレーニングサンプル**: AI の学習用コードサンプル

## リポジトリの特徴

- **テキストベース**: 実行可能なコードではなく、マークダウン形式のドキュメント
- **サブモジュール設計**: Git サブモジュールとして各プロジェクトから参照
- **AI 最適化**: LLM が読み取りやすい構造と `llms.txt` による統合
- **Docusaurus 対応**: 静的サイト生成による技術文書の公開

## 変更履歴

- **2024年初期**: `Ateliers.Core.AIAssistant` として開始（GitHub Copilot 専用）
- **2025年11月**: AI ツール非依存・多言語対応の構造に全面改訂
- **2026年1月**: `ateliers-knowledge` にリネーム、スコープを開発ガイドライン全般に拡大

## 変更履歴

- **2024年初期**: `Ateliers.Core.AIAssistant` として開始（GitHub Copilot 専用）
- **2025年11月**: AI ツール非依存・多言語対応の構造に全面改訂
- **2026年1月**: `ateliers-knowledge` にリネーム、スコープを開発ガイドライン全般に拡大

## 現在のディレクトリ構造

```
ateliers-knowledge/
├─ ai-generation-guidelines/     # AI 生成用ガイドライン
│  ├─ fundamentals/              # 基本原則
│  ├─ by-language/               # 言語別（C#, PowerShell, YAML, Lua）
│  ├─ by-framework/              # フレームワーク別
│  ├─ by-tool/                   # ツール別
│  └─ meta/                      # メタガイドライン
│
├─ ai-training-samples/          # AI トレーニング用サンプル
│  └─ codes/                     # コードサンプル
│
├─ tool-specific/                # ツール固有設定
├─ scripts/                      # セットアップスクリプト
├─ .github/workflows/            # GitHub Actions
└─ docs/                         # プロジェクト文書
```

## 今後追加予定の構造

```
ateliers-knowledge/
├─ guidelines/                   # 開発ガイドライン（追加予定）
│  ├─ development/              # GitHub 運用、コーディング規約
│  ├─ versioning/               # バージョニング戦略
│  └─ architecture/             # アーキテクチャ指針
│
└─ practices/                    # ベストプラクティス（追加予定）
   ├─ dotnet/                   # .NET 関連
   ├─ git/                      # Git 運用
   └─ ai/                       # AI 活用方法
```

## 使用方法

### サブモジュールとして追加

```bash
git submodule add https://github.com/yuu-git/ateliers-knowledge.git .submodules/ateliers-knowledge
```

### AI ツールでの参照

**Cursor / Cline**:
```
@Docs .submodules/ateliers-knowledge/llms.txt
```

**GitHub Copilot**:
サブモジュール内のファイルを開くことでコンテキストとして認識されます。

## 関連リンク

- **Repository**: https://github.com/yuu-git/ateliers-knowledge
- **License**: MIT License
- **主な用途**: Git サブモジュール、AI ツールのコンテキスト、Docusaurus 統合
