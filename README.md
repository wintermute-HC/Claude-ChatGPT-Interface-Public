# Claude-ChatGPT-Interface

Claude Code から OpenAI Codex(ChatGPT 側)を呼び出すプラグイン `openai/codex-plugin-cc` について、SNS 投稿の内容を一次情報で検証した結果と、正しい導入手順をまとめたものです。

- 検証日: 2026-11-14
- 一次情報: <https://github.com/openai/codex-plugin-cc> の README

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

### 未検証の点

- OpenAI 公式ドキュメント・料金ページ(<https://developers.openai.com/codex/pricing>)での裏取りはしていません。プランごとの具体的な上限は未確認です。
- 実際のインストールと動作確認はしていません。上記はすべて README の記載に基づきます。

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

- **使用量**: プラグイン経由の利用は Codex の使用量上限に加算されます。
- **レビューゲート**: `/codex:setup --enable-review-gate` で有効にすると、Claude の応答のたびに Codex がレビューし、問題があれば Claude が止まらず修正を続けます。README は「Claude と Codex の長時間ループになり、使用量上限を急速に消費しうる。セッションを見ていられるときだけ有効にすること」と警告しています。無効化は `/codex:setup --disable-review-gate` です。
- **設定**: プラグインは Codex CLI と同じ設定を使います。プロジェクト単位の `.codex/config.toml` は、そのプロジェクトが信頼済みの場合だけ読み込まれます。
