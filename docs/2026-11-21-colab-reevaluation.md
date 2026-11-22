# Colab reevaluation, November 21

## English summary

In a follow-up to the November 20 experiment, one notebook stopped during input preparation. A revised cell completed scoring. These trials illustrate why an experiment log should distinguish a run that never reached the evaluator from a run that returned a score.

For replication, record which files the active runtime can read and verify the expected digests before starting the evaluator. Pass those verified paths to the scoring cell. Report the number of cases actually scored alongside the result.

## 日本語の記録

11月20日の実験に続き、Colabで再評価を試みた。最初の試行は入力準備で停止した。書き直したセルでは採点が完了した。最初の試行には合否を付けず、「採点未実行」と記録する。

追試では、実行中のランタイムから読み取れたファイルの場所とハッシュを記録し、照合したパスを採点セルへ渡す。結果を報告するときは、実際に採点した件数を添える。

[Previous experiment / 前回の実験](2026-11-20-communication-test.md)
