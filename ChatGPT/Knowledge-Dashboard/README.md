# Knowledge Dashboard：システム内部の連関図

[Obsidian用の連関図](system-map.canvas) ／ [再生成用データ](system-map.data.json)

Google Driveのナレッジ用スプレッドシートを、フォルダ・カテゴリ別に探す一覧画面。

## 最初に見るポイント

Driveと常時同期するアプリではなく、一覧のスナップショットと端末内表示設定を組み合わせる。

## 内部の役割

| 部分 | 何をするか |
|---|---|
| Drive一覧のスナップショット | Driveから取得・整理した一覧をファイルとして保存する。 |
| HTML内のDATA | 画面は埋込みデータを読む。 |
| 検索・フォルダ・カテゴリ表示 | 検索、絞り込み、並び替え、折りたたみで探す。 |
| 表示管理をlocalStorageへ保存 | 非表示などの個人用表示状態を端末へ残す。 |

## データのつながり

### 一覧を作り表示する

- Drive一覧のスナップショット → HTML内のDATA：取得結果をJSONとHTMLへ反映
- HTML内のDATA → 検索・フォルダ・カテゴリ表示：埋込みDATAから描画

### 個人の見え方を変える

- 検索・フォルダ・カテゴリ表示 → 表示管理をlocalStorageへ保存：表示状態を端末に保存

## 内部仕様と確認事項

### Drive一覧のスナップショット

READMEではClaudeにDrive検索を依頼し、data/spreadsheets.jsonを更新する運用を示す。

- 実行中にDriveを自動スキャンするバックエンドはない。

根拠：[README.md](https://github.com/gurii-gabreh/Knowledge-Dashboard/blob/51be29a23c418b5fc1fe99c70a48ac351de4c7d6/README.md)

### HTML内のDATA

dashboard.htmlのDATAへシート一覧を内包する。JSONは再生成用の元データで、同じ内容をHTMLにも反映する必要がある。

- JSONだけ更新してもHTMLの埋込み一覧は自動更新されない。

根拠：[dashboard.html](https://github.com/gurii-gabreh/Knowledge-Dashboard/blob/51be29a23c418b5fc1fe99c70a48ac351de4c7d6/dashboard.html)、[README.md](https://github.com/gurii-gabreh/Knowledge-Dashboard/blob/51be29a23c418b5fc1fe99c70a48ac351de4c7d6/README.md)

### 検索・フォルダ・カテゴリ表示

visibleItemsやmatches等が一覧を処理し、シートのリンクを開く。

- シート本文の編集・同期をこの画面が行うわけではない。

根拠：[dashboard.html](https://github.com/gurii-gabreh/Knowledge-Dashboard/blob/51be29a23c418b5fc1fe99c70a48ac351de4c7d6/dashboard.html)

### 表示管理をlocalStorageへ保存

loadState／saveStateと管理モーダルが表示設定を変更する。

- この操作はDrive上のシート削除や元一覧の更新ではない。

根拠：[dashboard.html](https://github.com/gurii-gabreh/Knowledge-Dashboard/blob/51be29a23c418b5fc1fe99c70a48ac351de4c7d6/dashboard.html)

## 確認範囲

- 確認日：2026-10-17 JST
- 対象ブランチ：`claude/spreadsheet-list-dashboard-te7yvs`
- 対象コミット：[51be29a23c418b5fc1fe99c70a48ac351de4c7d6](https://github.com/gurii-gabreh/Knowledge-Dashboard/tree/51be29a23c418b5fc1fe99c70a48ac351de4c7d6)
- コード・文書から作成。外部サービスへの書込み、アプリの起動、稼働環境の認証・データ確認は行っていません。
- Canvasは単体で読めるテキストカードです。Vaultへ配置して開いてください。接続先・ID・カード重複は生成スクリプトで検証済み。Obsidian実アプリでの表示確認は未実施です。
- 再生成は個人スキル `obsidian-app-mindmap` の `scripts/build_canvas.py` にJSONと出力先を渡します。
- JPGの作業は含みません。
