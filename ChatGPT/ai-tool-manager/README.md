# AI Tool Manager：システム内部の連関図

[Obsidian用の連関図](system-map.canvas) ／ [再生成用データ](system-map.data.json)

AIツール情報の取得・整理・一覧表示とGoogle Sheetsへの保存を、単一HTMLから操作する。

## 最初に見るポイント

ブラウザで取得・AI補完・保存をつなぐ。独自サーバーは含まず、GAS機能は外部依存。

## 内部の役割

| 部分 | 何をするか |
|---|---|
| 単一HTMLの管理画面 | カテゴリ検索・並び替え・収集・同期をブラウザで操作する。 |
| AI GalleryのREST API | 公開WordPress APIをページ送りしてツール一覧を取得する。 |
| Gemini補完と手動受け渡し | 説明の補完をAPIまたはコピー＆貼付の手順で行う。 |
| Googleログイン | OAuthでSheetsへのアクセス権を得る。 |
| Google Sheetsへの同期 | 取得済みデータを一覧シートへ書き込む。 |
| 外部GASの補助機能 | 保存・スケジュール・収集ログを設定済みGASへ依頼する。 |

## データのつながり

### ツールの取得と整理

- AI GalleryのREST API → Gemini補完と手動受け渡し：取得情報を補完・整形
- Gemini補完と手動受け渡し → 単一HTMLの管理画面：結果を一覧へ反映

### 一覧の共有保存

- 単一HTMLの管理画面 → Googleログイン：Googleログインで権限取得
- Googleログイン → Google Sheetsへの同期：トークンでSheetsを読み書き

## 内部仕様と確認事項

### 単一HTMLの管理画面

index.html内にUI、一覧データ、収集・整形・保存の処理をまとめる。取得済みツールと設定はlocalStorageへ保存する。

- 端末をまたぐ共有にはSheetsからの読込または同期が必要。

根拠：[index.html](https://github.com/gurii-gabreh/ai-tool-manager/blob/f9aba42f6fc117a04f16f69357e77aeab1baf202/index.html)

### AI GalleryのREST API

toolsとtool-category APIを使う。総ページ数を読み、残りを5ページずつ取得する。差分取得やカテゴリ情報の補完も実装する。

- 取得先APIの現在の応答や件数は未検証。

根拠：[index.html](https://github.com/gurii-gabreh/ai-tool-manager/blob/f9aba42f6fc117a04f16f69357e77aeab1baf202/index.html)

### Gemini補完と手動受け渡し

ブラウザからGemini generateContentへ送る経路と、取得情報を100件単位に分けてGemini画面へ手動で渡し、戻ったJSONを読む経路を持つ。

- 手動受け渡しは自動API連携ではない。設定したAPIキーを使う経路の利用上限・料金は未確認。

根拠：[index.html](https://github.com/gurii-gabreh/ai-tool-manager/blob/f9aba42f6fc117a04f16f69357e77aeab1baf202/index.html)

### Googleログイン

Google Identity ServicesのinitTokenClientでSheetsスコープのアクセストークンを取得し、ブラウザ内で利用する。

- 認証設定やOAuthクライアントの現在の動作は未検証。

根拠：[index.html](https://github.com/gurii-gabreh/ai-tool-manager/blob/f9aba42f6fc117a04f16f69357e77aeab1baf202/index.html)

### Google Sheetsへの同期

syncToSheetsは対象シートの値をclearしてから一覧をPUTし、書式設定を適用する。Sheetsから読み直す処理も持つ。

- この同期経路は差分追記ではなくクリア後の書き直し。対象範囲に注意する。

根拠：[index.html](https://github.com/gurii-gabreh/ai-tool-manager/blob/f9aba42f6fc117a04f16f69357e77aeab1baf202/index.html)

### 外部GASの補助機能

callGasApiはGAS URLへGETでactionを送る。setSchedule等の呼出し口をUIに備える。

- GAS本体のコードはこのリポジトリに存在しない。外部にある実装や実際のスケジュール稼働は確認できない。

根拠：[index.html](https://github.com/gurii-gabreh/ai-tool-manager/blob/f9aba42f6fc117a04f16f69357e77aeab1baf202/index.html)

## 確認範囲

- 確認日：2026-10-17 JST
- 対象ブランチ：`main`
- 対象コミット：[f9aba42f6fc117a04f16f69357e77aeab1baf202](https://github.com/gurii-gabreh/ai-tool-manager/tree/f9aba42f6fc117a04f16f69357e77aeab1baf202)
- コード・文書から作成。外部サービスへの書込み、アプリの起動、稼働環境の認証・データ確認は行っていません。
- Canvasは単体で読めるテキストカードです。Vaultへ配置して開いてください。接続先・ID・カード重複は生成スクリプトで検証済み。Obsidian実アプリでの表示確認は未実施です。
- 再生成は個人スキル `obsidian-app-mindmap` の `scripts/build_canvas.py` にJSONと出力先を渡します。
- JPGの作業は含みません。
