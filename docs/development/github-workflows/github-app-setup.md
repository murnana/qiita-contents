# GitHub App セットアップガイド

このガイドでは、Qiita 公開ワークフローで使用する GitHub App のセットアップ手順を説明します。

## 概要

GitHub App は、リポジトリルールセットに準拠しながら、ワークフローから自動的にプルリクエストを作成するために使用されます。

## セットアップ手順

### 1. GitHub App の作成

1. GitHub の右上のプロフィールアイコンをクリックし、**Settings** を選択
2. 左サイドバーの一番下にある **Developer settings** をクリック
3. 左サイドバーの **GitHub Apps** をクリック
4. **New GitHub App** ボタンをクリック

### 2. GitHub App の設定

以下の項目を設定します:

#### 基本情報
- **GitHub App name**: `qiita-publisher`（または任意の名前、グローバルに一意である必要があります）
- **Homepage URL**: `https://github.com/murnana/qiita-contents`
- **Webhook**:
  - **Active** のチェックを**外す**（Webhook は不要）

#### 権限設定（Permissions）

**Repository permissions** で以下を設定:

- **Contents**: `Read and write` を選択
  - ブランチへのコミットとプッシュに必要
- **Pull requests**: `Read and write` を選択
  - プルリクエストの作成に必要
- **Metadata**: `Read-only`（自動選択されます）

その他の権限はすべてデフォルト（No access）のままにします。

#### インストール制限

- **Where can this GitHub App be installed?**:
  - **Only on this account** を選択

### 3. GitHub App の作成完了

**Create GitHub App** ボタンをクリックして App を作成します。

### 4. 秘密鍵の生成

1. 作成した GitHub App のページで、下にスクロールして **Private keys** セクションを見つけます
2. **Generate a private key** ボタンをクリック
3. `.pem` ファイルがダウンロードされます（このファイルは安全に保管してください）

### 5. App ID の確認

GitHub App のページ上部の **About** セクションに **App ID** が表示されています。この番号をメモしてください。

### 6. リポジトリへのインストール

1. GitHub App のページの左サイドバーで **Install App** をクリック
2. 自分のアカウント横の **Install** ボタンをクリック
3. **Only select repositories** を選択
4. `qiita-contents` リポジトリを選択
5. **Install** ボタンをクリック

### 7. リポジトリシークレットの設定

1. `qiita-contents` リポジトリのページに移動
2. **Settings** → **Secrets and variables** → **Actions** をクリック
3. 以下の2つのシークレットを追加:

#### APP_ID の追加
- **New repository secret** をクリック
- **Name**: `APP_ID`
- **Secret**: 手順 5 でメモした App ID を入力
- **Add secret** をクリック

#### APP_PRIVATE_KEY の追加
- **New repository secret** をクリック
- **Name**: `APP_PRIVATE_KEY`
- **Secret**: 手順 4 でダウンロードした `.pem` ファイルの内容全体をコピー&ペースト
  - ファイルをテキストエディタで開き、`-----BEGIN RSA PRIVATE KEY-----` から `-----END RSA PRIVATE KEY-----` までをすべて含めてコピー
- **Add secret** をクリック

### 8. リポジトリルールセットの更新

GitHub App をバイパスアクターとして追加し、PR 内でコミットを作成できるようにします。

1. リポジトリの **Settings** → **Rules** → **Rulesets** に移動
2. 既存の **Release branch** ルールセットをクリック
3. **Bypass list** セクションまでスクロール
4. **Add bypass** をクリック
5. 検索ボックスに作成した GitHub App の名前（`qiita-publisher`）を入力
6. App を選択し、バイパスモードを **Always** または **Pull requests only** に設定
7. **Save changes** をクリック

## 設定の確認

以下の項目が正しく設定されていることを確認してください:

- ✅ GitHub App が作成されている
- ✅ App に Contents と Pull requests の権限がある
- ✅ App が `qiita-contents` リポジトリにインストールされている
- ✅ `APP_ID` シークレットが設定されている
- ✅ `APP_PRIVATE_KEY` シークレットが設定されている
- ✅ リポジトリルールセットのバイパスリストに App が追加されている

## トラブルシューティング

### ワークフローで "Resource not accessible by integration" エラーが発生

**原因**: GitHub App の権限が不足している

**解決策**:
1. GitHub App の設定ページに移動
2. **Permissions & events** タブを確認
3. **Repository permissions** で **Contents** と **Pull requests** が `Read and write` になっているか確認
4. 権限を変更した場合、リポジトリの **Settings** → **Integrations** → **GitHub Apps** から App を再承認する必要があります

### ワークフローで App トークンが生成できない

**原因**: シークレットが正しく設定されていない

**解決策**:
1. リポジトリの **Settings** → **Secrets and variables** → **Actions** を確認
2. `APP_ID` と `APP_PRIVATE_KEY` が両方とも存在するか確認
3. `APP_PRIVATE_KEY` の内容が完全か確認（BEGIN/END 行を含む）

### PR が作成されるがルールセット違反で失敗する

**原因**: GitHub App がバイパスリストに追加されていない

**解決策**:
1. リポジトリの **Settings** → **Rules** → **Rulesets** を確認
2. **Release branch** ルールセットを編集
3. **Bypass list** に GitHub App が含まれているか確認

## セキュリティに関する注意事項

- `.pem` ファイルは絶対に Git リポジトリにコミットしないでください
- `.pem` ファイルは安全な場所に保管してください
- GitHub App の秘密鍵が漏洩した場合は、すぐに古い鍵を削除して新しい鍵を生成してください
- GitHub App にはこのリポジトリ以外のアクセス権限を与えないでください

## 次のステップ

GitHub App のセットアップが完了したら、ワークフローファイル (`.github/workflows/publish.yml`) が正しく設定されていることを確認してください。

ワークフローの詳細については、[パブリッシュワークフローのドキュメント](./publish-workflow.md) を参照してください。
