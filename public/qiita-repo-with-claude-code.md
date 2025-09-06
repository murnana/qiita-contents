---
title: Qiita記事管理リポジトリーに、Claude Codeを入れてみた
tags:
  - Claude
  - VSCode
  - DevContainer
  - QiitaCLI
  - AI
private: false
updated_at: ''
id: 139153e443941490223f
organization_url_name: null
slide: false
ignorePublish: false
---

私はQiita記事をGitHubリポジトリーで管理しています。
ついに Claude Proに課金したので、試しにQiitaの記事執筆環境に Claude Codeを導入しました。

## 開発環境
<!-- textlint-disable ja-technical-writing/ja-no-mixed-period -->
【ホストOS】
- Windows 11 Home 24H2 (ビルド 26000.4646)
- Intel Core i7-13700F、32GB RAM

【開発ツール】
- Docker 28.3.3 + DevContainer (WSL2)
- VS Code 1.103.2 + Claude Code 1.0.108
- Node.js 20.19.2、Qiita CLI 1.6.2
- textlint 13.4.1 (日本語の技術文書プリセット)

【リポジトリー】
- https://github.com/murnana/qiita-contents
<!-- textlint-enable ja-technical-writing/ja-no-mixed-period -->

## 導入

今回の導入手順です。

### 1. DevContainer環境でQiita CLIを動かす環境を整備

こちらは以前に構築済みのもので、詳細な手順は省略します。

### 2. Claude CodeをDevContainer上でインストール

[Claude Code overview - Anthropic](https://docs.anthropic.com/en/docs/claude-code/overview) の指示に従い、以下コマンドでインストールします。

```bash
npm install -g @anthropic-ai/claude-code
```

### 3. コマンドの実行

ターミナルで `claude` コマンドを実行し、初期セットアップを進めます。

### 4. DevContainer設定の更新

以後のコンテナ再構築時に自動でインストールされるよう、_.devcontainer/devcontainer.json_ ファイルの `postCreateCommand` に上記コマンドを追加します。

## Claude Code のためのセットアップ
Claude Codeはリポジトリーを解析して、_CLAUDE.md_ ファイルを自動生成できます。
最初は英語で作成されますが、日本語にするよう指示すると日本語版に変更してくれます。

このファイルはClaude Codeとのやり取りの際に、事前に読み込まれるプロンプトのような役割を果たします。

当初はClaude Codeが生成したままの内容にしておきました。
実際に使用していく中で、必要に応じて修正していく予定です。

生成されたCLAUDE.mdファイルの内容は、以下のGitHubリンクで確認できます。

https://github.com/murnana/qiita-contents/blob/45e82552cb0c1e1f5f346e470341be0e39dc721b/CLAUDE.md


## お試し - README.mdを作成する
恥ずかしながら、リポジトリーには _README.md_ ファイルが用意されていませんでした。
せっかくの機会なので、Claude Codeに作成を依頼しました。

英語での作成を指示した結果、英語版のREADMEが生成されました。

ただし初稿では `QIITA_TOKEN` を環境変数に設定するよう記載されていました。
よく考えてみると、コンテナを再構築するたびにQiitaトークンが消えてしまう問題がありました。
そのため、README.mdの執筆と並行して、ワークスペース内で認証情報を永続化する工夫も追加しました。

その解決方法についてもClaude Codeと一緒に検討しました。
このように、AIとのコーディングはペアプログラミングのような感覚で、非常に勉強になる体験でした。

## まとめ

Claude Codeを導入し、セットアップもClaude Codeと共に行いました。新しいアプローチや解決方法が見つかり、AIの提案から問題点も発見しました。

今回この記事も Claude Codeとともに書いています。
ベースとなる文章は書いていますが、textlintだけではカバーできなかった文章校正も行ってくれて助かっています。

この記事を読んでくださり、ありがとうございました。
今後も記事を書く際に活用していく予定です。
