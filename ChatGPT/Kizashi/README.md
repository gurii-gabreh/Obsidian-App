# 結（Yui／Kizashi）：システム内部の連関図

[Obsidian用の連関図](system-map.canvas) ／ [再生成用データ](system-map.data.json)

災害現場の家族・関係者が、ルーム単位で問診・間取り・前兆報告を共有するWebアプリ。

## 最初に見るポイント

単一Workerが画面・APIを担う。災害の予測や救助判断ではなく、共有と傾聴を中心に分担する。

## 内部の役割

| 部分 | 何をするか |
|---|---|
| Web画面・音声入力 | 地図、事前問診・間取り、前兆報告、傾聴チャットを表示する。 |
| Cloudflare Worker | 静的ファイルと/api/rooms以下のAPIを同じWorkerから提供する。 |
| ルームとD1 | 6桁コードを共有範囲にし、事案と関連データを保存する。 |
| 問診の暗号化 | 問診データをAES-GCMで暗号化して保存する。 |
| Workers AIの傾聴チャット | 危機のキーワード確認を先に行い、通常はAIへ送る。 |
| 期限切れの削除 | 終了から30日後を期限とし、日次処理で関連データを消す。 |
| 地図変換・検証用ツール | 本体と別に、プロトタイプとデータ変換・XML検証を置く。 |

## データのつながり

### 共有情報を保存する

- Web画面・音声入力 → Cloudflare Worker：ルームコード付きAPIへ送信
- Cloudflare Worker → 問診の暗号化：問診データを暗号化
- 問診の暗号化 → ルームとD1：D1へルーム単位で保存

### 傾聴チャット

- Web画面・音声入力 → Cloudflare Worker：履歴と発話をPOST
- Cloudflare Worker → Workers AIの傾聴チャット：入力確認後にチャット処理
- Workers AIの傾聴チャット → Web画面・音声入力：通常AIまたは固定・スタブ応答を表示

### 終了後の保守

- ルームとD1 → 期限切れの削除：expires_atを過ぎた事案と関連データを削除

## 内部仕様と確認事項

### Web画面・音声入力

バニラJSとLeafletで構成する。Web Speechによる一問一答で入力し、文字起こしを利用者が確認する。別クライアントの情報はポーリング等で取り込む。

- 端末の音声・位置情報権限に依存する。救助の予測や判断をするアプリではない。

根拠：[kizashi-app/src/public/app.js](https://github.com/gurii-gabreh/Kizashi/blob/993811179cc4ab2c63843e8e0d325ca34c9f9145/kizashi-app/src/public/app.js)、[kizashi-app/src/public/voiceIntake.js](https://github.com/gurii-gabreh/Kizashi/blob/993811179cc4ab2c63843e8e0d325ca34c9f9145/kizashi-app/src/public/voiceIntake.js)、[kizashi-app/src/README.md](https://github.com/gurii-gabreh/Kizashi/blob/993811179cc4ab2c63843e8e0d325ca34c9f9145/kizashi-app/src/README.md)

### Cloudflare Worker

fetchがルーム作成・確認・終了、問診、間取り、前兆、チャットの各ルートへ振り分ける。API以外はenv.ASSETSで配信する。


根拠：[kizashi-app/src/worker/index.js](https://github.com/gurii-gabreh/Kizashi/blob/993811179cc4ab2c63843e8e0d325ca34c9f9145/kizashi-app/src/worker/index.js)

### ルームとD1

incidents、family_intake、floor_plans、precursor_signsをD1へ保存する。各ルートでresolveRoomを使い、ルームコードと期限を確認する。

- 個人ごとのログイン・管理者ロールではない。コードを知る人を単位に共有する方式。

根拠：[kizashi-app/src/worker/lib/roomAuth.js](https://github.com/gurii-gabreh/Kizashi/blob/993811179cc4ab2c63843e8e0d325ca34c9f9145/kizashi-app/src/worker/lib/roomAuth.js)、[kizashi-app/src/migrations/0001_init.sql](https://github.com/gurii-gabreh/Kizashi/blob/993811179cc4ab2c63843e8e0d325ca34c9f9145/kizashi-app/src/migrations/0001_init.sql)、[kizashi-app/src/worker/routes/incidents.js](https://github.com/gurii-gabreh/Kizashi/blob/993811179cc4ab2c63843e8e0d325ca34c9f9145/kizashi-app/src/worker/routes/incidents.js)

### 問診の暗号化

crypto.jsがサーバー側の秘密鍵を使いランダムIVで暗号化する。intake APIが許可した項目を1ルーム1件として保存・復号する。

- 暗号化対象は問診。すべてのテーブルや端末表示が暗号文になるわけではない。鍵の実設定は未確認。

根拠：[kizashi-app/src/worker/lib/crypto.js](https://github.com/gurii-gabreh/Kizashi/blob/993811179cc4ab2c63843e8e0d325ca34c9f9145/kizashi-app/src/worker/lib/crypto.js)、[kizashi-app/src/worker/routes/intake.js](https://github.com/gurii-gabreh/Kizashi/blob/993811179cc4ab2c63843e8e0d325ca34c9f9145/kizashi-app/src/worker/routes/intake.js)

### Workers AIの傾聴チャット

chat APIはルーム確認、入力長制限、危機キーワード検知を行う。AIバインディングがない場合はスタブを返す。会話はクライアントから渡す直近履歴を使用しDBへ永続化しない。

- 助言・救助予測をしない指示は実装されるが、応答の完全な保証ではない。ここでは相談先の番号等は転載しない。

根拠：[kizashi-app/src/worker/routes/chat.js](https://github.com/gurii-gabreh/Kizashi/blob/993811179cc4ab2c63843e8e0d325ca34c9f9145/kizashi-app/src/worker/routes/chat.js)、[kizashi-app/src/public/chat.js](https://github.com/gurii-gabreh/Kizashi/blob/993811179cc4ab2c63843e8e0d325ca34c9f9145/kizashi-app/src/public/chat.js)

### 期限切れの削除

ルーム終了操作でexpires_atを設定し、scheduledから期限切れの問診・間取り・前兆・事案をdb.batchで削除する。

- 終了していない事案に30日の期限が自動設定されるわけではない。

根拠：[kizashi-app/src/worker/routes/incidents.js](https://github.com/gurii-gabreh/Kizashi/blob/993811179cc4ab2c63843e8e0d325ca34c9f9145/kizashi-app/src/worker/routes/incidents.js)、[kizashi-app/src/worker/scheduled/purgeExpiredIncidents.js](https://github.com/gurii-gabreh/Kizashi/blob/993811179cc4ab2c63843e8e0d325ca34c9f9145/kizashi-app/src/worker/scheduled/purgeExpiredIncidents.js)、[kizashi-app/src/wrangler.toml](https://github.com/gurii-gabreh/Kizashi/blob/993811179cc4ab2c63843e8e0d325ca34c9f9145/kizashi-app/src/wrangler.toml)

### 地図変換・検証用ツール

prototypeはUI検証用。convert_ksj_a33.pyは区域データを変換し、d1-pocはDBスキーマ検証、jma-xml-pocはサンプル警報XMLの解析を行う。

- 気象庁XMLの継続取得やWeb Push／LINE通知が本体へ接続済みとは確認できない。wranglerのD1 IDはプレースホルダで本番稼働は未確認。

根拠：[kizashi-app/prototype/doshasai-prototype.html](https://github.com/gurii-gabreh/Kizashi/blob/993811179cc4ab2c63843e8e0d325ca34c9f9145/kizashi-app/prototype/doshasai-prototype.html)、[kizashi-app/tools/convert_ksj_a33.py](https://github.com/gurii-gabreh/Kizashi/blob/993811179cc4ab2c63843e8e0d325ca34c9f9145/kizashi-app/tools/convert_ksj_a33.py)、[kizashi-app/tools/jma-xml-poc/parse.mjs](https://github.com/gurii-gabreh/Kizashi/blob/993811179cc4ab2c63843e8e0d325ca34c9f9145/kizashi-app/tools/jma-xml-poc/parse.mjs)、[kizashi-app/src/wrangler.toml](https://github.com/gurii-gabreh/Kizashi/blob/993811179cc4ab2c63843e8e0d325ca34c9f9145/kizashi-app/src/wrangler.toml)

## 確認範囲

- 確認日：2026-10-17 JST
- 対象ブランチ：`main`
- 対象コミット：[993811179cc4ab2c63843e8e0d325ca34c9f9145](https://github.com/gurii-gabreh/Kizashi/tree/993811179cc4ab2c63843e8e0d325ca34c9f9145)
- コード・文書から作成。外部サービスへの書込み、アプリの起動、稼働環境の認証・データ確認は行っていません。
- Canvasは単体で読めるテキストカードです。Vaultへ配置して開いてください。接続先・ID・カード重複は生成スクリプトで検証済み。Obsidian実アプリでの表示確認は未実施です。
- 再生成は個人スキル `obsidian-app-mindmap` の `scripts/build_canvas.py` にJSONと出力先を渡します。
- JPGの作業は含みません。
