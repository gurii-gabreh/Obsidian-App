# Credit Radar：システム内部の連関図

[Obsidian用の連関図](system-map.canvas) ／ [再生成用データ](system-map.data.json)

Claude Codeの作業量と、利用上限に達した時刻を同じ日付軸で比較する。

## 最初に見るポイント

実測作業ログと自己申告の上限時刻を突き合わせるための道具。公式利用量APIの監視ではない。

## 内部の役割

| 部分 | 何をするか |
|---|---|
| 使用量ダッシュボード | 件数・所要時間・利用上限ログを並べる。 |
| 専用GAS | 一覧取得と2種類のログ書込みを受ける。 |
| 2つのログタブ | 共通の進捗管理Sheetに専用タブだけを追加する。 |
| 利用上限の手動記録 | 上限に達したと感じた時刻と状況を入力する。 |
| タスク作業量の記録口 | 外部Routine等からのログ登録用APIを用意する。 |

## データのつながり

### 上限を記録する

- 利用上限の手動記録 → 専用GAS：共有トークン付きで送信
- 専用GAS → 2つのログタブ：専用ログタブへ保存
- 2つのログタブ → 使用量ダッシュボード：GET結果をグラフ・表へ描画

### 外部作業ログの受け口

- タスク作業量の記録口 → 専用GAS：addUsageLogの入力を処理

## 内部仕様と確認事項

### 使用量ダッシュボード

index.htmlがSVGグラフ、表、上限ログ入力を提供する。接続URLと共有トークンは利用者のlocalStorageへ保存する。

- Claudeの公式クレジット残量を自動取得するアプリではない。

根拠：[index.html](https://github.com/gurii-gabreh/credit-radar/blob/ba09e5261777e06f0140c995a172d2312fc6da14/index.html)、[README.md](https://github.com/gurii-gabreh/credit-radar/blob/ba09e5261777e06f0140c995a172d2312fc6da14/README.md)

### 専用GAS

doGet listはJSONを返し、doPostはTOKENを照合してaddLimitLogまたはaddUsageLogへ振り分ける。

- progress用GASとは別プロジェクトを使う設計。個人別のログイン認証ではない。

根拠：[gas/Code.gs](https://github.com/gurii-gabreh/credit-radar/blob/ba09e5261777e06f0140c995a172d2312fc6da14/gas/Code.gs)、[README.md](https://github.com/gurii-gabreh/credit-radar/blob/ba09e5261777e06f0140c995a172d2312fc6da14/README.md)

### 2つのログタブ

使用量ログと利用上限ログを扱う。使用量ログはrepo・task・着手日時で既存行を探し、あれば更新する。


根拠：[gas/Code.gs](https://github.com/gurii-gabreh/credit-radar/blob/ba09e5261777e06f0140c995a172d2312fc6da14/gas/Code.gs)

### 利用上限の手動記録

フォームがGASへPOSTし、一覧を再読込してグラフへ反映する。

- 自己申告のログ。APIが実際の上限到達を検知しているわけではない。

根拠：[index.html](https://github.com/gurii-gabreh/credit-radar/blob/ba09e5261777e06f0140c995a172d2312fc6da14/index.html)

### タスク作業量の記録口

GASのaddUsageLogに作業開始・完了等を渡す想定。

- 各Routineから自動で呼ぶ接続はREADME上で未実施。受け口があるだけで全作業を自動収集できるとは言えない。

根拠：[gas/Code.gs](https://github.com/gurii-gabreh/credit-radar/blob/ba09e5261777e06f0140c995a172d2312fc6da14/gas/Code.gs)、[README.md](https://github.com/gurii-gabreh/credit-radar/blob/ba09e5261777e06f0140c995a172d2312fc6da14/README.md)

## 確認範囲

- 確認日：2026-10-17 JST
- 対象ブランチ：`claude/usage-analytics-dashboard-kh5fj0`
- 対象コミット：[ba09e5261777e06f0140c995a172d2312fc6da14](https://github.com/gurii-gabreh/credit-radar/tree/ba09e5261777e06f0140c995a172d2312fc6da14)
- コード・文書から作成。外部サービスへの書込み、アプリの起動、稼働環境の認証・データ確認は行っていません。
- Canvasは単体で読めるテキストカードです。Vaultへ配置して開いてください。接続先・ID・カード重複は生成スクリプトで検証済み。Obsidian実アプリでの表示確認は未実施です。
- 再生成は個人スキル `obsidian-app-mindmap` の `scripts/build_canvas.py` にJSONと出力先を渡します。
- JPGの作業は含みません。
