# 2026-11-20 Claude Code–Codex communication and verification tests

## English summary

On one Mac, a local Claude Code session and an existing Codex Work chat exchanged messages through two append-only text files, one per sender. An interrupted Codex response stream did not stop the file relay. After the monitor stopped, the desktop client still displayed an active state until the application was restarted. The initial connection failure remains unexplained.

Two single-round restart tests returned acknowledgments in 15 and 16 seconds, measured between outgoing and acknowledgment file entries. Codex progress was invisible in the first trial and visible in real time in the second, as observed by the user. An earlier preflight display check also failed. These are different measurements from end-to-end display latency.

A separate synthetic numerical test passed all 468 frozen evaluation cases in Colab. A known boundary-case specification violation and a weakness in the evaluator explain why that pass did not establish full conformance. The revised candidate passed regression checks against the same cases. Blind evaluation of the candidate remains pending.

## 試験環境と判定方法

同じMacで、ClaudeアプリのローカルCodeセッションと既存のCodex Workチャットを使用した。この日のエージェント間の通信経路は、Mac上の送信側別の共有テキストファイル2本。各便は「JST時刻・試験ID・本文」の順で1行だけ追記し、返信には対応するIDを付けた。ACKの時刻差、エージェントによる読み取り、ユーザーの画面表示は個別に判定した。

11月20日の通信試験は、プラグイン呼び出しや画面操作による転送を使った以前の試験とは別の試験である。Mac上に共有ファイルを置くことと、各エージェントのモデル処理に伴うサービスへの送信は別であり、ネットワーク上を流れたデータ全体は測定していない。

## 合成データによる相互検証

| 工程 | 観測と判定 |
| --- | --- |
| 分担・版の固定 | Codexは開発用データ、境界テスト、実装初稿を担当。Claude Codeは初稿の本文を見る前に、正解付き評価データと評価条件を固定。初稿は14:39:54、評価資料は14:43:23にハッシュを共有した。 |
| 開発・評価 | Codexの報告は開発用39件と追加1万往復の通過。ユーザーがColabのCPUランタイムで固定初稿を実行し、保存された結果原本で468/468合格、失敗0件を確認。評価処理0.025秒はファイル転送や全体の所要時間を含まない。 |
| 反例と評価器 | 等長・許容誤差0・原点のごく近くで数値が丸められ、2解の符号順が仕様を満たさない反例があった。固定468件にはこの入力がなく、角度の周期差を比較する評価器も符号順を見落とし得た。参照実装の境界条件にも不備が見つかった。固定条件での合格と、既知の仕様違反1件を併記した。 |
| 修正候補 | 別ファイルの候補は開発用40/40、開示済みの468件を旧評価器で468/468と報告された。更新した評価器・参照実装での追試、未開示ケースでの評価、候補の採用はこの日の終了時点で未完了。 |
| 独立判定 | Codexが原本より先にClaude Codeの判定を読み、Codexの作業は独立判定ではなく原本照合となった。次回は双方に同じ原本を配布し、各自が判定本文を開示する前にそのSHA-256を交換する。 |

## Codexの表示停止と復旧

| 時刻（JST） | 観測 |
| --- | --- |
| 14:34:46〜15:08:00 | 1つのターン内で共有ファイル監視を開始。14:37:36に会話更新の取りこぼしを記録し、15:06:55に回答ストリームの再開要求がHTTP 404（`Resume stream unavailable`）で失敗。15:08:00に会話を再取得した。 |
| 15:10ごろ〜15:29 | ユーザーがCodexの表示異常を認識。画面に応答が出ない間も、共有ファイルには呼び出し・診断依頼への返信が残った。 |
| 15:30:07〜15:30:32 | 監視プロセスを終了し、停止を共有ファイルに記録。その後の再取得でサーバーの状態値が変わったが、クライアントは最新位置を適用せず、画面側の実行中状態が残った。 |
| 15:42台〜15:48:15 | アプリ再起動後に回転表示が消えた。同じ会話が待機状態になったことをログで確認。欠けた応答の本文があとから表示されたかは未確認。 |

アプリの表示先と実行側セッションIDには別々の識別子が使われていたため、障害の対象は画面の会話ルートとエラーログを照合して確定した。ログが直接示すのは回答ストリームの**再開要求の失敗**まで。最初に接続が切れた原因と、再取得時にクライアントが最新位置を適用しなかった条件は特定できていない。

## 監視停止後の再開試験

| 時刻（JST） | 共有ファイル | 画面での確認 |
| --- | --- | --- |
| 16:06:29 → 16:06:44 | 送信からACKの追記まで15秒。 | Claude Code側の途中経過は表示され、後からも読めた。Codex側は途中経過が表示されず、終了後も読めなかった。 |
| 16:23〜16:25 | 表示確認中は送信を保留。 | Codexの開始確認行が最終返信前に見えなかった。 |
| 16:26:12 → 16:26:28 | ユーザーの指示で1件送信。ACKまで16秒。 | ユーザーがCodex側のリアルタイムの途中経過を確認。この1往復に限る合格。 |

追試では、開始前の表示を実際の画面で確認し、試験IDとACKで通信の成否を判定する。ログに送出が記録されていても、途中経過の表示は画面上で確かめる。最初は1往復でターンを閉じ、最終返信にも時系列を残す。画面表示の再現性、両画面間の表示時刻差、別の端末・版での再現性は未測定。
