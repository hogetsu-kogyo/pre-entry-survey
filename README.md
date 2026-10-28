# 入社前アンケート

応募者向けアンケート画面と、管理者向け回答確認画面の静的ファイルです。

## ファイル

- `index.html` — 応募者向けアンケート画面。質問文はHTML/JavaScriptに埋め込まず、Supabase Edge Function `get-survey-questions` から1問ずつ取得します。
- `admin.html` — 管理者ログイン・回答確認画面。判定対象の設問番号は管理者認証後にSupabaseの `survey_settings` から読み込み、判定そのものはデータベースのトリガーで実行します。回答データはSupabaseに保存されます。

## GitHub Pages

このリポジトリをGitHub Pagesで公開する場合、GitHub FreeではリポジトリをPublicにする必要があります。判定条件は公開HTMLに埋め込まず、Supabaseの管理者限定設定とデータベーストリガーで処理します。公開前に、HTML/JavaScriptに秘密鍵・個人情報が含まれていないことを確認してください。

組織 `hogetsu-kogyo`、リポジトリ `pre-entry-survey` の想定URLは次のとおりです。

- 応募者画面: `https://hogetsu-kogyo.github.io/pre-entry-survey/`
- 管理者画面: `https://hogetsu-kogyo.github.io/pre-entry-survey/admin.html`
- ブラウザーから送信されるOrigin: `https://hogetsu-kogyo.github.io`（リポジトリ名のパスはOriginに含まれません）

現在の公開先は `https://hogetsu-pre-entry-survey.pages.dev/` です。新URLが開けることとGitHub Pagesのデプロイ成功を確認してから運用を切り替えてください。切り替え確認が終わるまでは現在の公開先を停止しないでください。

## Supabase側の確認

- `get-survey-questions` と `submit-survey` の許可Originに `https://hogetsu-kogyo.github.io` が含まれていること。
- Supabase Authentication の Redirect URLs に `https://hogetsu-kogyo.github.io/pre-entry-survey/admin.html` が登録されていること。
- 管理者ログイン、パスワード設定メールの戻り先、回答送信、管理画面での回答表示を本番URLでテストすること。
- 質問データはSupabaseの `survey_question_bank` テーブルに保存する。テーブルに対する一般利用者の直接アクセス権限は付与せず、Edge Function経由で取得させる。
- `service_role` キー、secret key、個人情報をHTMLまたは公開リポジトリに含めない。

Supabaseの質問取得関数は1回のリクエストで指定された1問だけを返します。ただし、応募者がブラウザーで順番に受け取る質問を完全に秘密にできるわけではありません。
