# むぎちゃの勉強ノート

手書きの読書ノート(PDF)を埋め込んで公開する個人ブログ。

- **静的サイトジェネレータ**: [Hugo](https://gohugo.io/) (extended)
- **テーマ**: [PaperMod](https://github.com/adityatelange/hugo-PaperMod)
- **PDF Viewer**: [PDF.js](https://mozilla.github.io/pdf.js/)
- **ホスティング**: GitHub Pages(GitHub Actions 経由)
- **公開 URL**: <https://oishimugicha.github.io/mugicha-blog/>

## ローカル開発

```bash
# 初回のみ: テーマを取得
git submodule update --init --recursive

# 開発サーバー(下書き含む)
hugo server -D

# 本番ビルド
hugo --minify
```

## 新しい記事を書く

```bash
hugo new content posts/<タイトル>.md
```

`content/posts/` に Markdown を置く。本文中で PDF を埋め込みたい場合は `pdf` ショートコードを使う:

```markdown
{{< pdf src="/pdfs/sample.pdf" height="900px" title="読書ノート: 〇〇" >}}
```

PDF ファイル本体は `static/pdfs/` に置く。
