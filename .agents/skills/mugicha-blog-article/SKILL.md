---
name: mugicha-blog-article
description: Turn freeform Japanese voice-input text into blog articles in the mugicha-blog Astro repository, preserving the author's voice while organizing the writing and saving the article. Also use for revising drafts and previewing articles under src/content/blog; do not use for site layout or component changes.
---

# Mugicha Blog Article

## 記事を整える

- 思いつくまま話した音声入力テキストを受け取り、本人の語り口を残した読みやすい記事に整える。メモや下書きにも同じ方針を使う。
- 初回の編集前に `src/content.config.ts` と最近の記事を確認し、現在のスキーマと文体に合わせる。
- 重複、言い直し、フィラーを整理し、関連する話題をまとめて順序や段落を整える。見出しは読みやすさに役立つ場合だけ付け、短い記事に定型的な構成を押し付けない。脱線も筆者の関心や人柄が伝わる内容なら生かす。
- 構成の整い方より本人の語り口を優先する。一人称、くだけた表現、感情の強さ、ためらいや曖昧さを残す。過度に丁寧な解説文にしたり、推測を断言に変えたりしない。
- ユーザーが述べていない事実、感想、結論を補わない。誤変換は文脈から明らかな場合に直し、意味を左右する不明点や固有名詞だけ確認する。文章をつなぐために理由や因果関係を創作しない。
- タイトル、短く自然なカード説明文、読みやすい本文を作る。説明文は記事の内容を要約しすぎず、既存記事と同程度の簡潔さにする。

## 記事を保存・修正する

- 「記事にして」などの依頼では、整形した原稿を標準で記事ファイルまで保存し、結果を提示する。「原稿だけ」「まだ保存しない」などの指定があればそれに従う。
- 新規記事は `src/content/blog/<slug>.md` に置き、内容を表す英語の kebab-case をスラッグに使う。
- frontmatter には `title`、`description`、`date` を設定し、日付はユーザーのローカル日付を `YYYY-MM-DD` 形式で記録する。
- 修正依頼では指定されたタイトル、説明文、本文だけを変更し、無関係な文章やファイルを触らない。

## 表示を確認する

- コマンドはリポジトリのルートで実行する。`npm` が見つからない場合は未インストールと決めつけず、既存の実行ファイルを確認する。このWindows環境では `C:\Program Files\nodejs\npm.cmd` が利用でき、PowerShellでは `& 'C:\Program Files\nodejs\npm.cmd' run build` のように呼び出せる。別のPCでは存在を確認してから使う。
- 依存パッケージが未導入なら `package-lock.json` に従い `npm ci` で導入する。npmの再インストールや永続的なPATH変更は通常不要。
- プレビューを求められたら、リポジトリの `AGENTS.md` に従い `npm run dev -- --background` で開発サーバーを起動する。すでに起動中なら重複起動しない。
- サーバーの状態確認・ログ確認・停止には、それぞれ `npm run dev -- status`、`npm run dev -- logs`、`npm run dev -- stop` を使う。
- `astro.config.mjs` の `base` と記事スラッグから確認URLを案内し、記事ページとトップページのカードが表示されることを確認する。
- 完了前に `npm run build` を実行し、記事を含む静的ルートが生成されることを確認する。

## Git操作の権限を守る

- 記事作成やプレビューの依頼だけでは、コミットやプッシュを行わない。
- コミットを明示的に依頼された場合は、差分を確認して対象ファイルだけをステージし、日本語のコミットメッセージを使う。
- プッシュは外部変更として扱い、対象リモートとブランチを特定したうえで、ユーザーがそのプッシュを明示的に承認した場合だけ実行する。
