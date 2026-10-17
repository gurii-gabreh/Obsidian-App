# AI Research Radar：システム内部の連関図

[Obsidian用の連関図](system-map.canvas) ／ [再生成用データ](system-map.data.json)

Sheetsの依頼タスクに従ってGeminiで調べ、収集ログを蓄積して閲覧する。

## 最初に見るポイント

ServiceNow版とは別方式。依頼管理と正本はGoogle Sheets、調査担当はGemini＋必要時のClaude。

## 内部の役割

| 部分 | 何をするか |
|---|---|
| Sheetsの依頼タスク | Repo・Task・状態・備考から調査対象を選ぶ。 |
| GASの調査ループ | 調査・保存・状態更新と代替結果の取込みを担当する。 |
| 検索付きGemini | キーワードに合う情報を検索して要約する。 |
| 収集ログのSheet | 調査結果を追加し、GASのGETで返す。 |
| GitHubのJSONミラー | Sheetの結果をresearch-log.jsonへ書き出す。 |
| 記事一覧の静的画面 | 設定した取得先から記事を読み、カテゴリ等で表示する。 |
| Claudeによる代替調査 | API制限時などに、外部のClaude作業結果をGitHub経由で渡す。 |

## データのつながり

### 依頼から収集ログへ

- Sheetsの依頼タスク → GASの調査ループ：対象キーワードを読む
- GASの調査ループ → 検索付きGemini：検索・要約を依頼
- 検索付きGemini → 収集ログのSheet：結果をGAS経由で追記

### 閲覧と外部保存

- 収集ログのSheet → 記事一覧の静的画面：GAS GETのJSONで表示

### Claude代替結果の取込み

- Claudeによる代替調査 → GASの調査ループ：公開JSONをGASが取得
- GASの調査ループ → 収集ログのSheet：未取込の結果を保存
- 収集ログのSheet → GitHubのJSONミラー：内容が変わればGitHubへミラー

## 内部仕様と確認事項

### Sheetsの依頼タスク

対象Repoの未完了タスクをGASが読み、毎日／N回マーカーを解析して繰り返し回数を管理する。


根拠：[gas/Code.gs](https://github.com/gurii-gabreh/ai-research-radar/blob/e4de992173d6642e1ed0cdb9ac84c5163be3b947/gas/Code.gs)、[README.md](https://github.com/gurii-gabreh/ai-research-radar/blob/e4de992173d6642e1ed0cdb9ac84c5163be3b947/README.md)

### GASの調査ループ

collectAIResearchが各キーワードを処理する。レート制限等を扱い、時間トリガーや測定結果に基づく時刻選定の補助関数を持つ。

- 実際のトリガーと実行成功は未確認。

根拠：[gas/Code.gs](https://github.com/gurii-gabreh/ai-research-radar/blob/e4de992173d6642e1ed0cdb9ac84c5163be3b947/gas/Code.gs)

### 検索付きGemini

generateContentにgoogle_searchを指定し、カテゴリ・タイトル・日本語要約・出典URLのJSONを要求する。

- モデルの回答とURLをそのまま事実保証するものではない。

根拠：[gas/Code.gs](https://github.com/gurii-gabreh/ai-research-radar/blob/e4de992173d6642e1ed0cdb9ac84c5163be3b947/gas/Code.gs)

### 収集ログのSheet

収集ログを正本とし、doGetが行を新しい順のJSONへ変換する。ソースURL等で重複を扱う。


根拠：[gas/Code.gs](https://github.com/gurii-gabreh/ai-research-radar/blob/e4de992173d6642e1ed0cdb9ac84c5163be3b947/gas/Code.gs)

### GitHubのJSONミラー

exportResearchLogToGithub_が既存ファイルと比較し、変更時だけGitHub Contents APIで更新する。

- GitHub認証はGASのプロパティ設定が必要。

根拠：[gas/Code.gs](https://github.com/gurii-gabreh/ai-research-radar/blob/e4de992173d6642e1ed0cdb9ac84c5163be3b947/gas/Code.gs)

### 記事一覧の静的画面

index.htmlのloadDataがGAS URLをfetchし、一覧・分類を描画する。接続設定をlocalStorageで保持する。

- GASの接続先とWeb公開状態は未検証。

根拠：[index.html](https://github.com/gurii-gabreh/ai-research-radar/blob/e4de992173d6642e1ed0cdb9ac84c5163be3b947/index.html)

### Claudeによる代替調査

Claudeがdata/claude-fallback-results.jsonへ追記する運用を持ち、GASがキーワードを突き合わせて取り込む。

- Claude作業そのものをGASが自動起動するコードではない。

根拠：[README.md](https://github.com/gurii-gabreh/ai-research-radar/blob/e4de992173d6642e1ed0cdb9ac84c5163be3b947/README.md)、[gas/Code.gs](https://github.com/gurii-gabreh/ai-research-radar/blob/e4de992173d6642e1ed0cdb9ac84c5163be3b947/gas/Code.gs)

## 確認範囲

- 確認日：2026-10-17 JST
- 対象ブランチ：`main`
- 対象コミット：[e4de992173d6642e1ed0cdb9ac84c5163be3b947](https://github.com/gurii-gabreh/ai-research-radar/tree/e4de992173d6642e1ed0cdb9ac84c5163be3b947)
- コード・文書から作成。外部サービスへの書込み、アプリの起動、稼働環境の認証・データ確認は行っていません。
- Canvasは単体で読めるテキストカードです。Vaultへ配置して開いてください。接続先・ID・カード重複は生成スクリプトで検証済み。Obsidian実アプリでの表示確認は未実施です。
- 再生成は個人スキル `obsidian-app-mindmap` の `scripts/build_canvas.py` にJSONと出力先を渡します。
- JPGの作業は含みません。
