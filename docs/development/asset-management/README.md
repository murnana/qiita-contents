# アセット管理ガイド

このドキュメントは、Qiita記事で使用する画像やその他のアセットをこのリポジトリで管理するための完全ガイドです。

## 目次

- [概要](#概要)
- [ディレクトリ構造](#ディレクトリ構造)
- [ワークフロー](#ワークフロー)
- [命名規則](#命名規則)
- [画像仕様](#画像仕様)
- [アップロードログ](#アップロードログ)
- [チェックリスト](#チェックリスト)
- [よくある質問](#よくある質問)

## 概要

このリポジトリでは、記事で使用する画像を以下の2つに分けて管理します：

1. **元アセット** (`sources/`): 編集可能な元ファイル（draw.io、生スクリーンショット、Photoshopファイルなど）
2. **公開用画像** (`images/`): Qiitaにアップロードするために最適化された画像

どちらもGitで追跡し、バージョン管理とチーム共有を可能にします。

### なぜこの方法か

- **バージョン管理**: 画像の変更履歴を追跡できる
- **再現性**: 元アセットから公開用画像を再生成できる
- **共同作業**: チームメンバーと画像を共有できる
- **一元管理**: 記事と関連アセットを1つのリポジトリで管理

## ディレクトリ構造

### 基本構造

```
qiita-contents/
├── assets/                    # 記事用アセットの保存場所
│   └── {article-slug}/        # 記事ごとのディレクトリ
│       ├── sources/           # 元アセット（編集可能）
│       │   ├── diagram.drawio
│       │   ├── screenshot.png
│       │   └── chart.xlsx
│       └── images/            # 公開用画像（最適化済み）
│           ├── diagram.png
│           ├── screenshot.webp
│           └── chart.png
└── docs/
    └── development/
        └── asset-management/
            └── logs/          # アップロードログ
                └── {article-slug}.md
```

### ディレクトリの役割

#### `assets/{article-slug}/sources/`

元となる編集可能なアセットを保存します。

**保存するファイル:**
- Draw.ioの図（`.drawio`）
- 元のスクリーンショット（高解像度PNG）
- Photoshop/GIMPファイル（`.psd`, `.xcf`）
- Excel/スプレッドシート（`.xlsx`, `.ods`）
- SVGファイル（`.svg`）
- 動画ファイル（後でフレーム抽出する場合）

**Git管理:** すべて追跡する

#### `assets/{article-slug}/images/`

Qiitaにアップロードするための最適化済み画像を保存します。

**保存するファイル:**
- 最適化されたPNG（`.png`）
- WebP画像（`.webp`）
- JPEG画像（`.jpg`, `.jpeg`）
- アニメーションGIF（`.gif`）

**Git管理:** すべて追跡する

#### `docs/development/asset-management/logs/{article-slug}.md`

Qiitaにアップロードした画像の情報を記事別に記録します。

**ファイル形式:** Markdown（`.md`）
**ファイル名:** 記事スラッグに対応（例: `atcoder-abc434-taskc-with-claude-code.md`）

## ワークフロー

### 1. 元アセットの作成

記事で使用する画像の元ファイルを作成します。

```bash
# 記事のアセットディレクトリを作成（初回のみ）
mkdir -p assets/my-article-slug/sources
mkdir -p assets/my-article-slug/images
```

**ツール例:**
- **図解・ダイアグラム**: Draw.io、Mermaid、Excalidraw
- **スクリーンショット**: Windows Snipping Tool、macOS Screenshot、ShareX
- **画像編集**: Photoshop、GIMP、Pixelmator

元ファイルを `assets/{article-slug}/sources/` に保存します。

### 2. 画像の最適化

元アセットから公開用の画像を生成します。

#### ffmpegを使用した最適化

**PNG → WebP 変換（推奨）:**
```bash
ffmpeg -i sources/screenshot.png -quality 85 images/screenshot.webp
```

**画像のリサイズ:**
```bash
# 幅1200pxにリサイズ（高さは自動調整）
ffmpeg -i sources/large-image.png -vf scale=1200:-1 images/large-image.png
```

**JPEG圧縮:**
```bash
ffmpeg -i sources/photo.png -q:v 3 images/photo.jpg
```

#### ImageMagickを使用した最適化

**基本的な変換:**
```bash
# PNG → WebP
convert sources/image.png -quality 85 images/image.webp

# リサイズ
convert sources/image.png -resize 1200x images/image.png

# 品質を指定してJPEG変換
convert sources/image.png -quality 80 images/image.jpg
```

**バッチ処理:**
```bash
# sourcesディレクトリ内のすべてのPNGをWebPに変換
for file in sources/*.png; do
  filename=$(basename "$file" .png)
  ffmpeg -i "$file" -quality 85 "images/${filename}.webp"
done
```

#### Draw.ioからのエクスポート

1. Draw.ioで図を開く
2. `File` → `Export as` → `PNG` / `SVG`
3. 解像度を設定（推奨: 2x）
4. `assets/{article-slug}/images/` に保存

### 3. Qiitaへのアップロード

**重要:** Qiita CLIには画像アップロード機能がありません。Webインターフェースを使用します。

#### 手順

1. **Qiitaの画像管理ページにアクセス**
   - URL: https://qiita.com/settings/images
   - ログインが必要です

2. **画像をアップロード**
   - `assets/{article-slug}/images/` から最適化済み画像をドラッグ&ドロップ
   - または「ファイルを選択」ボタンでアップロード

3. **URLをコピー**
   - アップロード後、以下の形式のURLが表示されます：
     ```
     https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/{user_id}/{uuid}.{ext}
     ```
   - このURLをコピーします

4. **記事で参照**
   - Markdown形式で記事に貼り付け：
     ```markdown
     ![説明テキスト](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/12345/uuid.png)
     ```

5. **アップロードログに記録**
   - 後述の「アップロードログ」セクションを参照

### 4. 記事での画像参照

#### Markdown形式（基本）

```markdown
![画像の説明](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/12345/uuid.png)
```

**重要:** alt テキスト（`画像の説明`）は必ず日本語で記述してください。

#### HTML形式（サイズ指定）

大きな画像の表示サイズを制御する場合：

```html
<img width="600" src="https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/12345/uuid.png" alt="認証フローの図">
```

#### 複数画像の並列表示

```html
<div style="display: flex; gap: 10px;">
  <img width="300" src="https://qiita-image-store.../image1.png" alt="画像1">
  <img width="300" src="https://qiita-image-store.../image2.png" alt="画像2">
</div>
```

### 5. アップロードログの記録

アップロード情報を `docs/development/asset-management/logs/{article-slug}.md` に記録します。

#### ログファイルの作成

新しい記事の場合、以下のテンプレートを使用してログファイルを作成します：

```markdown
# {article-title} - 画像アップロードログ

## 画像一覧

| ローカルファイル | Qiita URL | アップロード日時 | 備考 |
|-----------------|-----------|----------------|------|
| | | | |

## メモ

-
```

#### ログの記録例

```markdown
# AtCoder ABC434 Task C を Claude Code で解く - 画像アップロードログ

## 画像一覧

| ローカルファイル | Qiita URL | アップロード日時 | 備考 |
|-----------------|-----------|----------------|------|
| algorithm-flow.png | https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/12345/abc-def-123.png | 2025-11-30 10:30 | アルゴリズムのフローチャート |
| screenshot-editor.webp | https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/12345/ghi-jkl-456.webp | 2025-11-30 10:35 | エディタのスクリーンショット |
| result-chart.png | https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/12345/mno-pqr-789.png | 2025-11-30 10:40 | 実行結果のグラフ |

## メモ

- すべての画像を300KB以下に最適化
- WebP形式を優先的に使用（ブラウザ互換性良好）
- フローチャートはDraw.ioで作成（sources/algorithm-flow.drawioから生成）
```

## 命名規則

### 記事スラッグ（article-slug）

記事のファイル名と一致させます。

**例:**
- 記事ファイル: `public/atcoder-abc434-taskc-with-claude-code.md`
- アセットディレクトリ: `assets/atcoder-abc434-taskc-with-claude-code/`
- ログファイル: `docs/development/asset-management/logs/atcoder-abc434-taskc-with-claude-code.md`

### 画像ファイル名

#### 推奨規則

1. **ケバブケース（kebab-case）を使用**
   ```
   ✓ authentication-flow-diagram.png
   ✗ AuthenticationFlowDiagram.png
   ✗ authentication_flow_diagram.png
   ```

2. **説明的な名前を付ける**
   ```
   ✓ login-form-screenshot.png
   ✓ performance-comparison-chart.png
   ✗ image1.png
   ✗ screenshot.png
   ```

3. **英語のファイル名を使用**
   ```
   ✓ code-editor-screenshot.png
   ✗ コードエディタのスクリーンショット.png
   ```
   理由: クロスプラットフォーム互換性、URL生成時の問題回避

4. **連番が必要な場合は2桁のゼロパディング**
   ```
   ✓ step-01-login.png
   ✓ step-02-dashboard.png
   ✓ step-03-settings.png
   ✗ step-1-login.png
   ✗ step-2-dashboard.png
   ```

5. **バージョンを示す場合**
   ```
   ✓ architecture-diagram-v2.png
   ✓ ui-mockup-draft.png
   ✓ ui-mockup-final.png
   ```

#### ファイル名の例

**良い例:**
- `authentication-flow-diagram.png` - フローチャート
- `api-endpoint-list.png` - API一覧のスクリーンショット
- `performance-before-optimization.png` - 最適化前の結果
- `performance-after-optimization.png` - 最適化後の結果
- `code-example-main-function.png` - コード例
- `error-message-screenshot.png` - エラーメッセージ

**悪い例:**
- `image1.png` - 内容が不明
- `スクリーンショット 2025-11-30.png` - 日本語、日付のみ
- `IMG_20251130_103045.png` - カメラのデフォルト名
- `Untitled.png` - 内容が不明

## 画像仕様

### 推奨フォーマット

| 用途 | フォーマット | 理由 |
|------|-------------|------|
| スクリーンショット | WebP | 優れた圧縮率、ブラウザ互換性良好 |
| 図解・ダイアグラム | PNG または WebP | シャープな線、透明度サポート |
| 写真 | JPEG | 写真に最適化された圧縮 |
| 透明度が必要 | PNG | アルファチャンネルサポート |
| アニメーション | GIF または MP4 | 動きのある説明 |

### サイズと品質

#### 推奨サイズ

- **最大幅**: 1200px（Qiitaの記事表示幅に最適）
- **UI スクリーンショット**: 600-800px（モバイルフレンドリー）
- **小さなアイコン・図**: 元のサイズ（リサイズ不要）
- **高解像度図**: 1600px以下

#### ファイルサイズ目標

- **目標**: 1画像あたり < 300KB
- **許容**: < 500KB
- **避けるべき**: > 1MB

大きな画像はページ読み込み速度に影響します。

#### 品質設定

**WebP:**
```bash
# 品質: 85（推奨、良好なバランス）
ffmpeg -i input.png -quality 85 output.webp
```

**JPEG:**
```bash
# 品質: 80（推奨）
ffmpeg -i input.png -q:v 3 output.jpg
# -q:v 2 (高品質) から -q:v 5 (低品質) の範囲
```

**PNG:**
```bash
# PNG最適化（可逆圧縮）
optipng -o7 image.png
# または
pngquant --quality=80-95 image.png
```

### 解像度

- **通常ディスプレイ**: 1x（標準解像度）
- **Retinaディスプレイ**: 必要に応じて2x（高解像度）
  - ただしファイルサイズが増えるため、重要な図のみ

### Alt テキスト

#### 必須要件

1. **すべての画像に alt テキストを記述する**
2. **日本語で記述する**（記事内容に合わせる）
3. **説明的で簡潔にする**（10-30文字が理想）
4. **スクリーンリーダーを意識する**

#### 良い例

```markdown
![認証フローの図](url)
![ログイン画面のスクリーンショット](url)
![実行結果のグラフ](url)
![エラーメッセージの例](url)
```

#### 悪い例

```markdown
![](url)  // alt テキストなし
![画像](url)  // 抽象的すぎる
![image](url)  // 英語
![スクリーンショット](url)  // 何のスクリーンショットか不明
```

### アクセシビリティ

- **コントラスト**: 図の文字は背景とのコントラストを確保
- **文字サイズ**: 図内の文字は読みやすいサイズに（最小12px推奨）
- **色覚**: 色だけで情報を伝えない（パターンやラベルも併用）

## アップロードログ

### 目的

1. **URL管理**: ローカルファイルとQiita URLの対応を記録
2. **再アップロード**: 画像更新時に既存URLを確認
3. **監査**: いつ誰がアップロードしたか追跡

### ログファイルの配置

```
docs/development/asset-management/logs/{article-slug}.md
```

### テンプレート

```markdown
# {article-title} - 画像アップロードログ

## 画像一覧

| ローカルファイル | Qiita URL | アップロード日時 | 備考 |
|-----------------|-----------|----------------|------|
| | | | |

## メモ

-
```

### 記録項目

| 項目 | 説明 | 例 |
|------|------|-----|
| ローカルファイル | `images/` 内のファイル名 | `diagram.png` |
| Qiita URL | アップロード後の完全なURL | `https://qiita-image-store.s3...` |
| アップロード日時 | YYYY-MM-DD HH:MM 形式 | `2025-11-30 10:30` |
| 備考 | 画像の内容や用途 | `認証フローの図` |

### 更新タイミング

- 新しい画像をアップロードした直後
- 画像を再アップロードして更新した場合
- 画像を削除した場合（削除フラグを追加）

## チェックリスト

### 画像追加時のチェックリスト

記事に画像を追加する際は、以下を確認してください：

- [ ] **元アセット保存**: `assets/{article-slug}/sources/` に元ファイルを保存
- [ ] **画像最適化**: ffmpegやImageMagickで最適化済み
- [ ] **ファイルサイズ**: 300KB以下（推奨）、500KB以下（許容）
- [ ] **画像サイズ**: 幅1200px以下
- [ ] **ファイル名**: ケバブケースで説明的な名前
- [ ] **フォーマット**: 適切なフォーマット（WebP/PNG/JPEG）を選択
- [ ] **Qiitaアップロード**: Webインターフェースからアップロード完了
- [ ] **URL取得**: Qiita URLをコピー済み
- [ ] **記事に挿入**: Markdown形式で記事に挿入済み
- [ ] **Alt テキスト**: 日本語で説明的なalt テキストを記述
- [ ] **ログ記録**: `docs/development/asset-management/logs/{article-slug}.md` に記録
- [ ] **Git コミット**: 元アセットと最適化画像をコミット
- [ ] **プレビュー確認**: `npx qiita preview` で画像表示を確認

### レビュー時のチェックリスト

Pull Request レビュー時：

- [ ] 画像ファイルが適切なディレクトリに配置されているか
- [ ] ファイル名が命名規則に従っているか
- [ ] ファイルサイズが適切か（大きすぎないか）
- [ ] Alt テキストが日本語で説明的か
- [ ] アップロードログが更新されているか
- [ ] 元アセット（sources/）も保存されているか

## よくある質問

### Q1. 既存の記事に画像を追加したい

**A:** 以下の手順で追加できます：

1. アセットディレクトリを作成：
   ```bash
   mkdir -p assets/{article-slug}/sources
   mkdir -p assets/{article-slug}/images
   ```

2. 通常のワークフローに従って画像を追加

3. ログファイルを作成：
   ```bash
   # テンプレートをコピー
   cp docs/development/asset-management/logs/atcoder-abc434-taskc-with-claude-code.md \
      docs/development/asset-management/logs/{article-slug}.md
   ```

### Q2. 画像を更新したい

**A:**

1. `sources/` の元ファイルを更新
2. 最適化し直して `images/` を更新
3. Qiitaに再アップロード（新しいURLが発行される）
4. 記事内のURLを新しいURLに更新
5. ログファイルを更新（古いURLは削除または「削除済み」と記録）

### Q3. 複数の記事で同じ画像を使いたい

**A:**

**推奨しません。** 各記事のアセットディレクトリに個別にコピーしてください。

理由：
- 記事の独立性を保つ
- 一方の記事を削除しても影響を受けない
- 記事ごとに最適化設定を変えられる

### Q4. 動画を埋め込みたい

**A:**

Qiitaは動画の直接埋め込みをサポートしていません。以下の方法があります：

1. **YouTube/Vimeo埋め込み**:
   ```markdown
   https://www.youtube.com/watch?v=VIDEO_ID
   ```
   （URLを貼ると自動で埋め込まれる）

2. **GIFアニメーション**:
   ```bash
   # 動画からGIF作成
   ffmpeg -i input.mp4 -vf "fps=10,scale=800:-1" output.gif
   ```
   GIFをQiitaにアップロード

3. **MP4を画像として**:
   Qiitaは`.mp4`のアップロードをサポート（一部ブラウザ）

### Q5. 画像が表示されない

**チェックポイント:**

1. **URLが正しいか**: コピー時に切れていないか確認
2. **Qiitaにアップロード済みか**: ローカルパスを使っていないか
3. **プレビューサーバーは起動中か**: `npx qiita preview` を実行
4. **Markdown形式が正しいか**: `![alt](url)` の形式を確認
5. **ブラウザキャッシュ**: Ctrl+Shift+R で強制リロード

### Q6. Gitリポジトリサイズがどんどん大きくなる

**A:**

大きな画像ファイルを多数追跡すると発生します。

**対策:**
1. 画像を300KB以下に最適化
2. 不要な画像は削除
3. 大きな元アセット（PSDファイルなど）は外部ストレージ検討
4. 将来的にGit LFSへの移行を検討

**現在のリポジトリサイズ確認:**
```bash
du -sh .git
```

### Q7. 画像の最適化が面倒

**A:**

将来的に自動化スクリプトの追加を検討できます（現在は手動）。

**簡易的なバッチ処理例:**
```bash
# sourcesのすべてのPNGをWebPに変換
cd assets/{article-slug}
for file in sources/*.png; do
  filename=$(basename "$file" .png)
  ffmpeg -i "$file" -quality 85 "images/${filename}.webp"
done
```

### Q8. Draw.ioファイルをGitで管理すると差分が見づらい

**A:**

Draw.ioは内部的にXMLなので、テキスト差分が見づらいです。

**対策:**
1. コミットメッセージで変更内容を明記
2. 重要な変更時はエクスポートしたPNG/SVGもコミット
3. Draw.io Desktopの「Export as」で比較用SVG保存

### Q9. 一時ファイル（.tmp）がGitに追加されてしまう

**A:**

`.gitignore` に既に追加されています：
```gitignore
assets/**/*.tmp
assets/**/*.temp
assets/**/.DS_Store
```

もし追加された場合：
```bash
git rm --cached assets/**/*.tmp
```

### Q10. Qiita以外のプラットフォームでも使いたい

**A:**

このシステムはQiitaに最適化されていますが、他のプラットフォームでも使えます：

1. `images/` から最適化済み画像を取得
2. 各プラットフォームにアップロード
3. ログファイルに別セクションを追加（例: `## Zenn`, `## note`）

## 参考リンク

### Qiita公式ドキュメント

- [画像のアップロード・削除方法 | Qiita ヘルプ](https://help.qiita.com/ja/articles/qiita-image-upload)
- [Qiita Markdown記法](https://qiita.com/Qiita/items/c686397e4a0f4f11683d)
- [埋め込み可能なコンテンツ](https://qiita.com/Qiita/items/612e2e149b9f9451c144)

### 画像最適化ツール

- [ffmpeg公式サイト](https://ffmpeg.org/)
- [ImageMagick公式サイト](https://imagemagick.org/)
- [WebP公式ドキュメント](https://developers.google.com/speed/webp)

### Draw.io

- [Draw.io公式サイト](https://www.drawio.com/)
- [Draw.io Desktop](https://github.com/jgraph/drawio-desktop)
- [VS Code Extension: Draw.io Integration](https://marketplace.visualstudio.com/items?itemName=hediet.vscode-drawio)

## 更新履歴

- **2025-11-30**: 初版作成（画像管理システム構築）
