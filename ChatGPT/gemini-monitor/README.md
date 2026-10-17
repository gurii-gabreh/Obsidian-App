# Gemini Monitor：システム内部の連関図

[Obsidian用の連関図](system-map.canvas) ／ [再生成用データ](system-map.data.json)

モデル別・時間帯別のAPI応答状況を測り、成功率や待ち時間をグラフにする。

## 最初に見るポイント

ブラウザで測るのではなく、GASが測り、Sheetsに残し、画面が可視化する。

## 内部の役割

| 部分 | 何をするか |
|---|---|
| Chart.jsのダッシュボード | 成功率・レイテンシ・規模別結果を比較する。 |
| GAS Web API | 取得・設定保存・即時測定を振り分ける。 |
| 測定とトリガー | 対象モデルとプロンプト規模を組み合わせて測定する。 |
| Gemini API | 測定用プロンプトへの応答を返す。 |
| 測定・設定シート | 履歴を蓄積し、画面へ返す。 |

## データのつながり

### 測定データがたまる流れ

- 測定とトリガー → Gemini API：プロンプトを送る
- Gemini API → 測定・設定シート：応答と測定値をGASが保存

### 閲覧・設定の流れ

- Chart.jsのダッシュボード → GAS Web API：GASへactionを送る
- GAS Web API → 測定・設定シート：シートの結果・設定を読み書き

## 内部仕様と確認事項

### Chart.jsのダッシュボード

index.htmlがGAS URLをlocalStorageへ保存し、getData／getSettings等を呼ぶ。取得結果をヒートマップや時系列へ集計する。


根拠：[index.html](https://github.com/gurii-gabreh/gemini-monitor/blob/4eca9d60966ebdc3f7c536ec368b3516368a6fac/index.html)

### GAS Web API

doGetのactionでgetData、getSettings、saveSettings、runNowを処理しJSONを返す。

- 個別利用者の認証と稼働中GASへの反映状況は未検証。

根拠：[gas/Code.gs](https://github.com/gurii-gabreh/gemini-monitor/blob/4eca9d60966ebdc3f7c536ec368b3516368a6fac/gas/Code.gs)

### 測定とトリガー

runMeasurementは測定時間帯を確認し、Gemini APIへ順次送信して処理時間・HTTPエラー・トークン使用量を記録する。pro系は小サイズだけを対象にする。

- 結果は設定モデル・プロンプト・利用枠にも左右されるため、サービス全体の混雑そのものを断定する指標ではない。

根拠：[gas/Code.gs](https://github.com/gurii-gabreh/gemini-monitor/blob/4eca9d60966ebdc3f7c536ec368b3516368a6fac/gas/Code.gs)

### Gemini API

generateContentのHTTPコードからsuccess、rate_limit、error等へ分類する。APIキーはGAS側の設定から読む。

- コード中のモデル候補・無料枠の記述は現行の提供条件を保証しない。

根拠：[gas/Code.gs](https://github.com/gurii-gabreh/gemini-monitor/blob/4eca9d60966ebdc3f7c536ec368b3516368a6fac/gas/Code.gs)

### 測定・設定シート

SpreadsheetAppで測定行と設定値を保存する。設定変更時は時間トリガーを再設定する。

- トリガーの実時刻や現在の設定値は未確認。

根拠：[gas/Code.gs](https://github.com/gurii-gabreh/gemini-monitor/blob/4eca9d60966ebdc3f7c536ec368b3516368a6fac/gas/Code.gs)

## 確認範囲

- 確認日：2026-10-17 JST
- 対象ブランチ：`main`
- 対象コミット：[4eca9d60966ebdc3f7c536ec368b3516368a6fac](https://github.com/gurii-gabreh/gemini-monitor/tree/4eca9d60966ebdc3f7c536ec368b3516368a6fac)
- コード・文書から作成。外部サービスへの書込み、アプリの起動、稼働環境の認証・データ確認は行っていません。
- Canvasは単体で読めるテキストカードです。Vaultへ配置して開いてください。接続先・ID・カード重複は生成スクリプトで検証済み。Obsidian実アプリでの表示確認は未実施です。
- 再生成は個人スキル `obsidian-app-mindmap` の `scripts/build_canvas.py` にJSONと出力先を渡します。
- JPGの作業は含みません。
