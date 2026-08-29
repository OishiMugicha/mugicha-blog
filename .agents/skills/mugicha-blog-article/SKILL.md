---
name: mugicha-blog-article
description: Create and revise Japanese blog articles in the mugicha-blog Astro repository from topics, notes, dictation, or drafts. Use for drafting, polishing, previewing, or publishing files under src/content/blog; do not use for site layout or component changes.
---

# Mugicha Blog Article

## 記事を整える

- 初回の編集前に `src/content.config.ts` と最近の記事を確認し、現在のスキーマと文体に合わせる。
- 音声入力やメモでは、重複、言い直し、フィラーを整理しながら、筆者の主張、一人称、素朴で個人的な語り口を残す。
- ユーザーが述べていない事実、感想、結論を補わない。意味や固有名詞が判断できない場合だけ、必要な箇所を確認する。
- タイトル、短く自然なカード説明文、読みやすい本文を作る。説明文は記事の内容を要約しすぎず、既存記事と同程度の簡潔さにする。

## 記事を保存・修正する

- 新規記事は `src/content/blog/<slug>.md` に置き、内容を表す英語の kebab-case をスラッグに使う。
- frontmatter には `title`、`description`、`date` を設定し、日付はユーザーのローカル日付を `YYYY-MM-DD` 形式で記録する。
- 修正依頼では指定されたタイトル、説明文、本文だけを変更し、無関係な文章やファイルを触らない。

## 表示を確認する

- プレビューを求められたら、リポジトリの `AGENTS.md` に従い `npm run dev -- --background` で開発サーバーを起動する。すでに起動中なら重複起動しない。
- `astro.config.mjs` の `base` と記事スラッグから確認URLを案内し、記事ページとトップページのカードが表示されることを確認する。
- 完了前に `npm run build` を実行し、記事を含む静的ルートが生成されることを確認する。

## Git操作の権限を守る

- 記事作成やプレビューの依頼だけでは、コミットやプッシュを行わない。
- コミットを明示的に依頼された場合は、差分を確認して対象ファイルだけをステージし、日本語のコミットメッセージを使う。
- プッシュは外部変更として扱い、対象リモートとブランチを特定したうえで、ユーザーがそのプッシュを明示的に承認した場合だけ実行する。
