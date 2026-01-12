---
title: C# 用語・表記規則
sidebar_label: 用語・表記規則
tags: [C#, Terminology, Naming, Conventions]
description: C# プロジェクトにおける用語の表記ゆれを防ぐための規則
---

# C# 用語・表記規則

*作成: 2025/01/27*

C# プロジェクトにおける用語の表記ゆれを防ぐための規則について記載します。

## 注意事項

この記事は、個人的な技術利用方針であり、推奨事項やベストプラクティスの主張ではございません。  
個人的なアプローチ例の紹介であり、すべてのプロジェクトや環境に最適とは限りませんが、参考にしていただけると幸いです。

## 1. 基本方針

C# の PascalCase 命名規則により、同じ概念を表す単語でも動詞形・名詞形で表記が異なることがあります。  
プロジェクト全体で一貫性を保つため、以下の規則に従って用語を使い分けます。

## 2. 動詞と名詞の使い分け

### 2.1. Generate / Generation

| 形式 | 推奨表記 | 用途 | 理由 |
|------|----------|------|------|
| 動詞 | `Generate` | メソッド名 | 「～を生成する」という動作を表す |
| 動詞 | `Generate` | インターフェース名 | 「～する能力」を表す (例: `IGenerateVoiceService`) |
| 名詞 | `Generation` | クラス名（処理・サービス） | 「生成処理」という概念を表す |
| 名詞 | `Generation` | オプション・設定クラス | 「生成に関する設定」を表す |
| 名詞 | `Generation` | リクエスト・レスポンス | 「生成のための～」を表す |

#### 2.1.1. 判断基準

**✅ Generate を使う場合：**
- メソッドが「～を生成する」という動作を直接表現する
- インターフェース名で「～する能力・責務」を表現する
- 動詞として明確に機能する場面

**✅ Generation を使う場合：**
- 「生成処理」という概念・プロセス全体を表すクラス名
- 生成に関する設定やオプションを保持するクラス
- 生成のためのリクエストやレスポンスを表すクラス
- 名詞として機能する場面

#### 2.1.2. 実装例

```csharp
// ✅ 推奨パターン

// インターフェース名：動詞 Generate を使用（～する能力を表す）
public interface IGenerateVoiceService
{
    // メソッド名：動詞 Generate を使用（～を生成するという動作）
    Task<string> GenerateVoiceFileAsync(IGenerateVoiceRequest request);
    Task<IReadOnlyList<string>> GenerateVoiceFilesAsync(IEnumerable<IGenerateVoiceRequest> requests);
}

// リクエストクラス：名詞 Generation を使用（生成のためのリクエスト）
public interface IGenerateVoiceRequest
{
    string Text { get; }
    string OutputWavFileName { get; }
    IVoiceGenerationOptions? Options { get; }
}

// オプションクラス：名詞 Generation を使用（生成に関する設定）
public class VoiceGenerationOptions
{
    public int? StyleId { get; set; }
    public float? SpeedScale { get; set; }
    public float? PitchScale { get; set; }
}

// サービス実装クラス：名詞 Generation を使用（生成処理を表す）
public class VoiceGenerationService : IGenerateVoiceService
{
    public async Task<string> GenerateVoiceFileAsync(IGenerateVoiceRequest request)
    {
        // 実装
    }
}

// 内部リクエストクラス：名詞 Generation は使わず Generate を使用
// （動詞的な性質が強い場合）
public class VoicevoxGenerateRequest
{
    public string Text { get; set; }
    public VoicevoxGenerateOptions? Options { get; set; }
}

// ❌ 非推奨パターン

// インターフェース名に名詞形を使用するのは不自然
public interface IGenerationVoiceService { }

// メソッド名に名詞形を使用するのは不自然
public Task<string> GenerationVoiceFileAsync() { }

// オプションクラスに動詞形を使用するのは不自然
public class VoiceGenerateOptions { }
```

#### 2.1.3. 混在パターンの例

実際のコードでは、コンテキストによって使い分けることがあります：

```csharp
// Voicevox 固有の「生成リクエスト」
// → "Generate" を使用（ライブラリ内部の命名に合わせる）
var voicevoxRequest = new VoicevoxGenerateRequest
{
    Text = request.Text,
    OutputWavFileName = request.OutputWavFileName,
    Options = ConvertOptions(request.Options)
};

// 汎用的な「生成オプション」インターフェース
// → "Generation" を使用（概念を表す）
public interface IVoiceGenerationOptions
{
    int? StyleId { get; }
    float? SpeedScale { get; }
}
```

このように、外部ライブラリや既存コードとの整合性を保つために、
一部で例外的に異なる表記を使用することは許容されます。

### 2.2. その他の動詞・名詞ペア

今後、同様の表記ゆれが発見された場合、ここに追記します。

| 動詞形 | 名詞形 | 備考 |
|--------|--------|------|
| `Generate` | `Generation` | 生成 |
| （将来追加） | （将来追加） | - |

## 3. 命名規則の例外ケース

以下の場合は、上記の規則の例外として扱います：

1. **外部ライブラリとの整合性**
   - 依存する外部ライブラリの命名規則に合わせる必要がある場合
   - 例：`VoicevoxGenerateRequest` (Voicevox ライブラリの命名に従う)

2. **既存コードの大規模な変更を避ける場合**
   - 既に広く使用されているコードの命名を変更すると影響が大きい場合
   - ただし、新規作成時は本規則に従うこと

3. **ドメイン固有の用語**
   - 業界標準やドメイン知識として確立された用語がある場合

## 4. 関連ドキュメント

- [C# 名前空間の命名方針](./names-of-namespaces.md)
- [GitHub リポジトリ命名方針](../../github/repository-naming-policy.md)
- [C# コーディングガイドライン](./README.md)
