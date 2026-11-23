---
title: '[Notion CLI] Notion CLI を Windowsで使う準備'
tags:
  - Notion
private: false
updated_at: '2026-11-23T18:18:50+09:00'
id: 4b4f797aa4bd34e62594
organization_url_name: null
slide: false
ignorePublish: false
posting_campaign_uuid: null
agreed_posting_campaign_term: false
---

:::note info
**バージョンについて**
* Windows 11 Home
  * バージョン: 2626H2
* wingetバージョン: v1.29.380
* Notion CLI: 0.23.31
:::

## Notion CLIとは

Notion CLI は、キーボードで文字の命令(コマンド)を入力して、Notion を操作するツールです。

ページの作成、編集、削除や、Notionデータベースの操作などができます。

## セットアップ
### セットアップに必要なもの

Notion CLIが使えるように設定するのに、必要なものは以下です。

* Notionのアカウント
* インターネットに接続できる環境
* ブラウザー(普段お使いのもので構いません)
* WinGet (Windows にアプリを入れる機能。『インストール』で確認します)

### 1. コマンドを入力するためのアプリを開く

コマンドを入力するため、好みのターミナルアプリを開きます。
私のおすすめは Windowsターミナルです。

#### Windowsターミナル
Windowsキーを押下後「Terminal」と検索して「ターミナル」を開きます。
または、`Windows` キー + `R` キー を同時に押した後、「wt」と入力、そのままエンターキーを押します。

ここではインストール、セットアップ方法については説明しません。
インストールする場合、 [Windows Terminal - Windows に無料でダウンロードしてインストールする | Microsoft Store](https://apps.microsoft.com/detail/9n0dx20hk701) から入れるとよいでしょう。

#### コマンドプロンプト
`Windows` キー + `R` キーを同時に押した後、「cmd」と入力、そのままエンターキーを押します。


### インストール
WinGet を使用してインストールします。

WinGetは以下コマンドを実行すると、WinGetが使えるかわかります。
```powershell
winget --version
```
`v1.29.380` のような数字が表示されれば使えます。

<details><summary>WinGet について</summary>

---

Windowsで使用できる、アプリのインストールや更新、削除などができるツールです。
WinGet は標準で入っていることが多いです。

入っていない場合、[アプリ インストーラー](https://apps.microsoft.com/detail/9NBLGGH4NNS1) を入れた後、PCを再起動してください。

---

</details>


以下コマンドで Notion CLI をインストールします。

```powershell
winget install Notion.ntn
```

日本語環境の場合、以下の文章が表示されればインストールに成功しています。
```
コマンド ライン エイリアスが追加されました: "ntn"
インストールが完了しました
```

その後、ターミナルアプリを再起動してから、以下コマンドを実行します。
```powershell
ntn --version
```

使用できる状態であれば、 Notion CLI  のバージョン(`ntn 0.23.31`)が表示されます。
表示されない場合は、PCを再起動してから再度 `ntn --version` を試してください。

### 2. 認証
#### ログイン
Notionアカウントと、ワークスペースを認証します。

```powershell
ntn login
```

このコマンドを実行すると、ブラウザーが開きます。
Notionにログインしていない場合、ここでログインを行います。

##### 連携するワークスペースの選択

連携するワークスペースを選択できます。
また、認証コードもここで表示されます。認証コードはターミナルにも表示されます。
![ブラウザーに表示されたNotion CLIの認可画面](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/78179/ef9e2e7c-442a-4131-97dc-86c083bfdfd9.avif)
![ntn login 実行後、ターミナルが確認待ちになっている様子](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/78179/f9253a8e-d2e2-4262-84d5-4bc7c030dd17.avif)

ターミナルとブラウザーで、認証コードが一致するか確認します。

一致しない場合は「承認」を押さず、ブラウザーを閉じます。
ターミナルアプリを開きなおして `ntn login` をもう一度実行し、最初からやり直してください。

認証コードが一致している場合、ブラウザー上で「承認」ボタンを押下します。

認証が終わると、ターミナルも `✔ Authenticated!` と表示されます。
![認証が完了したターミナルの様子](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/78179/70b39472-100f-4eb0-a643-4d5f5d72121b.avif)

認証情報は、Windowsの場合「資格情報マネージャー」に保存されます。


#### ログアウト
ログアウトを行うコマンドもあります。
何らかの理由でPCに認証情報を残したくない場合、以下のコマンドを使ってください。

```powershell
ntn logout
```

## 参考
- [WinGet を使用して install し、アプリケーションを管理する | Microsoft Learn](https://learn.microsoft.com/ja-jp/windows/package-manager/winget/)
- [Notion CLIを使用して、ターミナルからNotionを操作する | Notion Help](https://www.notion.com/ja/help/use-notion-from-your-terminal-with-notion-cli)
- [Notion CLI - Notion Docs](https://developers.notion.com/cli/get-started/overview)
- [Windows の資格情報マネージャー | Microsoft Support](https://support.microsoft.com/ja-jp/windows/security/credential-manager-in-windows)
