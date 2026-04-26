# mounjaro-lp（マンジャロ オンライン診療 LP）

公開予定：`https://mounjaro.ikegawa-clinic.com/`

静的HTML 1ページ構成（Astro等のビルド不要）。
neurology-lp と異なり、単一ページのため Astro は使わず素のHTMLにしています。

## ファイル

- `index.html` … メインページ（snippet_19_マンジャロLP.html を独立HTML化したもの）

## ローカルプレビュー

```sh
cd "/Users/ikegawamasayuki/Documents/Claude/Projects/池川内科システム/mounjaro-lp"
python3 -m http.server 8000
# → http://localhost:8000/ で確認
```

または直接ブラウザで `index.html` を開く（ファイルプロトコルでもほぼ動作）。

## Cloudflare Pages デプロイ手順（先生作業）

### 1. GitHub リポジトリ作成

```sh
cd "/Users/ikegawamasayuki/Documents/Claude/Projects/池川内科システム/mounjaro-lp"
git init
git add .
git commit -m "Initial commit: マンジャロLP"
gh repo create ikegawa-clinic/mounjaro-lp --public --source=. --remote=origin --push
```

`gh` コマンド未認証なら `gh auth login` を先に実行。

### 2. Cloudflare Pages プロジェクト作成

1. Cloudflare ダッシュボード → **Workers & Pages** → **Create application** → **Pages** → **Connect to Git**
2. リポジトリ `ikegawa-clinic/mounjaro-lp` を選択
3. ビルド設定：
   - Framework preset: **None**（または "Static HTML"）
   - Build command: 空欄
   - Build output directory: `/` または空欄（ルートに `index.html` があるため）
4. **Save and Deploy** → 数十秒で `https://mounjaro-lp.pages.dev/` で公開

### 3. カスタムドメイン（サブドメイン）設定

1. 作成された Pages プロジェクト → **Custom domains** → **Set up a custom domain**
2. `mounjaro.ikegawa-clinic.com` を入力 → **Continue**
3. Cloudflare DNS で `ikegawa-clinic.com` ゾーンが管理されている場合は自動追加。それ以外（ConoHa WING のDNS等）の場合は CNAME を手動追加：
   - レコード種別: CNAME
   - ホスト名: `mounjaro`
   - 値: `mounjaro-lp.pages.dev`（プロジェクトのデフォルトドメイン）
4. SSL 証明書発行を待つ（通常 1〜5分）
5. `https://mounjaro.ikegawa-clinic.com/` でアクセス可能になる

### 4. 既存 WordPress側の対応（任意）

サブドメインが本番LPになる場合、WordPress 側の `/mounjaro-online/`（id:2234, 現在 status: draft）は次の3択：

- **A. 削除して 301 リダイレクト**：Redirection プラグインで `/mounjaro-online/` → `https://mounjaro.ikegawa-clinic.com/`（friday-neurology と同パターン）
- **B. 公開のままダブルで運用**：Canonical タグで重複SEOを回避。LP側に `<link rel="canonical" href="https://mounjaro.ikegawa-clinic.com/">` 追加（既に追記済）
- **C. 下書きのまま放置**：先生がいつでも切り替え可能な状態を保持

おすすめは **A**（友金曜神経内科LPと同じ運用パターン）。

## 画像について

- ヒーロー・特徴カード3枚は WordPress メディアの `/wp-content/uploads/mounjaro-lp/` を絶対URLで参照
- 現状ファイル未配置のため `onerror` で非表示になる（レイアウト崩れなし）
- 配置するには WordPress メディアライブラリにアップロード後、URLを確認

## brain2 / fire2（治療方針セクション）

既存 `/mounjaro/` ページで使われている自前イラスト2枚を絶対URLで参照済。
WP メディアからの参照のため、cross-origin で問題なく表示されます。

## 既存 neurology-lp との違い

| 項目 | neurology-lp | mounjaro-lp |
|---|---|---|
| 構成 | Astro マルチページ | 静的HTML 1ファイル |
| ビルド | `npm run build` | 不要 |
| 公開URL | neurology.ikegawa-clinic.com | mounjaro.ikegawa-clinic.com |
| Cloudflare Pages 設定 | Build command `npm run build`、output `dist` | Build command 空欄、output `/` |

## 更新運用

`snippet_19_マンジャロLP.html` を更新した場合は、本リポジトリの `index.html` も同期する必要があります。
将来的に頻繁に更新するなら、`HP原稿/snippet_19_マンジャロLP.html` から自動で `index.html` を生成するスクリプトを追加してもよいです。
