# Text Extraction：システム内部の連関図

[Obsidian用の連関図](system-map.canvas) ／ [再生成用データ](system-map.data.json)

試験問題の画像をClaude CLIで構造化し、Google Sheetsへ記録して処理済みフォルダへ移す。

## 最初に見るポイント

ローカルPythonが調整役。画像認識はClaude CLI、保管と出力はGoogle APIが担当する。

## 内部の役割

| 部分 | 何をするか |
|---|---|
| Flask画面とbatch.py | 設定画面から手動実行、またはcronで同じ処理を呼ぶ。 |
| Google Drive API | サービスアカウントで画像の一覧・取得・移動を行う。 |
| 共通の処理パイプライン | 画像ごとの抽出・記録・移動を順番に進める。 |
| Claude Code CLI | ReadツールとJSON Schemaで問題番号・本文・選択肢・正解を抽出する。 |
| Sheetsへの追記と画像移動 | 抽出結果を1行追記し、成功した画像を処理済みへ移す。 |

## データのつながり

### 画像から問題データへ

- Flask画面とbatch.py → 共通の処理パイプライン：共通処理を起動
- 共通の処理パイプライン → Google Drive API：画像を一時取得
- Google Drive API → Claude Code CLI：画像パスを渡して構造化抽出

### 抽出成功後の保存

- Claude Code CLI → Sheetsへの追記と画像移動：問題項目を行として追記
- Sheetsへの追記と画像移動 → Google Drive API：記録後に処理済みへ移動

## 内部仕様と確認事項

### Flask画面とbatch.py

Flaskは127.0.0.1:5000で設定・フォルダ選択・/runを提供する。batch.pyも共通のrun_processingを使う。

- Webサーバーを起動せずbatch.pyをcronから呼べる。cronの登録自体は別途必要。

根拠：[app.py](https://github.com/gurii-gabreh/Text-Extraction/blob/4f3343099b369800554b14475f9e1c42035d58f8/app.py)、[batch.py](https://github.com/gurii-gabreh/Text-Extraction/blob/4f3343099b369800554b14475f9e1c42035d58f8/batch.py)、[README.md](https://github.com/gurii-gabreh/Text-Extraction/blob/4f3343099b369800554b14475f9e1c42035d58f8/README.md)

### Google Drive API

認証JSONからDrive・Sheets用の資格情報を作る。画像を一時ディレクトリへダウンロードする。

- 対象フォルダとシートへの共有設定が必要。list_imagesのページ送り処理は見当たらないため、大量画像の全件処理は別途確認が必要。

根拠：[core/drive_client.py](https://github.com/gurii-gabreh/Text-Extraction/blob/4f3343099b369800554b14475f9e1c42035d58f8/core/drive_client.py)、[core/processor.py](https://github.com/gurii-gabreh/Text-Extraction/blob/4f3343099b369800554b14475f9e1c42035d58f8/core/processor.py)

### 共通の処理パイプライン

run_processingが設定検証、ヘッダー確認、画像処理を制御する。画像ごとに例外を扱い、失敗をログへ残して次へ進む。


根拠：[core/processor.py](https://github.com/gurii-gabreh/Text-Extraction/blob/4f3343099b369800554b14475f9e1c42035d58f8/core/processor.py)

### Claude Code CLI

subprocessでclaude -pを起動し、モデル・タイムアウト・構造化出力を指定する。欠損やUNREADABLEは失敗とする。

- Claude CLIのインストール・認証が必要。コードはRead限定のツール指定に加え権限確認を省略する起動オプションも使用する。今回は実行していない。

根拠：[core/claude_extractor.py](https://github.com/gurii-gabreh/Text-Extraction/blob/4f3343099b369800554b14475f9e1c42035d58f8/core/claude_extractor.py)

### Sheetsへの追記と画像移動

Sheetsへ問題項目と元ファイル名を追記後、Driveの親フォルダを変更する。設定はローカルconfig.jsonへ保存する。

- 追記と移動は一体のトランザクションではない。移動だけ失敗して再実行すると再追記されうる。

根拠：[core/sheets_client.py](https://github.com/gurii-gabreh/Text-Extraction/blob/4f3343099b369800554b14475f9e1c42035d58f8/core/sheets_client.py)、[core/drive_client.py](https://github.com/gurii-gabreh/Text-Extraction/blob/4f3343099b369800554b14475f9e1c42035d58f8/core/drive_client.py)、[core/config.py](https://github.com/gurii-gabreh/Text-Extraction/blob/4f3343099b369800554b14475f9e1c42035d58f8/core/config.py)、[core/processor.py](https://github.com/gurii-gabreh/Text-Extraction/blob/4f3343099b369800554b14475f9e1c42035d58f8/core/processor.py)

## 確認範囲

- 確認日：2026-10-17 JST
- 対象ブランチ：`claude/app-creation-resources-eenb15`
- 対象コミット：[4f3343099b369800554b14475f9e1c42035d58f8](https://github.com/gurii-gabreh/Text-Extraction/tree/4f3343099b369800554b14475f9e1c42035d58f8)
- コード・文書から作成。外部サービスへの書込み、アプリの起動、稼働環境の認証・データ確認は行っていません。
- Canvasは単体で読めるテキストカードです。Vaultへ配置して開いてください。接続先・ID・カード重複は生成スクリプトで検証済み。Obsidian実アプリでの表示確認は未実施です。
- 再生成は個人スキル `obsidian-app-mindmap` の `scripts/build_canvas.py` にJSONと出力先を渡します。
- JPGの作業は含みません。
