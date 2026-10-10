---
title: '[Slidev] Windowsでのセットアップ'
tags:
  - Slidev
  - Windows
  - Node.js
  - Markdown
private: false
updated_at: ''
id: null
organization_url_name: null
slide: false
ignorePublish: false
posting_campaign_uuid: null
agreed_posting_campaign_term: false
---

<details><summary>きっかけ</summary>

[@_kzr](https://x.com/_kzr)様のツイートより。

https://x.com/_kzr/status/2082431630531236204

</details>

## Node.js の導入

このシステムを使用するには Node.js が必要です。

私は Volta という、Node.js をバージョン別に入れたり使ったりできるものを好んでいます。

ここでは説明しません。

https://volta.sh/

この記事執筆時点での、必要なバージョンは 22.12.0 以上です。

バージョンに困っている人は、ひとまず[Node.js — Node.js®をダウンロードする](https://nodejs.org/ja/download)に書いてある LTS のものを入れるとよいです。

```powershell
volta install node@24.21.0
```

## プロジェクトのセットアップ

npm からインストールします。
お好みで pnpm, yarn, deno 等もあります。

```powershell
npm init slidev@latest
```

導入するパッケージについて確認を求められるので、このまま `y` を入れます。

```text
Need to install the following packages:
create-slidev@53.0.0
Ok to proceed? (y)
```

そのまま、プロジェクトの名前を聞かれます。

```text
> npx
> create-slidev


  ●■▲
  Slidev Creator  v53.0.0

? Project name: »
```

お好みのプロジェクト名を入れてください。デフォルトだと `slidev` が入っています。

```text
√ Project name: ... slidev
  Scaffolding project in slidev ...
  Done.
```

コマンドを実行したディレクトリーの下に、先ほど入力したプロジェクト名のディレクトリーが作られます。

例えば、ユーザーディレクトリー `C:\Users\<ユーザー名>` で実行すると、`C:\Users\<ユーザー名>\slidev` に作られます。

続いて、インストールしてよいか聞かれます。

`y` と入力すると、必要なパッケージが入ってきます。
またさらに、実行までしてくれます。

```text
? Install and start it now using npm? » (Y/n)
```

実行に成功すると、デフォルトブラウザーでスライドが開きます。

![Slidevのウェルカムスライドがブラウザーで開いた様子](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/78179/af6002d4-b24b-4ec5-8a3d-1b301fdebb03.avif)

## 参考

* [Getting Started | Slidev](https://sli.dev/guide/)
