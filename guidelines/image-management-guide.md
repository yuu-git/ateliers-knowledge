---
title: 画像ファイル管理ガイドライン
sidebar_label: 画像管理ガイドライン
tags: [Image, Management, Guidelines]
---

# 画像ファイルの管理方針

## 基本ルール

### 1. 固有の画像：`img/` フォルダ

特定のドキュメント専用の画像は、そのドキュメントと同じ階層に `img/` フォルダを作成して配置します。

```
guidelines/
  development/
    github/
      repository-naming-policy.md
      img/                           ← 固有画像
        naming-example-01.png
        naming-example-02.png
```

**マークダウンでの参照：**

```markdown
![命名例1](img/naming-example-01.png)
```

### 2. 共通の画像：`.assets/` フォルダ

複数のドキュメントから参照される画像は、リポジトリルート直下の `.assets/` フォルダに配置します。

```
.assets/
  ├─ icons/          # アイコン画像
  ├─ diagrams/       # 図表・ダイアグラム
  ├─ screenshots/    # スクリーンショット
  └─ logos/          # ロゴ画像
```

**マークダウンでの参照：**

```markdown
<!-- 相対パス -->
![ブランチ戦略](../../.assets/diagrams/branch-strategy.png)

<!-- ルート相対パス（Docusaurus） -->
![ブランチ戦略](/.assets/diagrams/branch-strategy.png)
```

## ファイル命名規則

- **形式**: ケバブケース（小文字 + ハイフン）
  - ✅ `branch-strategy-diagram.png`
  - ❌ `BranchStrategyDiagram.png`
  - ❌ `branch_strategy_diagram.png`

- **連番**: 必要に応じて末尾に `-01`, `-02` を追加
  - `naming-example-01.png`
  - `naming-example-02.png`

## 推奨画像形式

- **PNG**: 図表、スクリーンショット（可逆圧縮）
- **JPEG**: 写真（非可逆圧縮）
- **SVG**: ベクター画像（ロゴ、アイコン）
- **WebP**: 最新フォーマット（サポート確認が必要）

## 画像サイズガイドライン

- **幅**: 通常 1200px 以下（Web 表示に最適化）
- **ファイルサイズ**: 500KB 以下を推奨
- **高解像度**: 必要な場合のみ、Retina 対応 (@2x)

## 整理のルール

1. **固有 or 共通の判断**
   - 1つのドキュメントでのみ使用 → `img/`
   - 2つ以上のドキュメントで使用 → `.assets/`

2. **サブディレクトリの活用**
   - `.assets/` 内は用途別に分類
   - `img/` 内は基本的にフラット構造

3. **定期的な見直し**
   - 未使用画像の削除
   - 固有→共通への昇格検討

## 例

### 固有画像の例

```
guidelines/
  development/
    coding/
      csharp/
        names-of-namespaces.md
        img/
          namespace-structure-example.png
```

### 共通画像の例

```
.assets/
  diagrams/
    branch-strategy-overview.png      ← 複数ドキュメントから参照
    release-flow-diagram.png
```

---

*Last Updated: 2026-01-11*
