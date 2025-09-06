# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## プロジェクト概要

Qiita CLIを使用して技術記事を管理・公開するQiitaコンテンツ管理リポジトリです。`public/`ディレクトリにMarkdownファイルとして記事を保存し、textlintを使用して日本語文章の校正を行います。

## 主要なコマンド

### テストとリント
- `npm test` - publicディレクトリ内の全てのMarkdownファイルに対してtextlintを実行
- `npx textlint "./public/*.md"` - Markdownコンテンツのリント処理を直接実行

### Qiita CLI操作
- `npx qiita login` - Qiitaへの認証（QIITA_TOKENが必要）
- `npx qiita preview` - 記事のローカルプレビュー（localhost:8888でサーバー起動）
- `npx qiita publish` - 記事をQiitaに公開
- `npx qiita pull` - Qiitaから最新の記事を取得
- `npx qiita new <filename>` - 新しい記事テンプレートを作成

### 開発環境
- DevContainerを使用した統一された開発環境を提供
- `npm ci` - 依存関係のインストール（CIではnpm installより推奨）

## アーキテクチャと構造

### 記事管理
- 記事は`public/`ディレクトリにMarkdownファイルとして保存
- 各記事のファイル名はQiita記事IDに対応
- 記事はYAMLフロントマターでQiita固有のメタデータ（タイトル、タグ、非公開ステータスなど）を使用
- `qiita.config.json`でプレビューサーバーの設定を制御

### 文章品質管理
- 技術文書向けの日本語固有のルールプリセットを持つtextlintを使用
- `.textlintrc`の設定により、`:::details`などのQiita固有の記法に対応
- GitHub Actionsを通じてプルリクエストで自動校正を実行

### CI/CDワークフロー
- **校正ワークフロー**（`proofreading.yml`）：PRでtextlintを実行し、reviewdog経由でレビューコメントを提供
- **公開ワークフロー**（`publish.yml`）：mainブランチへのプッシュ時に自動的に記事をQiitaに公開
- 公開には`QIITA_TOKEN`シークレットの設定が必要

### 依存関係
- `@qiita/qiita-cli`: 記事管理用の公式Qiita CLI
- `textlint` with Japanese presets: 日本語技術コンテンツのテキストリント
- `@anthropic-ai/claude-code`: Claude Code統合

## 重要な注意点

- すべての記事コンテンツは日本語で、日本語技術文書の慣習に従うこと
- textlintの設定はQiitaのMarkdown拡張機能に特化して調整済み
- mainブランチへの公開は自動実行されるため、直接プッシュには注意
- プレビューサーバーはデフォルトでポート8888で実行（qiita.config.jsonで設定可能）
