# Claude–Codex連携：既報・関連事例の調査記録

[English summary](RELATED_WORK.md) | 日本語（詳細）

調査日：2026-11-15 JST

## 結論

GitHubにも、それ以外にも、今回の内容と重なる既報・実装・運用例・研究が存在する。

1. 未追跡シンボリックリンクによるフォルダ外本文の収集は既報で、今回の再現は既報の独立した追試に位置付けられる。
2. ClaudeとCodexの相互レビュー、役割交代、人の承認、変更範囲の限定も、先行する公開プロジェクトにある。
3. 記録を日英で整理した実用上の価値と、研究上の新規性は別である。現時点で「同じ研究がない」「この組み合わせは独自」「安全性改善が実証された」とは判断できない。
4. 本調査は資料確認であり、関連プロジェクトの実行や修正PRの試験は行っていない。調査後、本記録と既報の引用を公開文書へ反映した。

## 調査方法と確認水準

- 先行する調査要約を、確定情報ではなく確認対象として扱った。
- GitHubのIssue・PRは公開APIで本文・状態・日時を取得し、重要な修正案は差分も確認した。
- 検索結果だけで結論を出さず、著者自身の説明・公開規則・原著論文を読んだ。
- 一部のGitHubページでWeb取得ツールが失敗した場合、公開APIまたは公開rawファイルを使った。
- 外部プロジェクトのインストールや実行、修正PRのテスト再実行は行っていない。「作者が成功と報告している」ことと、こちらで動作を確認したことは区別する。
- 英語・日本語の検索と、資料から辿れる関連リンクに範囲を限定した。世界中の未公開資料まで含む網羅性や、厳密な先取権を証明する調査ではない。

## 1. GitHub上の直接重なる既報

| 一次資料 | 今回確認した内容 | 今回の記録との関係 |
| --- | --- | --- |
| [openai/codex-plugin-cc Issue #597](https://github.com/openai/codex-plugin-cc/issues/597) | 2026-08-05、同じ `db52e28` を対象に、フォルダ外のリンク先本文の取り込みと再現を報告。書き込み可能なrescue既定値、バックグラウンド処理等も論点にしている | リンク問題とrescueの注意は既報。今回確認できた資料では、リンク問題は#828以前から記載されている |
| [Issue #828](https://github.com/openai/codex-plugin-cc/issues/828) | 2026-10-28 JST、1.0.6 / `db52e28`。外側のマーカー本文が収集結果に入る再現と、`lstat`等による対策を報告。APIではopen、コメント0件 | 今回のマーカーによる再現と直接重なる |
| [PR #834](https://github.com/openai/codex-plugin-cc/pull/834/files) | `statSync`を`lstatSync`に変え、シンボリックリンクと通常ファイル以外を除外。通常・軽量の両収集経路に対する回帰テストを追加。APIではopen、未マージ | 「リンクを除外する」という対策も既出。PRの作成はUTCで10月29日、JSTでは10月30日 |
| [trevoraspencer/codex-plugin-cc PR #8](https://github.com/trevoraspencer/codex-plugin-cc/pull/8) | 2026-10-20 JSTにマージ済み。外側に解決されるリンクを除外し、内側のリンクは収集する設計。関連差分のファイルを確認 | 公式PRと対策の範囲が異なる。派生版でのマージを公式版の修正完了と混同しない |
| [tmathura/copilot-plugin-cc PR #148](https://github.com/tmathura/copilot-plugin-cc/pull/148) | 2026-10-27 JSTにマージ済み。Windowsのjunction等による実パスの境界と`.git`配下を扱う。実装・試験記録を含むPR | 関連する同種の境界問題だが、別プラグイン・別の条件。公式Codex版の修正ではない |

### #597の閉鎖についての重要な補正

#597のイベントAPIでは、投稿者の `ddh785` 自身が2026-08-05に閉じている。状態理由は `not_planned`。

したがって「not plannedで閉じられた」は確認できるが、「OpenAIの保守者が内容を否定した／修正を拒否した」とは、この記録から判断できない。#828の説明をそのまま保守者の判断に読み替えてはいけない。

### 修正案の検証状況について

#834のテスト成功はPR投稿者の報告であり、今回こちらで再実行した結果ではない。コメント欄にはCodexレビューの失敗を表示するbotコメントもある。これだけから、修正が誤りであるとも、人によるレビューを通過済みであるとも判断しない。

調査時の公式`main`は依然として `db52e28f4d9ded852ab3942cea316258ae4ef346` だった。[公式コミット](https://github.com/openai/codex-plugin-cc/commit/db52e28f4d9ded852ab3942cea316258ae4ef346)

## 2. 権限・承認に関する既報

| 一次資料 | 本文で確認した論点 | 判定上の注意 |
| --- | --- | --- |
| [Issue #75](https://github.com/openai/codex-plugin-cc/issues/75) | Claude側の`.claude/settings.json`で拒否したファイルを、rescue経由では読めたという報告 | 投稿者の再現報告を確認。今回の環境で追試した事実ではない |
| [Issue #673](https://github.com/openai/codex-plugin-cc/issues/673) | フォルダ外への書き込みが拒否される制約と、その説明不足 | 「境界を越えて書ける」という問題ではなく、制約の文書化に関する報告 |
| [PR #742](https://github.com/openai/codex-plugin-cc/pull/742) | sandboxを選択する引数と、再開したthreadの権限の照合等を提案 | IssueではなくPR。openであり、公式に採用された仕様とは限らない |
| [PR #494](https://github.com/openai/codex-plugin-cc/pull/494) | 書き込み時の承認ポリシーと自動レビュー担当を設定する案 | IssueではなくPR。自動審査と人の明示承認は同じではない |

これらから、権限や承認の問題意識自体は新しくない。一方、投稿者ごとの再現条件・版・提案をまとめて「全環境で同じ問題が確定」とは扱わない。

## 3. GitHub上の相互検証・人の承認の先行例

### harness17/cross-agent-harness

[リポジトリ](https://github.com/harness17/cross-agent-harness)、[codex-dev規則](https://github.com/harness17/cross-agent-harness/blob/main/.claude/skills/codex-dev/SKILL.md)を確認した。承認された設計と限定した作業範囲をCodexへ渡し、commit/pushをさせず、差分と検証結果を確認してからユーザーにcommit可否を尋ねる手順がある。

「人の承認」「範囲限定」「別AIによるレビュー」が重なる。各ファイル変更前に毎回承認するという今回の規則と完全に同一とは限らないが、承認を組み込んだClaude–Codex連携の先行例である。リポジトリ作成は2026-05-17 JST。READMEの確認した最終変更は2026-06-22 JST。

### Whisker17/claude-codex-loop

[README](https://github.com/Whisker17/claude-codex-loop)には、Claudeによる設計とCodexの独立レビュー、ユーザーによる設計の承認、その後のCodex実装とClaudeレビューがある。人の承認は実装段階に入るゲートであり、変更1回ごとの承認とは区別する。READMEの最終変更は2026-03-31 JST。

### siglernir-ai/codex-claude-handoff

[README](https://github.com/siglernir-ai/codex-claude-handoff/blob/main/README.md)には、役割の交換、実装者と査読者の分離、範囲と証拠の記録、人によるcommit・push・公開等の承認が明記されている。今回の運用案と複数の点で重なる。READMEの確認した最終変更は2026-10-14 JST。

いずれも本文の方針・仕様を確認したものであり、仕組みが確実に実装・強制されるかは今回試験していない。

### その他の近接例

[Dong-skills](https://github.com/handong66/Dong-skills)の現在のREADME・共同作業スキルにも、固定しない役割、書き込み担当の引き継ぎ、所見の裏取り、未確認事項の記録がある。ただしWeb取得ツールのキャッシュと直接取得した現在の本文に版の差があったため、公開当初の内容や個々の規則の導入時期までは断定しない。

[itstarts/cc-plugin-codex](https://github.com/itstarts/cc-plugin-codex/blob/main/plugins/cc/README.en.md)は逆方向、すなわちCodexからClaudeを呼ぶ実装例で、読み取り専用レビューと書き込み境界の試験記録を説明している。調査時点でアーカイブ済み。今回のプラグインそのものの試験ではない。

## 4. GitHub以外の著者による実践記録

| 資料 | 確認した関連内容 | 今回との違い |
| --- | --- | --- |
| [yuya0620：MCP経由の承認挙動の実測](https://zenn.dev/tyuya/articles/claude-codex-mcp-approval-gap)（2026-11-11公開） | sandboxを固定して承認ポリシーだけを変える実験、観測と原因推定の区別、別AIへの裏取り依頼 | `codex mcp-server`経由、旧版・別構成の測定。`codex-plugin-cc` 1.0.6の動作と同一視しない |
| [harness：相互呼び出しの運用記事](https://zenn.dev/harness/articles/cross-agent-harness-automation)（2026-05-19） | 作業契約、ユーザー承認後の委任、commit/push禁止、追加権限が必要な場合の停止 | 運用案・実装の紹介。人の承認を扱う外部記事も存在することを確認 |
| [Shimo：codex-review](https://zenn.dev/shimo4228/articles/codex-review-cross-model-decorrelation) | 読み取り専用の別モデルレビュー、指摘を外部入力として検証、重大指摘で人に判断を求める運用 | 変更ごとの人の承認ではないが、「自動判定だけ」と要約すると人の関与を落とす |
| [Iliya Oblakov：相互レビュー手順](https://dev.to/iliyaoblakov/how-i-make-claude-code-and-codex-review-each-others-work-5dmh) | 共通規則、読み取り専用レビュー、同一commitのテストとゲート、人もレビューを読むという方針 | 各変更前に人が承認する規則を本文からは確認できない。インストーラーの確認と通常作業の承認は別 |
| [DevelopersIO：プラグイン試用記事](https://dev.classmethod.jp/articles/codex-plugin-cc-trial-report/)（2026-05-07） | コマンド実行とレビュー出力の記録 | プラグインの権限境界・リンク先収集の追試としては扱わない |
| [Daniel Vaughan：導入紹介](https://codex.danielvaughan.com/2026/03/31/codex-plugin-cc-cross-model-bridge/) | プラグインの導入、相互レビューとreview gateの解説 | 今回の境界問題の独立実験を示す記事ではない |

新しく確認したyuya0620の記事は、権限の実測と相互検証という点で特に近い。ただし記事の結果をこちらで再実験したわけではなく、当時の条件付き報告として位置付ける。

## 5. 学術的な背景

- [Duほか：Improving Factuality and Reasoning in Language Models through Multiagent Debate（2023）](https://arxiv.org/html/2305.14325v1)：要旨だけでなく、手法、異なるモデルを使う小規模実験、限界の節を確認した。同じモデルの複数個体に加えChatGPTとBardの組み合わせも扱う。合意しても誤答になる限界を記している。Claude Code/Codexのファイル権限や承認手順を検証した論文ではない。
- [Irving・Christiano・Amodei：AI safety via debate（2018）](https://arxiv.org/abs/1805.00899)：今回は要旨と書誌情報を確認した。人がAIの討論を判定する研究上の先行概念。今回の開発ツール連携を直接評価したものではない。

「複数AIで検証する」「最終的に人が判断する」という一般的発想には研究上の先例がある。それらの論文だけで、今回の運用規則が誤りや危険を何％減らすかは判断できない。

## 6. 先行する調査要約への補正

**確認できた主要点**：#828と#834の内容、公式mainがdb52e28であること、派生版#8のマージ、同種の別プラグイン修正、相互レビューの既存事例。

**補正すべき点**：

1. #597は題名だけでなく本文を読むと、同じ問題・同じ版・再現・対策が既に記載されている。
2. #597の閉鎖主体は投稿者本人。保守者の拒否と推定しない。
3. #742・#494はPR。題名だけでなく本文で論点と未採用状態を区別する。
4. 「人の承認を軸とする似た試みがない」とは言えない。承認後の実装、役割交換、公開前承認を持つ先行例がある。
5. Zenn/DEVの記事を「自動判定が中心」とだけ表現すると、人による重大事項の判断やレビュー確認を省く。変更1回ごとの承認がないことと、人の関与がないことは別。
6. 「4点をまとめたものを見つけなかった」は限定した探索結果であって、独自性の証明ではない。

**確認できなかった点**：instagitの記事は取得できず、「自動生成」とする判断も今回は検証できていない。`vibecodedapps-official #14`は元報告に完全なリポジトリURLがなく、今回の検索でも対象を特定できなかった。どちらも結論の根拠には用いない。

## 7. 今回のリポジトリをどう評価できるか

- **事実として残せるもの**：実施した91件のテスト、実Codexの接続・レビュー試験、独立したリンク問題の再現、実施していない範囲の記録。
- **新規性を主張しない方がよいもの**：リンク問題自体、`lstat`等の対策、人の承認を含むClaude–Codex連携という一般的発想。
- **現在の位置付け**：既報の追試と、利用者向けの確認記録・運用案。新しい一般的方法の有効性を比較実験で示した研究とは区別する。
- **現時点で不足する証拠**：既存方式との比較、複数課題・複数回の評価、検出率・誤検出・費用・時間の測定、実際のClaudeデスクトップCode経由の一連の実験。

既報であることは公開をやめる理由にはならない。本プロジェクトは先行資料を引用し、独立した追試・確認記録・運用案として公開する。

## 記録の範囲

調査対象となった本リポジトリのcommitは `287a439aa3f502677f925b9e1d2619876137a63b`。Issue・PRの状態は2026-11-15の確認時点のもので、その後変化し得る。本書の公開は、対象プラグインの修正や追加の実動作試験を意味しない。
