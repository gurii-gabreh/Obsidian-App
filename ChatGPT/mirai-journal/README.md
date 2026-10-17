# Mirai Journal：システム内部の連関図

[Obsidian用の連関図](system-map.canvas) ／ [再生成用データ](system-map.data.json)

振り返り面談の分析結果を、目標・強み・キャリア・トレンドとして読むジャーナルのビューア。

## 最初に見るポイント

外部で記録・分析し、サイトは読む役割に徹する。古いAPI直呼出し構成と混同しない。

## 内部の役割

| 部分 | 何をするか |
|---|---|
| 外部での振り返り面談 | Gemini Live等で話した内容を別経路で記録・分析する。 |
| Claude Codeによる更新 | 分析後のデータをJSONへ直接commitする運用。 |
| 2つのJSON正本 | 高頻度の日記と低頻度の集計を分ける。 |
| ダッシュボードと詳細ページ | 日記、目標、自己分析、キャリア、週次レポートを表示する。 |

## データのつながり

### 記録から閲覧まで

- 外部での振り返り面談 → Claude Codeによる更新：トランスクリプトを渡して分析依頼
- Claude Codeによる更新 → 2つのJSON正本：結果を直接commit
- 2つのJSON正本 → ダッシュボードと詳細ページ：raw JSONを読み表示

## 内部仕様と確認事項

### 外部での振り返り面談

READMEの運用は、面談トランスクリプトをclaude-voice-bridgeへ保存し、Claude Codeへ分析依頼する。Codexへの委任は運用手順として記載される。

- このサイト内で音声面談やCLI実行を行う構成ではない。

根拠：[README.md](https://github.com/gurii-gabreh/mirai-journal/blob/490cdc6b931dea98f0ff2ac1b66d60b00b7ffac8/README.md)、[pages/settings.html](https://github.com/gurii-gabreh/mirai-journal/blob/490cdc6b931dea98f0ff2ac1b66d60b00b7ffac8/pages/settings.html)

### Claude Codeによる更新

journals.jsonに日次記録、profile.jsonに目標・強み・キャリア・トレンド・週次分析等をまとめる。

- AI分析がサイト操作だけで自動実行されるわけではない。

根拠：[README.md](https://github.com/gurii-gabreh/mirai-journal/blob/490cdc6b931dea98f0ff2ac1b66d60b00b7ffac8/README.md)、[js/data.js](https://github.com/gurii-gabreh/mirai-journal/blob/490cdc6b931dea98f0ff2ac1b66d60b00b7ffac8/js/data.js)

### 2つのJSON正本

data/journals.jsonとdata/profile.jsonをGitHubで管理し、Dataモジュールがraw URLから並列取得してメモリへ保持する。


根拠：[js/data.js](https://github.com/gurii-gabreh/mirai-journal/blob/490cdc6b931dea98f0ff2ac1b66d60b00b7ffac8/js/data.js)、[README.md](https://github.com/gurii-gabreh/mirai-journal/blob/490cdc6b931dea98f0ff2ac1b66d60b00b7ffac8/README.md)

### ダッシュボードと詳細ページ

各pages/*.htmlがDataの読取関数を使用する。ホームは連続記録・平均評価・最近の記録等を集計する。

- 新しい構成では画面上の保存・編集・削除やGemini API直呼出しを撤去している。

根拠：[js/app.js](https://github.com/gurii-gabreh/mirai-journal/blob/490cdc6b931dea98f0ff2ac1b66d60b00b7ffac8/js/app.js)、[js/data.js](https://github.com/gurii-gabreh/mirai-journal/blob/490cdc6b931dea98f0ff2ac1b66d60b00b7ffac8/js/data.js)、[pages/journal.html](https://github.com/gurii-gabreh/mirai-journal/blob/490cdc6b931dea98f0ff2ac1b66d60b00b7ffac8/pages/journal.html)、[pages/analysis.html](https://github.com/gurii-gabreh/mirai-journal/blob/490cdc6b931dea98f0ff2ac1b66d60b00b7ffac8/pages/analysis.html)、[README.md](https://github.com/gurii-gabreh/mirai-journal/blob/490cdc6b931dea98f0ff2ac1b66d60b00b7ffac8/README.md)

## 確認範囲

- 確認日：2026-10-17 JST
- 対象ブランチ：`main`
- 対象コミット：[490cdc6b931dea98f0ff2ac1b66d60b00b7ffac8](https://github.com/gurii-gabreh/mirai-journal/tree/490cdc6b931dea98f0ff2ac1b66d60b00b7ffac8)
- コード・文書から作成。外部サービスへの書込み、アプリの起動、稼働環境の認証・データ確認は行っていません。
- Canvasは単体で読めるテキストカードです。Vaultへ配置して開いてください。接続先・ID・カード重複は生成スクリプトで検証済み。Obsidian実アプリでの表示確認は未実施です。
- 再生成は個人スキル `obsidian-app-mindmap` の `scripts/build_canvas.py` にJSONと出力先を渡します。
- JPGの作業は含みません。
