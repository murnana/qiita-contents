# Qiita コンテンツ管理

Qiita CLI を使用してQiitaへ技術記事を管理・公開するためのコンテンツ管理リポジトリです。記事は `public/` ディレクトリにMarkdownファイルとして保存され、textlintによる日本語文章校正を行います。

## 特徴

- 📝 Qiita CLI による記事管理
- 🔍 textlint による日本語文章校正
- 🚀 GitHub Actions による Qiita への自動公開
- 🐳 統一された開発環境のための DevContainer サポート
- 📋 reviewdog によるプルリクエストレビュー自動化

## クイックスタート

### 前提条件

- Node.js (`.devcontainer/devcontainer.json` で指定)
- Qiita アカウントと API トークン

### インストール

```bash
npm ci
```

### Qiita 認証

```bash
npx qiita login --credential ./.qiita-config/
```

認証情報は `./.qiita-config/` に自動保存され、DevContainer の再ビルド後も維持されます。

## 使い方

### 記事管理

```bash
# 新しい記事を作成
npx qiita new <ファイル名>

# 記事をローカルでプレビュー (http://localhost:8888)
npx qiita preview

# Qiita から最新の記事を取得
npx qiita pull

# Qiita へ記事を公開
npx qiita publish
```

### 文章校正

```bash
# すべての記事に textlint を実行
npm test

# textlint を直接実行
npx textlint "./public/*.md"
```

## プロジェクト構成

```
qiita-contents/
├── .github/workflows/     # CI/CD ワークフロー
│   ├── publish.yml       # main ブランチへの push 時に Qiita へ自動公開
│   └── proofreading.yml  # reviewdog による PR 文章校正
├── .devcontainer/        # DevContainer 設定
├── assets/               # 記事の画像とソースファイル
│   └── {記事スラッグ}/
│       ├── sources/      # 編集可能な元のアセット
│       └── images/       # 最適化済みの公開用画像
├── docs/                 # ドキュメント
│   └── development/
│       └── asset-management/
│           ├── README.md # 画像管理ガイド
│           └── logs/     # 画像アップロードログ
├── public/               # 記事コンテンツ (Markdown ファイル)
├── .textlintrc          # textlint 設定
├── qiita.config.json    # Qiita CLI 設定
└── package.json         # プロジェクト依存関係
```

## 画像管理

画像は `assets/` ディレクトリ内に記事ごとに整理されています。ソースファイルと最適化済み画像の両方が Git で管理されます。

**ワークフロー:**
1. ソース画像（draw.io ダイアグラム、スクリーンショット）を `assets/{記事スラッグ}/sources/` に作成
2. ffmpeg を使用して `assets/{記事スラッグ}/images/` へ画像を最適化
3. Qiita の Web インターフェースから手動でアップロード
4. アップロード情報を `docs/development/asset-management/logs/{記事スラッグ}.md` に記録
5. 記事 Markdown 内で返却された Qiita URL を参照

詳細は [docs/development/asset-management/README.md](docs/development/asset-management/README.md) を参照してください。

## 記事フォーマット

記事は Qiita 固有のメタデータに YAML フロントマターを使用します:

```markdown
---
title: "記事タイトル"
emoji: "📝"
type: "tech" # または "idea"
topics: ["javascript", "nodejs"]
published: false
---

# 記事コンテンツ

記事の内容をここに書きます...
```

## CI/CD ワークフロー

### 文章校正ワークフロー

- プルリクエスト時にトリガー
- 日本語テキスト検証のため textlint を実行
- reviewdog によるレビューコメントを提供

### 公開ワークフロー

- main ブランチへの push 時にトリガー
- 記事を Qiita へ自動公開
- リポジトリ設定に `QIITA_TOKEN` シークレットが必要

## テキスト lint ルール

このプロジェクトは日本語固有のプリセットを使用した textlint を採用しています:

- `textlint-rule-preset-japanese`: 基本的な日本語文法ルール
- `textlint-rule-preset-jtf-style`: JTF（日本テクニカルコミュニケーション協会）スタイルガイド
- `textlint-rule-preset-ja-technical-writing`: 日本語技術文書ルール
- Qiita 固有の構文（例: `:::details`）のカスタムルール

## 開発環境

このプロジェクトには、異なるマシン間で統一された開発環境を実現するための DevContainer 設定が含まれています。コンテナにはコンテンツ作成・編集に必要なすべてのツールと拡張機能が含まれています。

## 設定ファイル

- `.textlintrc`: textlint のルールと設定
- `qiita.config.json`: Qiita CLI 設定（プレビューサーバーのポートなど）
- `.devcontainer/devcontainer.json`: 開発コンテナのセットアップ

## コントリビューション

1. フィーチャーブランチを作成
2. `public/` ディレクトリで記事を執筆・編集
3. `npm test` で文章品質を確認
4. プルリクエストを作成
5. reviewdog が指摘した textlint の問題に対応
6. main ブランチへマージすると自動公開

## ライセンス

MIT

## 作者

Murnana

## リポジトリ

- GitHub: [murnana/qiita-contents](https://github.com/murnana/qiita-contents)
- Issues: [GitHub Issues](https://github.com/murnana/qiita-contents/issues)
