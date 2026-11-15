# Claude-ChatGPT-Interface

Claude Code から OpenAI Codex を呼び出すプラグイン `openai/codex-plugin-cc` について、一次情報に基づく検証結果と導入手順をまとめたものです。

- 検証日: 2026-11-14
- 一次情報:
  - <https://github.com/openai/codex-plugin-cc> の README とソースコード(バージョン 1.0.6、最新コミット 2026-07-07)
  - <https://developers.openai.com/codex/pricing>(検索結果に出た同ページの要約で確認。ページ自体は直接開けていません)

## 検証結果

| 投稿の主張 | 判定 | 一次情報での記載 |
| --- | --- | --- |
| OpenAI 公式のプラグイン `codex-plugin-cc` がある | 正しい | GitHub の `openai` 組織配下に公開リポジトリとして実在(Apache-2.0)。説明文は "Use Codex from Claude Code to review code or delegate tasks." |
| `/plugin marketplace add openai/codex-plugin-cc` | 正しい | README に同じコマンドが記載 |
| `/plugin install codex@openai-codex` | 正しい | README に同じコマンドが記載 |
| `/codex:setup` と打つ | 正しいが手順が1つ抜けている | README ではインストール後に `/reload-plugins` を実行してから `/codex:setup` |
| ChatGPT のアカウントでログイン | 正しい | 要件は "ChatGPT subscription (incl. Free) or OpenAI API key."。未ログイン時は `!codex login` |
| 「役割分担して」と送ると自動で分担される | 公式機能ではない | README に自動の役割分担機能の記載はない。委任は `/codex:rescue` などをユーザーが呼び出す形(自然文で Codex への委任を頼むことは可能) |
| ChatGPT 側の使用量は ChatGPT のプランの範囲で使われる | おおむね正しい | README の記載は "Usage will contribute to your Codex usage limits." |

### ソースコードの点検結果

リポジトリを取得し、プラグインの全スクリプト(約5,300行)を点検しました。不審な点はありませんでした。

- **出所**: 最新コミットは `openai.com` のアドレスによるもので、マニフェストの作者は OpenAI です。2026年3月末の OpenAI によるリリースとして複数の技術メディアが報じています。
- **通信**: プラグイン自身は外部へ通信しません。ローカルの Unix ソケット/名前付きパイプで `codex app-server` とやり取りするだけで、OpenAI との通信は Codex CLI が行います。
- **起動するプロセス**: `codex`、`git`、`node`、`npm --version` のみです。認証情報や SSH 鍵を読むコードはありません。
- **フック**: セッション開始・終了時の後片付けと、停止時のレビューゲートの3つです。レビューゲートは既定で無効です。
- **Codex のインストール**: `/codex:setup` は `npm install -g @openai/codex` を自動では実行せず、必ず確認を求めます。

### 料金と使用量

プラグイン経由の利用は、ChatGPT プランに含まれる Codex の使用量上限から消費されます。

| プラン | 料金 | Codex の扱い |
| --- | --- | --- |
| Free | $0 | デスクトップアプリで利用可(段階的提供)。回数の記載なし |
| Plus | $20/月 | 5時間枠あたりの目安がモデル別に示され、週次上限もありうる |
| Pro | $100 / $200 / $500/月 | 現時点で5時間枠の上限なし |

Plus と Pro は上限到達後に追加クレジットを購入できます。数値は変動するので、実際の残量は Codex の使用量ダッシュボードで確認してください。

### 未検証の点

- 実際のインストールと動作確認はしていません(クラウド環境では実行できないため)。
- 料金ページは直接開けておらず、上の表は検索結果に出た同ページの要約に基づきます。

## 正しい導入手順

パソコンの Claude Code で実行します(クラウドセッションでは実行できません)。

### 前提

- Node.js 18.18 以上
- ChatGPT のアカウント(無料プラン含む)または OpenAI API キー

### 手順

1. マーケットプレイスを追加する

   ```
   /plugin marketplace add openai/codex-plugin-cc
   ```

2. プラグインをインストールする

   ```
   /plugin install codex@openai-codex
   ```

3. プラグインを再読み込みする(投稿で抜けていた手順)

   ```
   /reload-plugins
   ```

4. セットアップを実行する

   ```
   /codex:setup
   ```

   Codex CLI が未インストールで npm が使える場合は、ここでインストールを提案されます。自分で入れる場合は `npm install -g @openai/codex` です。

5. Codex にログインしていない場合はログインする

   ```
   !codex login
   ```

6. 動作確認をする

   `/agents` に `codex:codex-rescue` が表示されていれば導入済みです。README が勧める最初の実行は次のとおりです。

   ```
   /codex:review --background
   /codex:status
   /codex:result
   ```

## 使えるコマンド

| コマンド | 内容 |
| --- | --- |
| `/codex:review` | 未コミットの変更、または `--base <ref>` で指定したブランチ差分を Codex がレビューする(読み取り専用) |
| `/codex:adversarial-review` | 設計判断や前提に踏み込んで反論するレビュー。フォーカスを文章で指定できる(コードは修正しない) |
| `/codex:rescue` | 調査や修正などのタスクを Codex に委任する |
| `/codex:transfer` | 現在の Claude Code セッションから Codex のスレッドを作り、`codex resume <session-id>` を表示する |
| `/codex:status` | 実行中・最近の Codex ジョブを表示する |
| `/codex:result` | 完了したジョブの結果を表示する |
| `/codex:cancel` | 実行中のバックグラウンドジョブを中止する |
| `/codex:setup` | Codex のインストールと認証を確認する。レビューゲートの有効化・無効化もここで行う |

## 役割分担のやり方

投稿の「考える・チェック = Claude / 作る・直す = ChatGPT」は公式機能ではなく使い方の一例です。実際には次のように自分で指示します。

- Codex に作業させる: `/codex:rescue <依頼内容>`
- Codex にレビューさせる: `/codex:review` または `/codex:adversarial-review`

## 注意点

- **`/codex:rescue` は既定で書き込み可能**: 読み取り専用を明示的に頼まない限り `--write` が付き、Codex は承認なし(`approvalPolicy: never`)で作業フォルダ内のファイルを編集します。レビュー系コマンドは読み取り専用です。
- **`/codex:transfer` は会話ログを渡す**: `~/.claude/projects` 配下の Claude Code のセッション記録が Codex(OpenAI 側)に渡されます。
- **使用量**: プラグイン経由の利用は Codex の使用量上限に加算されます。
- **レビューゲート**: `/codex:setup --enable-review-gate` で有効にすると、Claude の応答のたびに Codex がレビューし、問題があれば Claude が止まらず修正を続けます。README は「Claude と Codex の長時間ループになり、使用量上限を急速に消費しうる。セッションを見ていられるときだけ有効にすること」と警告しています。無効化は `/codex:setup --disable-review-gate` です。
- **設定**: プラグインは Codex CLI と同じ設定を使います。プロジェクト単位の `.codex/config.toml` は、そのプロジェクトが信頼済みの場合だけ読み込まれます。
