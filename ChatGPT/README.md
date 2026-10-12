# ChatGPTで作成したObsidian Canvas

| ファイル | 内容 |
|---|---|
| [app-architecture-flow.canvas](app-architecture-flow.canvas) | 初心者向けのアプリ構成チェックリスト。8カテゴリ・25項目、短い説明と選び方。 |
| [progress-tracker-task-resolutions.canvas](progress-tracker-task-resolutions.canvas) | 前回の課題・対応経緯・最終方針。18タスク、5カテゴリ、関連タスクの別領域。 |
| [progress-tracker-final-spec.canvas](progress-tracker-final-spec.canvas) | 同じ5カテゴリの最終仕様19項目とアプリの特徴6項目。過去の対応経緯は省略。 |
| progress-tracker-task-resolutions.data.json | 課題マップの再生成用データ。 |
| progress-tracker-final-spec.data.json | 最終仕様マップの再生成用データ。 |

## 開き方

このフォルダをObsidianのVault内へ配置し、`.canvas` を開いてください。拡大・縮小、移動、カードの編集ができます。テキストをファイル内に保持するため、別ノートや画像の依存はありません。GitHubのファイル画面は操作可能なCanvasビューアではありません。

## Progress Trackerマップの対象

- 対象アプリ：gurii-gabreh/progress-tracker-dashboard
- 作成日：2026-10-12 JST
- 最終仕様の確認コミット：5aa2c2415b21e6f12901c8bba9828649e8daa218
- 課題マップは前回の18タスクの記録をそのまま保存したものです。
- 最終仕様はコード・運用文書を照合したスナップショットです。稼働中のGASやSheetを実機確認したものではありません。
- 実装ありと採用方針を区別し、制約とコミット固定の根拠リンクを各カードに記載しています。

Progress Trackerの2つのマップの再生成は個人スキル `obsidian-app-mindmap` の `scripts/build_canvas.py` にJSONと出力Canvasのパスを渡して行えます。Canvasを手で編集した場合、再生成時は元JSONの内容で出力されます。

## アプリ作成の全体像

`app-architecture-flow.canvas` は添付図を更新した学習用マップです。目的・画面・処理・データ・安全・開発・公開・スマホの8カテゴリに整理しています。特定アプリの採用済み仕様ではなく、必要に応じて構成を選ぶためのチェックリストです。技術名は選択肢の例で、料金や制限は採用時に確認してください。元図の項目に加え、仕様、開発環境、テスト、復元、秘密情報、公開・運用を補っています。
