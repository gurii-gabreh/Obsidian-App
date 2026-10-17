# Task WBS／Study Schedule：システム内部の連関図

[Obsidian用の連関図](system-map.canvas) ／ [再生成用データ](system-map.data.json)

学習タスクを階層と日付で整理し、WBS・ガント・今日の作業として読む。

## 最初に見るポイント

入力をClaude Code、集計・表示をブラウザが担当し、親の状態を重複管理しない。

## 内部の役割

| 部分 | 何をするか |
|---|---|
| Claude Codeによるタスク更新 | チャットで依頼し、tasks.jsonを更新する運用。 |
| 再帰的なタスクツリー | childrenで目標から実行タスクまでを表す。 |
| WBS・ガント表示 | 期間・遅延・完了数を計算して日別に表示する。 |
| 今日の作業ページ | 実行タスクを取り出して今日の対象等を表示する。 |
| GitHub Pages | main更新から静的ページを配信する。 |

## データのつながり

### データからWBSへ

- Claude Codeによるタスク更新 → 再帰的なタスクツリー：タスクのJSONをcommit
- 再帰的なタスクツリー → WBS・ガント表示：末端から集計して描画

### 今日の作業を見る

- 再帰的なタスクツリー → 今日の作業ページ：葉タスクを抽出・分類して表示

## 内部仕様と確認事項

### Claude Codeによるタスク更新

READMEはタスク追加・状態変更をClaude Codeで行う方式を示す。サイトはJSONを読む側を担当する。

- 画面上で共有タスクを直接保存するAPIはない。

根拠：[README.md](https://github.com/gurii-gabreh/Task-WBS/blob/32e24a75335800153651dc81cf1ca12f0105b3f8/README.md)、[index.html](https://github.com/gurii-gabreh/Task-WBS/blob/32e24a75335800153651dc81cf1ca12f0105b3f8/index.html)

### 再帰的なタスクツリー

末端の葉がstart／plannedEnd／actualEnd／status／priorityを持つ。中間ノードの期間や進捗は子から集計する。


根拠：[README.md](https://github.com/gurii-gabreh/Task-WBS/blob/32e24a75335800153651dc81cf1ca12f0105b3f8/README.md)、[index.html](https://github.com/gurii-gabreh/Task-WBS/blob/32e24a75335800153651dc81cf1ca12f0105b3f8/index.html)

### WBS・ガント表示

computeNodeで再帰集計し、カレンダーセルと階層行を描く。実績ログの表示も行う。


根拠：[index.html](https://github.com/gurii-gabreh/Task-WBS/blob/32e24a75335800153651dc81cf1ca12f0105b3f8/index.html)

### 今日の作業ページ

today.htmlがJSONから葉を集め、期日・状態等で分類して一覧を表示する。

- READMEの「唯一のビューア」という旧記述より、現在のコードにある追加ページを優先して図示する。

根拠：[today.html](https://github.com/gurii-gabreh/Task-WBS/blob/32e24a75335800153651dc81cf1ca12f0105b3f8/today.html)

### GitHub Pages

deploy-pages.ymlが静的ファイルを公開する。画面はdata/tasks.jsonをfetchする。

- 現在の公開設定や実データの最新反映は未検証。

根拠：[.github/workflows/deploy-pages.yml](https://github.com/gurii-gabreh/Task-WBS/blob/32e24a75335800153651dc81cf1ca12f0105b3f8/.github/workflows/deploy-pages.yml)、[index.html](https://github.com/gurii-gabreh/Task-WBS/blob/32e24a75335800153651dc81cf1ca12f0105b3f8/index.html)

## 確認範囲

- 確認日：2026-10-17 JST
- 対象ブランチ：`main`
- 対象コミット：[32e24a75335800153651dc81cf1ca12f0105b3f8](https://github.com/gurii-gabreh/Task-WBS/tree/32e24a75335800153651dc81cf1ca12f0105b3f8)
- コード・文書から作成。外部サービスへの書込み、アプリの起動、稼働環境の認証・データ確認は行っていません。
- Canvasは単体で読めるテキストカードです。Vaultへ配置して開いてください。接続先・ID・カード重複は生成スクリプトで検証済み。Obsidian実アプリでの表示確認は未実施です。
- 再生成は個人スキル `obsidian-app-mindmap` の `scripts/build_canvas.py` にJSONと出力先を渡します。
- JPGの作業は含みません。
