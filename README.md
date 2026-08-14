# zenn-content

Zenn の記事を GitHub 連携で管理するリポジトリ。

## 構成

```
zenn-content/
├── articles/          記事（1ファイル = 1記事）
│   └── ask-ai-prefill-links.md
├── images/            記事内で使う画像
├── tools/             記事に付随するツール（GitHub Pages で公開）
│   └── ask-ai-link-generator.html
├── package.json
└── .gitignore
```

## tools/

`tools/` 配下は GitHub Pages でそのまま配信される。Zenn 側は `articles/` と `books/` しか見ないので、
同居させても記事の管理には影響しない。

| ファイル | 公開URL |
|---|---|
| `tools/ask-ai-link-generator.html` | https://kake-nami.github.io/zenn-content/tools/ask-ai-link-generator.html |

`ask-ai-link-generator.html` は依存ゼロの単一HTML。記事URLと質問文から ChatGPT / Google AI Mode /
Claude / Perplexity のプリフィルリンクを生成し、note / Markdown / HTML の3形式で書き出す。
URL長を数えて 1800 字で警告、2000 字で超過表示する。

:::message
Gemini（`gemini.google.com/app?q=`）は**プリフィル非対応**（2026-08-15 実機確認）。
URLは開くが入力欄が空のままになる。同じ Gemini を使いたい場合は Google AI Mode
（`google.com/search?udm=50&q=`）を使う。ツール側では Gemini を既定オフにしてある。

Google AI Mode にも制約が2つある（同日確認）。

- `q=` は検索クエリなので **`%0A` が落ちる**。改行のまま渡すとURL末尾に次の行が
  直結して壊れる → ツール側で AI Mode のみ改行を空白に自動変換している
- **1ターンの検索応答**なので「まず質問して」が効かない。対話させたいなら
  ChatGPT / Claude を使う
:::

## 初回セットアップ

```bash
npm install
npx zenn preview          # http://localhost:8000 でプレビュー
```

GitHub 連携:

1. GitHub でリポジトリを作成（**Public** 推奨。Private でも可）
2. このディレクトリを push
3. Zenn のダッシュボード → **GitHubからのデプロイ** → リポジトリを連携
4. 連携後、`main` への push で自動デプロイ

## 記事を追加する

```bash
npx zenn new:article --slug my-new-article --title "タイトル" --type tech --emoji 📝
```

## slug の制約

ファイル名がそのまま公開URLの slug になる（`https://zenn.dev/<username>/articles/<slug>`）。

- **半角英小文字 `a-z` / 数字 `0-9` / ハイフン `-` / アンダースコア `_` のみ**
- **12〜50文字**
- 一度公開した slug は変更しない（URLが変わる）

## frontmatter

```yaml
---
title: "記事タイトル"
emoji: "🔗"        # 絵文字1文字。サムネイルに使われる
type: "tech"       # tech（技術記事） or idea（アイデア記事）
topics: ["llm", "javascript"]   # 最大5つ。英数字のみ推奨
published: false   # true にすると公開される
---
```

:::message
`published: false` のまま push すれば下書きとして保存される。プレビューを確認してから
`true` に変えて push、という運用が安全。
:::

## 画像

`images/` に置いて、記事から相対パスで参照する。

```markdown
![](/images/ask-ai/screenshot.png)
```

サイズ上限は 3MB / 枚。画像は Zenn 側にアップロードして URL を貼る方法もある。

## 現在の記事

| slug | タイトル | 状態 |
|---|---|---|
| `ask-ai-prefill-links` | aタグ4本でできる「AIに聞く」ボタンの作り方（?q= プリフィルリンクと llms.txt） | 下書き |

### 公開前にやること

- [x] note 記事へのリンク2箇所を差し替え（冒頭・末尾）
      → https://note.com/clever_lion4185/n/nc2acce158402
- [x] ジェネレータの配布リンクを差し替え
      → `tools/ask-ai-link-generator.html` を GitHub Pages で公開
- [x] GitHub Pages を有効化（Source: `main` / `/ (root)`）
      → https://kake-nami.github.io/zenn-content/tools/ask-ai-link-generator.html
- [x] スクリーンショット2枚を `images/ask-ai/` に配置
      → `generator.png`（7章・ツール画面） / `prefilled-result.png`（7章末・踏んだ結果）
- [x] 各AIサービスの `?q=` を実際に踏んで確認（2026-08-15）
      → ChatGPT / Claude / Perplexity は動作。Gemini のみ非対応 → Google AI Mode に差し替え
- [ ] `images/ask-ai/generator.png` を撮り直す（サービス欄が Gemini のままの旧UI）
- [ ] `published: true` に変更
