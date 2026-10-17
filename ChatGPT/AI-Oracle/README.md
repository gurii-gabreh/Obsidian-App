# AI Oracle：システム内部の連関図

[Obsidian用の連関図](system-map.canvas) ／ [再生成用データ](system-map.data.json)

質問を設定済みの外部サーバーへ送り、複数回答やまとめを表示するチャット画面。

## 最初に見るポイント

実体はフロントエンド。回答サーバーをこのリポジトリだけで再現できるとは言えない。

## 内部の役割

| 部分 | 何をするか |
|---|---|
| チャットUI | 質問、回答、チャット一覧をブラウザで表示する。 |
| 質問の送信 | 設定したサーバーURLの/askへ質問だけを送る。 |
| 外部サーバーへの依存 | 回答を作る処理は、このリポジトリの外にある。 |
| localStorage | チャット履歴と接続設定を端末へ保存する。 |

## データのつながり

### 質問から回答表示まで

- チャットUI → 質問の送信：質問を入力
- 質問の送信 → 外部サーバーへの依存：設定URLへPOST /ask
- 外部サーバーへの依存 → チャットUI：返却JSONの回答を描画

### 端末内の履歴

- チャットUI → localStorage：チャットと接続設定を保存

## 内部仕様と確認事項

### チャットUI

index.htmlで新規会話・切替・削除・入力・回答折りたたみを実装する。


根拠：[index.html](https://github.com/gurii-gabreh/AI-Oracle/blob/461872e7d607c1f7ee9a79f45782191e42670890/index.html)

### 質問の送信

askがPOST JSONで{question}を送り、返却JSONを描画する。画面上にはGemini CLIの処理待ち表示がある。

- 送信本文は質問であり、過去の会話全体がサーバーへ送られる実装とは確認できない。

根拠：[index.html](https://github.com/gurii-gabreh/AI-Oracle/blob/461872e7d607c1f7ee9a79f45782191e42670890/index.html)

### 外部サーバーへの依存

画面はdata.answers等を受け取る前提で描画する。設定の既定値はLAN内のHTTPサーバー。

- /askのサーバーコードやCLI実行処理はリポジトリにない。複数AIの実際の呼出し方式は確認できない。

根拠：[index.html](https://github.com/gurii-gabreh/AI-Oracle/blob/461872e7d607c1f7ee9a79f45782191e42670890/index.html)

### localStorage

チャット配列と設定をlocalStorageへ読み書きする。新規質問時・回答取得後の表示を管理する。

- 別端末への自動同期や共有DBは含まない。

根拠：[index.html](https://github.com/gurii-gabreh/AI-Oracle/blob/461872e7d607c1f7ee9a79f45782191e42670890/index.html)

## 確認範囲

- 確認日：2026-10-17 JST
- 対象ブランチ：`main`
- 対象コミット：[461872e7d607c1f7ee9a79f45782191e42670890](https://github.com/gurii-gabreh/AI-Oracle/tree/461872e7d607c1f7ee9a79f45782191e42670890)
- コード・文書から作成。外部サービスへの書込み、アプリの起動、稼働環境の認証・データ確認は行っていません。
- Canvasは単体で読めるテキストカードです。Vaultへ配置して開いてください。接続先・ID・カード重複は生成スクリプトで検証済み。Obsidian実アプリでの表示確認は未実施です。
- 再生成は個人スキル `obsidian-app-mindmap` の `scripts/build_canvas.py` にJSONと出力先を渡します。
- JPGの作業は含みません。
