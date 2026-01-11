# ateliers-knowledge プロジェクトについて

Ateliers.Core.AIAssistant プロジェクトは、AIアシスタントの機能を提供するためのプロジェクトです。

どちらかというと、コードベースではなく、テキストベースのコンテンツが中心となります。

## 1. フォルダ構成

Ateliers.Core.AIAssistant  
┣ AITrainingSamples  
┃ ┗ Csharp  
┗ GitHubCopilot  
　┣ CodeGenGuidelines  
　┣ CodeReviewGuidelines  
　┣ PromptGuidelines  
　┣ PromptManuals  
　┣ TestGenGuidelines  
　┗ TestReviewGuidelines  

### 1.1 フォルダ構成の説明

* AITrainingSamples  
  AIの学習用のサンプルを格納するフォルダです。コードやテストの生成に使用するためのサンプルコードやテンプレートを格納します。
  * Csharp フォルダには、C#言語のサンプルやテンプレートを格納します。

* GitHubCopilot  
  GitHub Copilot のためのガイドラインを格納するフォルダです。
  * CodeGenGuidelines フォルダには、GitHub Copilot がコード生成に使用するガイドラインテキストを格納します。
  * CodeReviewGuidelines フォルダには、GitHub Copilot がコードレビューに使用するガイドラインテキストを格納します。
  * PromptGuidelines フォルダには、GitHub Copilot のプロンプトのベストプラクティスや範例を格納します。
  * PromptManuals フォルダには、コードやテストの自動生成するための手順や方法を記したマニュアルを格納します。
  * TestGenGuidelines フォルダには、GitHub Copilot がテスト生成に使用するガイドラインテキストを格納します。
  * TestReviewGuidelines フォルダには、GitHub Copilot がテストレビューに使用するガイドラインテキストを格納します。

## 特記事項

2024/03/17 時点で GitHub Copilot は、コンテキストを解釈してのコードレビューやテストレビューには対応していません。  
そのため、CodeReviewGuidelines フォルダや TestReviewGuidelines フォルダには、GitHub Copilot がコードレビューやテストレビューに使用するガイドラインテキストを格納していますが、今現在は GitHub Copilot がコード生成に使用するガイドラインテキストのみが有効です。

たぶんそのうちできるようになるでしょう。
