# Prompt Manager：システム内部の連関図

[Obsidian用の連関図](system-map.canvas) ／ [再生成用データ](system-map.data.json)

参照プロンプトの収集・検索と、Claude CLIを使うローカル生成を分けたツール。

## 最初に見るポイント

GASは収集と保存、ローカルNodeは生成だけを担当し、Google認証を生成サーバーへ渡さない。

## 内部の役割

| 部分 | 何をするか |
|---|---|
| ブラウザのワークフロー | 参考資料・回答レベル・プレビュー・生成結果を操作する。 |
| CHAPRO収集GAS | 公開一覧APIと詳細を少しずつ収集する。 |
| Google Sheets | 取得プロンプト・設定・生成履歴を残す。 |
| ローカルNodeサーバー | localhostのAPIがClaude CLIへ仕事を渡す。 |
| Claude CLIと手動貼付 | 指示文の生成・質問・回答例を作る。 |

## データのつながり

### 参考情報を読む

- CHAPRO収集GAS → Google Sheets：取得済みプロンプトを保存
- Google Sheets → ブラウザのワークフロー：GAS経由で検索・表示

### 条件からプロンプトを生成

- ブラウザのワークフロー → ローカルNodeサーバー：候補・要望・レベルをPOST
- ローカルNodeサーバー → Claude CLIと手動貼付：メタプロンプトをCLIへ渡す
- Claude CLIと手動貼付 → ブラウザのワークフロー：結果と参照理由を表示

## 内部仕様と確認事項

### ブラウザのワークフロー

index.htmlがGASから一覧を読み、bigram類似度等で参照候補を選ぶ。設定や定型文はlocalStorageへ持つ。


根拠：[index.html](https://github.com/gurii-gabreh/PROMPT-MANAGER/blob/55f777f463cd3f4a4326bb6f2e237a861ddf815b/index.html)

### CHAPRO収集GAS

Code.gsがIDキュー・重複確認・日次実行カウント・時間トリガーを管理する。doGet／doPostは一覧取得と履歴保存等も受ける。

- 外部APIの現在の取得可否と実トリガーは未検証。

根拠：[Code.gs](https://github.com/gurii-gabreh/PROMPT-MANAGER/blob/55f777f463cd3f4a4326bb6f2e237a861ddf815b/Code.gs)

### Google Sheets

GASがシートのヘッダーを確認し、既存IDを避けて保存する。生成結果は履歴記録ボタンのPOSTで保存する。

- 生成しただけでは自動保存しない。GASの実デプロイは別途必要。

根拠：[Code.gs](https://github.com/gurii-gabreh/PROMPT-MANAGER/blob/55f777f463cd3f4a4326bb6f2e237a861ddf815b/Code.gs)、[index.html](https://github.com/gurii-gabreh/PROMPT-MANAGER/blob/55f777f463cd3f4a4326bb6f2e237a861ddf815b/index.html)

### ローカルNodeサーバー

server.jsに/health、/generate、/questions、/preview、/shutdownを持つ。JSONの条件を指示文にし、claude -pの結果を解析して返す。

- 手元のNodeと認証済みClaude CLIが必要。ローカルサーバーはSheetsへは書かない。

根拠：[local-server/server.js](https://github.com/gurii-gabreh/PROMPT-MANAGER/blob/55f777f463cd3f4a4326bb6f2e237a861ddf815b/local-server/server.js)、[local-server/README.md](https://github.com/gurii-gabreh/PROMPT-MANAGER/blob/55f777f463cd3f4a4326bb6f2e237a861ddf815b/local-server/README.md)

### Claude CLIと手動貼付

ローカルサーバーが子プロセスでClaudeを呼ぶ。UIには指示コピーと結果貼付の補助手順も実装される。

- 生成品質・CLIの利用上限や契約条件はコードから保証できない。

根拠：[local-server/server.js](https://github.com/gurii-gabreh/PROMPT-MANAGER/blob/55f777f463cd3f4a4326bb6f2e237a861ddf815b/local-server/server.js)、[index.html](https://github.com/gurii-gabreh/PROMPT-MANAGER/blob/55f777f463cd3f4a4326bb6f2e237a861ddf815b/index.html)

## 確認範囲

- 確認日：2026-10-17 JST
- 対象ブランチ：`claude/upbeat-cori-cus3xa`
- 対象コミット：[55f777f463cd3f4a4326bb6f2e237a861ddf815b](https://github.com/gurii-gabreh/PROMPT-MANAGER/tree/55f777f463cd3f4a4326bb6f2e237a861ddf815b)
- コード・文書から作成。外部サービスへの書込み、アプリの起動、稼働環境の認証・データ確認は行っていません。
- Canvasは単体で読めるテキストカードです。Vaultへ配置して開いてください。接続先・ID・カード重複は生成スクリプトで検証済み。Obsidian実アプリでの表示確認は未実施です。
- 再生成は個人スキル `obsidian-app-mindmap` の `scripts/build_canvas.py` にJSONと出力先を渡します。
- JPGの作業は含みません。
