# パブリッシュワークフローガイド

このガイドでは、Qiita 記事の自動公開ワークフロー (`.github/workflows/publish.yml`) の動作と使い方を説明します。

## 概要

パブリッシュワークフローは、main ブランチに記事をプッシュすると自動的に Qiita に公開し、メタデータの更新を PR として作成する仕組みです。

## ワークフローの動作

### トリガー条件

以下の条件を満たすとワークフローが実行されます:

- **ブランチ**: `main` ブランチへのプッシュ
- **対象ファイル**: `public/**.md` パターンにマッチするファイルの変更

### 実行フロー

```
1. main ブランチに記事をプッシュ
   ↓
2. ワークフローが自動起動
   ↓
3. Qiita API を使用して記事を公開
   ↓
4. 公開結果をチェック（記事 ID やタイムスタンプの更新）
   ↓
5. メタデータに変更がある場合:
   - 新しいブランチを作成 (automated/qiita-metadata-YYYYMMDD-HHMMSS)
   - メタデータの変更をコミット
   - プルリクエストを自動作成
   ↓
6. PR を手動でレビュー・マージ
   ↓
7. リポジトリが Qiita と同期完了
```

## ワークフローの各ステップ

### 1. リポジトリのチェックアウト

```yaml
- name: Checkout repository
  uses: actions/checkout@v4
  with:
    fetch-depth: 0
```

リポジトリの全履歴を取得します。これにより Git 操作が正しく動作します。

### 2. GitHub App トークンの生成

```yaml
- name: Generate GitHub App token
  id: app-token
  uses: actions/create-github-app-token@v1
  with:
    app-id: ${{ secrets.APP_ID }}
    private-key: ${{ secrets.APP_PRIVATE_KEY }}
```

GitHub App を使用してトークンを生成します。このトークンにより:
- 検証済み署名付きコミットが作成可能
- リポジトリルールセットに準拠した PR が作成可能

**必要なシークレット**:
- `APP_ID`: GitHub App の ID
- `APP_PRIVATE_KEY`: GitHub App の秘密鍵

### 3. Node.js と Qiita CLI のセットアップ

```yaml
- name: Setup Node.js
  uses: actions/setup-node@v4
  with:
    node-version: '20'

- name: Install Qiita CLI
  run: npm install -g @qiita/qiita-cli
```

記事の公開に必要な Qiita CLI をインストールします。

### 4. 記事の公開

```yaml
- name: Publish articles to Qiita
  env:
    QIITA_TOKEN: ${{ secrets.QIITA_TOKEN }}
  run: npx qiita publish --all
```

すべての記事を Qiita に公開します。

**必要なシークレット**:
- `QIITA_TOKEN`: Qiita API トークン

**公開動作**:
- 新規記事: Qiita に投稿され、記事 ID が付与される
- 既存記事: Qiita の記事が更新される

### 5. メタデータ変更のチェック

```yaml
- name: Check for metadata changes
  id: check
  run: |
    if git diff --quiet public/; then
      echo "has_changes=false" >> $GITHUB_OUTPUT
    else
      echo "has_changes=true" >> $GITHUB_OUTPUT
      git diff --stat public/
    fi
```

公開後にローカルファイルのメタデータが変更されたかチェックします。

**チェック内容**:
- 新規記事の場合: `id` フィールドが `null` から Qiita の UUID に更新される
- 既存記事の場合: `updated_at` フィールドが更新される

### 6. プルリクエストの作成

```yaml
- name: Create pull request for metadata updates
  if: steps.check.outputs.has_changes == 'true'
  env:
    GH_TOKEN: ${{ steps.app-token.outputs.token }}
  run: |
    git config user.name "qiita-publisher[bot]"
    git config user.email "qiita-publisher[bot]@users.noreply.github.com"

    BRANCH="automated/qiita-metadata-$(date +%Y%m%d-%H%M%S)"
    git checkout -b "$BRANCH"
    git add public/
    git commit -m "Qiita 公開後のメタデータを更新"
    git push origin "$BRANCH"

    gh pr create \
      --title "🔄 Qiita 記事メタデータの更新" \
      --body "..." \
      --base main \
      --head "$BRANCH" \
      --label "automated"
```

メタデータの変更がある場合、自動的に PR を作成します。

**PR の特徴**:
- ブランチ名: `automated/qiita-metadata-YYYYMMDD-HHMMSS`
- 作成者: `qiita-publisher[bot]`
- ラベル: `automated`
- タイトル: 🔄 Qiita 記事メタデータの更新

## 使用方法

### 新規記事の公開

1. `public/` ディレクトリに Markdown ファイルを作成:

```bash
npx qiita new my-article
# または
touch public/my-article.md
```

2. 記事のフロントマターを設定:

```yaml
---
title: 記事のタイトル
tags:
  - タグ1
  - タグ2
private: false
updated_at: ''
id: null
organization_url_name: null
slide: false
ignorePublish: false
---
```

3. 記事の本文を書く

4. main ブランチにプッシュ:

```bash
git add public/my-article.md
git commit -s -m "Add new article: 記事のタイトル"
git push origin main
```

5. ワークフローが自動実行され、Qiita に公開される

6. PR が自動作成されるので、レビューしてマージ

### 既存記事の更新

1. `public/` ディレクトリの記事を編集

2. main ブランチにプッシュ:

```bash
git add public/updated-article.md
git commit -s -m "Update article: 更新内容の説明"
git push origin main
```

3. ワークフローが自動実行され、Qiita の記事が更新される

4. メタデータに変更があれば PR が作成されるので、レビューしてマージ

## PR のレビューとマージ

### レビュー時の確認ポイント

自動作成された PR をレビューする際は、以下を確認してください:

1. **変更ファイルの確認**:
   - `public/` ディレクトリの Markdown ファイルのみが変更されているか
   - 他のファイルが含まれていないか

2. **変更内容の確認**:
   - `id` フィールド: `null` → UUID への変更（新規記事の場合）
   - `updated_at` フィールド: タイムスタンプの更新
   - **記事の本文に意図しない変更がないか**

3. **Qiita での公開確認**:
   - PR の説明にある Qiita のリンクから記事を確認
   - 記事が正しく公開されているか確認

### マージ方法

問題がなければ、通常通り PR をマージしてください:

```bash
# GitHub UI から Merge pull request
# または CLI から
gh pr merge <PR番号> --squash
```

マージ後、ブランチは自動的に削除されます。

## トラブルシューティング

### ワークフローが実行されない

**原因**: トリガー条件を満たしていない

**確認事項**:
- main ブランチへのプッシュか？
- `public/**.md` ファイルが変更されているか？
- ワークフローファイル自体に構文エラーがないか？

### "Resource not accessible by integration" エラー

**原因**: GitHub App の権限が不足、または App が正しくインストールされていない

**解決策**:
1. GitHub App の設定を確認（[GitHub App セットアップガイド](./github-app-setup.md) を参照）
2. リポジトリに App がインストールされているか確認
3. `APP_ID` と `APP_PRIVATE_KEY` シークレットが正しいか確認

### PR が作成されるが "Repository rule violations" エラー

**原因**: GitHub App がリポジトリルールセットのバイパスリストに追加されていない

**解決策**:
1. リポジトリの Settings → Rules → Rulesets を開く
2. **Release branch** ルールセットを編集
3. Bypass list に `qiita-publisher` App を追加
4. バイパスモードを **Pull requests only** に設定

### Qiita に公開されたが PR が作成されない

**原因**: メタデータの変更が検出されなかった、または PR 作成ステップでエラー

**確認事項**:
1. ワークフローログで "Check for metadata changes" ステップを確認
2. `has_changes=true` が出力されているか？
3. "Create pull request" ステップでエラーが発生していないか？

**手動での同期方法**:

```bash
# Qiita からメタデータを取得
npx qiita pull

# 変更を確認
git diff public/

# PR を作成
git checkout -b sync/qiita-metadata
git add public/
git commit -s -m "Sync article metadata from Qiita"
git push origin sync/qiita-metadata
gh pr create --title "Sync Qiita metadata" --base main
```

### 記事の内容も変更されてしまった

**原因**: Qiita 側で記事が編集されている、または Qiita CLI が記事を正規化している

**対応**:
1. PR をマージせずにクローズ
2. ローカルで記事の内容を確認・修正
3. 必要に応じて再度 Qiita に公開

**予防策**:
- Qiita Web UI で記事を直接編集しない
- すべての編集はリポジトリで行う

## ワークフローの無効化

一時的にワークフローを無効にする場合:

1. GitHub リポジトリの **Actions** タブを開く
2. 左サイドバーの **Publish articles** を選択
3. 右上の **...** メニューから **Disable workflow** を選択

または、ワークフローファイルを編集:

```yaml
on:
  # push:  # コメントアウト
  #   branches:
  #     - main
  #   paths:
  #     - 'public/**.md'
  workflow_dispatch:  # 手動実行のみ許可
```

## セキュリティに関する注意事項

### シークレットの管理

- `QIITA_TOKEN`, `APP_ID`, `APP_PRIVATE_KEY` は絶対にコードにコミットしないでください
- シークレットが漏洩した場合は、すぐに無効化して新しいものを生成してください

### GitHub App の権限

- GitHub App には最小限の権限のみを付与してください
- 現在の必要な権限:
  - Contents: Read and write
  - Pull requests: Read and write
  - Metadata: Read-only

### ブランチ保護

- ワークフローは main ブランチのブランチ保護ルールを尊重します
- PR 経由でのみ変更がマージされるため、セキュリティが保たれます

## 参考資料

- [GitHub App セットアップガイド](./github-app-setup.md)
- [Qiita CLI 公式ドキュメント](https://github.com/increments/qiita-cli)
- [GitHub Actions ドキュメント](https://docs.github.com/en/actions)
- [リポジトリルールセット](https://docs.github.com/en/repositories/configuring-branches-and-merges-in-your-repository/managing-rulesets)

## よくある質問

### Q: 複数の記事を一度に公開できますか？

A: はい、できます。複数の記事を同時に main ブランチにプッシュすると、すべての記事が一度に公開され、1つの PR にまとめられます。

### Q: 下書き記事（private: true）も公開されますか？

A: はい、`private: true` の記事も Qiita の非公開記事として公開されます。完全に公開を防ぎたい場合は、`ignorePublish: true` を設定してください。

### Q: PR を自動マージすることはできますか？

A: 技術的には可能ですが、このプロジェクトでは安全性を重視し、手動レビューを推奨しています。自動マージを実装する場合は、厳密なバリデーションが必要です。

### Q: ワークフローの実行時間はどのくらいですか？

A: 通常、2〜3分程度で完了します。記事の数や Qiita API の応答時間により変動します。

### Q: ワークフローが失敗した場合、記事は公開されますか？

A: Qiita への公開ステップが成功していれば、記事は公開されます。ただし、PR の作成が失敗した場合、メタデータの同期は手動で行う必要があります。
