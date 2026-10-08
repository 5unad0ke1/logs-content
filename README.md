# logs-content

[砂時計/log](https://logs.sunadokei.dev) の記事データ。ブログシステムは [5unad0ke1/logs-site](https://github.com/5unad0ke1/logs-site)。

`main` に push すると GitHub Actions(`.github/workflows/deploy.yml`)が logs-site の `main` を取得してビルドし、GitHub Pages に公開する。
logs-site(システム)だけ更新したときは、Actions の Deploy を手動実行(Run workflow)して反映する。

## ローカルで書く

```bash
git clone https://github.com/5unad0ke1/logs-site.git
cd logs-site
npm install
npm run dev   # content/ が無ければ、このリポジトリを content/ に clone する
```

記事は `logs-site/content/`(= このリポジトリ)の中で書いてコミット・push する。

## 記事の置き方

```
posts/<slug>/index.mdx   → https://logs.sunadokei.dev/log/<slug>/
posts/<slug>/*.png       → 記事で使う画像は同じフォルダに置く
```

### frontmatter

```yaml
---
title: カメラシェイクを、生成・管理・実行の3層に分ける # 必須
date: 2026-08-18 # 必須
updated: 2026-08-20 # 任意。更新日
tags: [architecture, unity] # 任意
description: 一覧や OGP に出す説明 # 任意。無ければ本文の冒頭から作る
cover: ./cover.png # 任意。OGP 画像
draft: true # 任意。true だと本番に出ない(npm run dev では見える)
---
```

### 外部の記事(Zenn など)を一覧に並べる

本文は書かず、frontmatter に `externalUrl` を入れる(Zenn・Qiita・Docswell など)。一覧の出典はホスト名の先頭から自動で付く(zenn.dev → zenn、www.docswell.com → docswell)。一覧ではそこへ(別タブで)飛び、サイト内のページは作らない。前後記事のナビにも入らない。RSS にはリンク先を外部 URL にして載る。

```yaml
# posts/zenn-ugui-design/index.md(フォルダ名は自由)
---
title: Unity uGUIで挑戦したいUI設計 [経験談]
date: 2025-10-20T11:03:40+09:00
tags: [unity, ugui]
externalUrl: https://zenn.dev/5unad0ke1/articles/56c4ecd49f3491
---
```

### 使えるコンポーネント(import 不要)

```mdx
import fig1 from './layers.png';

<Figure src={fig1} alt="3層構成" caption="キャプション" />  {/* fig.N は自動 */}
<LinkCard url="https://example.com" />                      {/* OGP はビルド時取得 */}
<Video youtube="動画ID" caption="キャプション" />
<Note>補足や注意点</Note>
```

コードブロックは ` ```cs title="ShakeHandle.cs" ` のように `title` でファイル名を出せる。

### リンクカードの OGP キャッシュ

`.cache/ogp.json` に取得結果が保存される。ローカルでビルド(または dev で表示)して増えた分はコミットしておくと、Actions でのビルド時に外部サイトへ取りに行かずに済む。取り直したいときは該当 URL の項目を消す。
