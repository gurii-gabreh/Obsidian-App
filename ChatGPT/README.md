# ChatGPTで作成したObsidian Canvas

| ファイル | 内容 |
|---|---|
| [progress-tracker-task-resolutions.canvas](progress-tracker-task-resolutions.canvas) | 前回の課題・対応経緯・最終方針。18タスク、5カテゴリ、関連タスクの別領域。 |
| [progress-tracker-final-spec.canvas](progress-tracker-final-spec.canvas) | 同じ5カテゴリの最終仕様19項目とアプリの特徴6項目。過去の対応経緯は省略。 |
| progress-tracker-task-resolutions.data.json | 課題マップの再生成用データ。 |
| progress-tracker-final-spec.data.json | 最終仕様マップの再生成用データ。 |

## 開き方

このフォルダをObsidianのVault内へ配置し、`.canvas` を開いてください。拡大・縮小、移動、カードの編集ができます。テキストをファイル内に保持するため、別ノートや画像の依存はありません。GitHubのファイル画面は操作可能なCanvasビューアではありません。

## 対象

- 対象アプリ：gurii-gabreh/progress-tracker-dashboard
- 作成日：2026-10-12 JST
- 最終仕様の確認コミット：5aa2c2415b21e6f12901c8bba9828649e8daa218
- 課題マップは前回の18タスクの記録をそのまま保存したものです。
- 最終仕様はコード・運用文書を照合したスナップショットです。稼働中のGASやSheetを実機確認したものではありません。
- 実装ありと採用方針を区別し、制約とコミット固定の根拠リンクを各カードに記載しています。

再生成は個人スキル `obsidian-app-mindmap` の `scripts/build_canvas.py` にJSONと出力Canvasのパスを渡して行えます。Canvasを手で編集した場合、再生成時は元JSONの内容で出力されます。
