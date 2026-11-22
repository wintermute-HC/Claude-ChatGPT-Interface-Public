# Claude-ChatGPT-Interface-Public

[English](README.md) | 日本語

Claude Code から OpenAI Codex を呼び出すプラグイン `openai/codex-plugin-cc` について、公式資料・ソースコードで確認した内容と、資料に記載された導入手順をまとめたものです。

本書には、資料・コードの確認結果と、2026-11-15に実施した範囲を限定した試験結果を記載します。Claude Code経由の導入・呼び出しと、全面的な安全性監査は未実施です。本プロジェクト固有の運用規則は [CLAUDE.ja.md](CLAUDE.ja.md) に定めます。

- 資料確認・限定試験日：2026-11-15
- 実際に本文を確認した一次資料：
  - [公式README（確認したコミットに固定）](https://github.com/openai/codex-plugin-cc/blob/db52e28f4d9ded852ab3942cea316258ae4ef346/README.md)
  - [プラグインのマニフェスト](https://github.com/openai/codex-plugin-cc/blob/db52e28f4d9ded852ab3942cea316258ae4ef346/plugins/codex/.claude-plugin/plugin.json)：作者欄OpenAI、バージョン1.0.6
  - 下記「実装の確認範囲」「注意点」に示すソースファイル
  - [公式料金ページ](https://learn.chatgpt.com/docs/pricing)：従来の `developers.openai.com/codex/pricing` から転送されたページの本文
- ソースの確認対象：コミット `db52e28f4d9ded852ab3942cea316258ae4ef346`（コミット日2026-07-07）。以下の実装説明はこの版についての記録で、将来の版にも同じ動作を保証するものではありません。

## 後続の実験記録

[2026-11-20 通信・相互検証試験](docs/2026-11-20-communication-test.md) に、Mac上の共有ファイル通信、Codexの表示停止と再開、合成データでの相互検証を記録しました。以下の11月15日のプラグイン調査・限定試験とは実施範囲が異なります。

[11月21日の追試：Colabでの再評価](docs/2026-11-21-colab-reevaluation.md)

## 既報と本プロジェクトの位置付け

本リポジトリは、**既報の独立した追試、確認記録、運用案**を共有するものです。リンク問題の初発見や、人の承認を組み込んだClaude–Codex連携の発明は主張しません。本規則による安全性・誤り率の改善を示す比較実験も実施していません。

リンク問題は[Issue #597](https://github.com/openai/codex-plugin-cc/issues/597)と[Issue #828](https://github.com/openai/codex-plugin-cc/issues/828)で既報です。修正案の[PR #834](https://github.com/openai/codex-plugin-cc/pull/834)は2026-11-15の確認時点で未統合でした。人の承認を含む先行例には[cross-agent-harness](https://github.com/harness17/cross-agent-harness)や[codex-claude-handoff](https://github.com/siglernir-ai/codex-claude-handoff)があり、承認の粒度や条件は本規則と異なります。

出典、確認水準、調査の限界は[既報・関連事例の調査記録](RELATED_WORK.ja.md)にまとめています。[English summary](RELATED_WORK.md)もあります。

## 意見・独立検証・支援の募集

[Issues](https://github.com/wintermute-HC/Claude-ChatGPT-Interface-Public/issues)で、アイデア、訂正、改善提案、独立検証の報告を受け付けます。ClaudeデスクトップのCode環境での試験と、CLI・下位スクリプト経由の試験は区別してください。

意見募集を目的としているため、プルリクエストは無効です。投稿者に書き込み権限を与えるものではなく、採用・変更は所有者が判断します。時間資源に限りがあるため、全件の再現試験や継続サポートは約束できません。

実験報告には、日付、OS・アプリ・プラグインの版、手順、期待結果、実際の結果、変更ファイルを記載してください。架空データを使い、ログから認証情報・個人情報・会話履歴を除いてください。未公開研究や機密データを使わず、実施権限のある環境と範囲で検証してください。適切な試験環境を用意できない場合、資料の査読や意見だけでも歓迎します。

### スポンサーへの関心

文書整備と独立検証への金銭的支援を歓迎します。受取先は未登録のため、現時点では決済窓口はありません。関心のある方は、決済情報や個人情報を書かずにIssueでお知らせください。支援は編集権限、検証結論への影響、特定の結果の保証を伴いません。受取先の登録後に正式な支援方法を掲載します。

## 公式資料で確認した内容

| 確認事項 | 判定・条件 | 一次情報での記載 |
| --- | --- | --- |
| プラグインの公開元 | OpenAIの公開リポジトリ | `openai/codex-plugin-cc` を確認。ライセンスは [Apache-2.0](https://github.com/openai/codex-plugin-cc/blob/db52e28f4d9ded852ab3942cea316258ae4ef346/LICENSE) |
| `/plugin marketplace add openai/codex-plugin-cc` | 正しい | README に同じコマンドが記載 |
| `/plugin install codex@openai-codex` | 正しい | README に同じコマンドが記載 |
| `/codex:setup` の実行順序 | 再読み込み後に実行 | README ではインストール後に `/reload-plugins` を実行してから `/codex:setup` |
| 認証方法 | ChatGPTアカウントまたはAPIキー | プラグインREADMEは無料プランも記載。ただし現在の公式料金ページはFreeの利用範囲をデスクトップアプリとしており、無料アカウントでCLI経由のプラグインを利用できるとは保証しない。未ログイン時は `!codex login` |
| 作業の委任 | コマンドまたは自然文で依頼 | 公式READMEは `/codex:rescue` と自然文による委任例を記載。本プロジェクトの役割・変更承認は `CLAUDE.md` に定める |
| 使用量・課金 | 認証方法によって異なる | ChatGPT認証はCodexのプラン条件、APIキー認証はAPI料金が適用される |

### 実装の確認範囲

今回確認したのは、公式README、マニフェスト、依存関係の宣言、作業委任の指示、承認・サンドボックス指定、セッション取り込み、フック定義、レビューゲート、セットアップ指示、レビュー資料の収集、状態ファイル・ローカル通信の関係箇所です。全コード・依存関係の監査や安全性の保証を行ったものではありません。以下は静的な確認結果で、実行結果ではありません。

- **出所**：`openai` 組織の公開リポジトリで、マニフェストの作者欄はOpenAI、バージョンは1.0.6です。これだけで実行時の安全性が保証されるわけではありません。
- **連携方式**：公式READMEでは、同じマシンのCodex CLIとCodex app-serverを利用すると説明されています。ローカルでの連携は、サービスへの情報送信がないことを意味しません。
- **フック**：[フック定義](https://github.com/openai/codex-plugin-cc/blob/db52e28f4d9ded852ab3942cea316258ae4ef346/plugins/codex/hooks/hooks.json)に `SessionStart`、`SessionEnd`、`Stop` があります。[設定初期値](https://github.com/openai/codex-plugin-cc/blob/db52e28f4d9ded852ab3942cea316258ae4ef346/plugins/codex/scripts/lib/state.mjs)ではレビューゲートが無効です。
- **インストール前の確認指示**：[セットアップのコマンド指示](https://github.com/openai/codex-plugin-cc/blob/db52e28f4d9ded852ab3942cea316258ae4ef346/plugins/codex/commands/setup.md)では、Codexが未導入でnpmが利用可能な場合、インストールするかユーザーに質問するよう定められています。プロンプト上の指示と技術的な強制は区別します。

### 料金と使用量

ChatGPTで認証する場合とAPIキーで認証する場合では、利用条件・課金方式が異なります。

- **ChatGPT認証**：契約プランのCodexの利用条件が適用されます。Freeの利用範囲について、プラグインREADMEと現在の公式料金ページでは説明の粒度が異なるため、無料アカウントでこのプラグインを使えるとは保証しません。
- **APIキー認証**：API料金が適用され、ChatGPTサブスクリプションの利用枠とは区別されます。
- **上限・追加利用**：プラン別の利用条件は変更されるため、本書に固定の金額・利用回数は掲載しません。最新条件は[公式料金案内](https://learn.chatgpt.com/docs/pricing)、残量は[使用量ダッシュボード](https://chatgpt.com/codex/settings/usage)で確認してください。

## 実施した試験と未確認範囲

### 試験環境と結果

Mac上のNode.js v26.13.1、npm 11.20.0、Codex CLI 0.169.0で実施しました。対象は上記コミットのプラグインです。ユーザーの研究データや会話履歴を使わず、試験用のリポジトリと無害な文字列を使用しました。

| 試験 | 結果 | 確認できた範囲 |
| --- | --- | --- |
| 公式の自動テスト `npm test` | 91件成功、失敗0、スキップ0 | 模擬Codexを使う試験を含む。実サービス全機能の成功を示すものではない |
| プラグインの下位スクリプトから実Codexへの接続 | 成功 | setupの結果が `ready: true`。ChatGPT認証を確認 |
| 実Codexへの試験用タスク送信 | 成功 | 指定した文字列が返り、返答の変更ファイル一覧は空。ファイル操作を依頼せず、書き込みオプションなし |
| 実Codexによる通常レビュー | 成功 | 試験用の加算を減算に変えた誤変更を指摘。レビュー後も対象ファイルの内容は変化なし |
| 未追跡リンクを通じた作業フォルダ外の本文収集 | 再現 | ローカルで無害なマーカーを収集することを確認。マーカーのサービスへの送信は未試験 |

自動テストは[公式テスト群](https://github.com/openai/codex-plugin-cc/tree/db52e28f4d9ded852ab3942cea316258ae4ef346/tests)を実行しました。実サービスの試験は `codex-companion.mjs` の `setup`、書き込み指定なしの `task`、`review` を試験用リポジトリに対して実行したものです。Claude Codeのスラッシュコマンドから実行した試験ではありません。

初回のsetupは実行環境のアクセス制限でCodexの状態データベースを開けず失敗し、必要な許可を得た再実行で成功しました。自動テストも制限下の実行を中断し、許可後の再実行結果を上表に記載しています。

### 独立して再現した既知の情報収集範囲の問題

この問題は[既報 #597](https://github.com/openai/codex-plugin-cc/issues/597)・[#828](https://github.com/openai/codex-plugin-cc/issues/828)の追試であり、新発見ではありません。リンク除外による対策も[PR #834](https://github.com/openai/codex-plugin-cc/pull/834)で提案済みです。

`/codex:adversarial-review` の資料収集処理では、未追跡のシンボリックリンクを追従し、作業フォルダ外のリンク先の本文を取り込む場合があります。無害な試験データを使った `collectReviewContext` のローカル実行で、フォルダ外に置いたマーカー本文が収集結果に含まれることを再現しました。

実装上、この収集結果はadversarial reviewのプロンプトに組み込まれます。機密情報やユーザーの既存ファイルを使う試験、マーカーを実際にサービスへ送信する試験は行っていません。この結果だけで遠隔攻撃が可能とは判断しません。

レビュー前に未追跡ファイルとリンク先を確認し、共有対象外の情報へのリンクをレビュー対象に含めないでください。根本的な対策は、収集処理でリンクを除外するか、リンク先の実パスが作業フォルダ内か検証することです。今回、プラグイン自体への修正は行っていません。

根拠：[未追跡ファイルの収集処理](https://github.com/openai/codex-plugin-cc/blob/db52e28f4d9ded852ab3942cea316258ae4ef346/plugins/codex/scripts/lib/git.mjs#L197)、[プロンプトへの組み込み](https://github.com/openai/codex-plugin-cc/blob/db52e28f4d9ded852ab3942cea316258ae4ef346/plugins/codex/scripts/codex-companion.mjs#L409)。

### 未確認の範囲

- Claude Code経由の導入・呼び出し（ClaudeデスクトップのCode環境を含む）。シェルからの試験では独立したClaude Code実行ファイルを確認できませんでしたが、デスクトップ内のCodeの利用可否を否定するものではありません。その後のCode画面への接続確認も、プラグインの一連の動作試験とは区別します。
- 実モデルによる書き込み権限の境界試験、実会話履歴のtransfer、実際のレビューゲートによるClaude Codeとの連携。
- 通信の捕捉・送信先の検証、Codex/Claude本体、依存パッケージ本体・配布物の監査、Windowsでの動作。
- 状態ファイルの改変、ローカル通信の入力サイズ・接続権限、同時実行の競合などの網羅的な検証。
- APIキー認証での実サービス利用、無料プラン等の個別アカウントでの利用可否、請求額。

以上の結果は、全機能の正常性や安全性全般を保証するものではありません。

## 公式READMEに記載された導入手順

以下は、対象リポジトリにアクセスできるパソコンのClaude Codeで実行する手順です。クラウド環境での実行可否は、その環境の構成・権限によります。

### 前提

- Node.js 18.18 以上
- Codex CLIを利用できるChatGPTアカウント、またはOpenAI APIキー。無料プランでの利用可否は、公式料金案内と実際のアカウント条件を確認してください。

### 手順

1. マーケットプレイスを追加する

   ```
   /plugin marketplace add openai/codex-plugin-cc
   ```

2. プラグインをインストールする

   ```
   /plugin install codex@openai-codex
   ```

3. プラグインを再読み込みする

   ```
   /reload-plugins
   ```

4. セットアップを実行する

   ```
   /codex:setup
   ```

   公式のセットアップ指示では、Codex CLI が未インストールで npm が使える場合、インストールするか質問することになっています。自分で入れる場合は `npm install -g @openai/codex` です。

5. Codex にログインしていない場合はログインする

   ```
   !codex login
   ```

6. 読み込み状態とレビューの実行結果を確認する

   以下は実施するための手順であり、本書で実行済みとするものではありません。

   `/agents` に `codex:codex-rescue` が表示されることを確認します。表示だけで認証や実行の成功までは確認できません。公式READMEが勧める最初の実行は次のとおりです。完了後に `/codex:result` で結果を確認してください。

   ```
   /codex:review --background
   ```

   続いて `/codex:status` で進捗を確認し、完了してから `/codex:result` で結果を取得します。レビュー対象がない場合やエラーの場合もあるため、起動表示だけで成功と判断しません。

## 公式READMEに記載されたコマンド

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

## 役割分担と承認

このプロジェクトでは、ClaudeとCodexの役割を固定しません。双方が独立分析、推論、コード作成、実装、検証を担当できます。重要な判断では可能な範囲で相互検証し、最終判断はユーザーが行います。

- 調査・レビューの段階では、既存ファイルを変更しません。
- 既存ファイルは原則として別名保存とし、上書きは対象を指定した明示的な承認がある場合に限ります。
- Git commitとpushは、ローカルのファイル変更とは別に承認を得ます。
- ファイル変更前に、対象、理由、差分、影響、保存先、実行担当AIを提示し、ユーザーの明示的な承認を得ます。
- 自動委任によるファイル変更は行いません。変更は、対象・内容・実行者についてユーザーが明示的に承認した範囲に限定します。
- Codexへの作業委任には `/codex:rescue`、レビューには `/codex:review` または `/codex:adversarial-review` を使用できます。ただし、コマンドの実行自体を変更承認の代わりにはしません。
- `/codex:transfer` は明示的な承認がある場合、レビューゲートは明示的な依頼がある場合に限り使用します。

詳細は [CLAUDE.ja.md](CLAUDE.ja.md) に従います。これらは運用上の規則であり、プラグインやOSの権限制御を代替するものではありません。

## 注意点

- **作業委任の書き込み権限**：この版の `codex-rescue` エージェントには、Claudeが自発的にCodexへ委任するよう促す指示があります。また、読み取り専用または変更を伴わないレビュー・診断・調査の依頼を除き、`--write` を付ける指示があります。実装は `--write` がある場合に `workspace-write`、ない場合に `read-only` を指定し、承認ポリシーの既定値は `never` です。書き込み可能な場合、許可範囲内の編集で追加確認が入るとは限りません。本プロジェクトでは、変更前にユーザー承認を得ます。調査時は読み取り専用を明示し、実際の権限も確認してください。承認条件を権限設定で保証できない場合は、`CLAUDE.md` 第7節に従って変更操作を停止します。レビュー系コマンドは公式READMEで読み取り専用とされています。
- **読み取り専用の意味**：対象ソースを修正しないことと、情報の収集範囲やサービスへの送信は別です。上記のリンク先収集の問題に注意してください。また、プラグインは状態・ジョブ・ログを保存し、終了時に削除・プロセス停止を行う処理があります。「読み取り専用」は一切の状態ファイルを書かないという意味ではありません。
- **会話履歴の引き継ぎ**：`/codex:transfer` は `~/.claude/projects` 配下の指定されたClaudeセッションを、ローカルのCodex app-serverのセッション取り込み機能に渡します。プラグインからローカルapp-serverへの取り込み要求を確認しましたが、Codex内部の取り込み処理や通信の実測は行っていません。取り込んだ履歴をCodexで利用すると、サービスへの情報送信が伴う可能性があります。実行前に対象履歴と共有範囲を確認し、明示的な承認を得ます。
- **使用量と課金**：ChatGPTでの認証では契約プランのCodex利用条件が適用されます。APIキーでの認証ではAPI料金が適用されます。現在の上限と残量は公式料金案内と使用量ダッシュボードで確認してください。
- **レビューゲート**：`/codex:setup --enable-review-gate` で有効にすると、Claude Codeの停止時の `Stop` フックでCodexレビューを実行します。指摘がある場合に加え、レビュー失敗・タイムアウト・出力形式の不備でも停止をブロックする経路があります。一方、セットアップ状況によってはレビューを実行せず戻る場合があります。自動で修正の成功を保証する機能ではありません。長時間のループや使用量消費につながるため、本プロジェクトでは明示的な依頼がある場合に限り有効にし、監視できるときに使用します。無効化は `/codex:setup --disable-review-gate` です。
- **設定と承認規則**：プラグインは同じマシンのCodex CLIと認証状態を使用し、その設定を読み込みます。ただし、処理ごとに承認ポリシーやサンドボックスを明示指定する実装もあります。プロジェクト単位の `.codex/config.toml` は信頼済みのプロジェクトでのみ読み込まれます。`CLAUDE.md` の規則だけで技術的な権限制御が保証されるわけではありません。

確認した一次資料（2026-11-15、プラグインのコミット `db52e28f4d9ded852ab3942cea316258ae4ef346`）：

- [公式README](https://github.com/openai/codex-plugin-cc/blob/db52e28f4d9ded852ab3942cea316258ae4ef346/README.md)
- [作業委任エージェント](https://github.com/openai/codex-plugin-cc/blob/db52e28f4d9ded852ab3942cea316258ae4ef346/plugins/codex/agents/codex-rescue.md)
- [タスクの実行権限](https://github.com/openai/codex-plugin-cc/blob/db52e28f4d9ded852ab3942cea316258ae4ef346/plugins/codex/scripts/codex-companion.mjs)
- [承認ポリシーとセッション取り込み](https://github.com/openai/codex-plugin-cc/blob/db52e28f4d9ded852ab3942cea316258ae4ef346/plugins/codex/scripts/lib/codex.mjs)
- [停止時のレビューゲート](https://github.com/openai/codex-plugin-cc/blob/db52e28f4d9ded852ab3942cea316258ae4ef346/plugins/codex/scripts/stop-review-gate-hook.mjs)
- [公式料金案内](https://learn.chatgpt.com/docs/pricing)
