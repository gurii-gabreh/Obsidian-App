# App Development Info：システム内部の連関図

[Obsidian用の連関図](system-map.canvas) ／ [再生成用データ](system-map.data.json)

アプリ開発の学習資料と、自分の理解・正誤・第二意見の記録を分けて読む静的サイト。

## 最初に見るポイント

学習資料そのものと、自分がどう理解したかの記録を別のHTMLとして管理する。

## 内部の役割

| 部分 | 何をするか |
|---|---|
| 分野一覧 | 学習資料と記録へリンクする入口。 |
| 学習資料 | 分野ごとの説明とレッスンをHTMLで保持する。 |
| 学習記録 | 理解の原文、正誤、他AI確認の記録を分ける。 |
| 学習・開発の方針 | 確認の基準や進め方をページとして示す。 |
| 静的公開 | GitHub ActionsからHTMLをPagesへ公開する。 |

## データのつながり

### 学習の参照経路

- 分野一覧 → 学習資料：分野・レッスンのHTMLへ遷移

### 理解の振り返り

- 分野一覧 → 学習記録：別の記録ページを開いて比較

## 内部仕様と確認事項

### 分野一覧

index.htmlからアーキテクチャ、サーバー、セキュリティ、AI、DB、テスト、設計等のページを開く。


根拠：[index.html](https://github.com/gurii-gabreh/App-Development-Info/blob/74dd21a58f133b43c0e0aa7edaa3331f1b296ec1/index.html)、[README.md](https://github.com/gurii-gabreh/App-Development-Info/blob/74dd21a58f133b43c0e0aa7edaa3331f1b296ec1/README.md)

### 学習資料

materials配下の分野ページとlessonページに内容を直接記載する。画面はHTML同士のリンクで遷移する。

- 動的な教材DBや外部AI自動生成のランタイムではない。

根拠：[README.md](https://github.com/gurii-gabreh/App-Development-Info/blob/74dd21a58f133b43c0e0aa7edaa3331f1b296ec1/README.md)、[index.html](https://github.com/gurii-gabreh/App-Development-Info/blob/74dd21a58f133b43c0e0aa7edaa3331f1b296ec1/index.html)

### 学習記録

records配下に記録を置き、資料とは別に管理する。未実施の確認は未実施として残す方針。

- サイトに学習記録の入力・自動採点・保存APIは見当たらない。

根拠：[README.md](https://github.com/gurii-gabreh/App-Development-Info/blob/74dd21a58f133b43c0e0aa7edaa3331f1b296ec1/README.md)

### 学習・開発の方針

policy.htmlを独立させ、学習の進め方と記録の扱いを参照できるようにする。


根拠：[policy.html](https://github.com/gurii-gabreh/App-Development-Info/blob/74dd21a58f133b43c0e0aa7edaa3331f1b296ec1/policy.html)

### 静的公開

deploy-pages.ymlがmain更新を入口に静的ページを配置する。更新はHTMLの編集・commitで行う。

- 外部サービスの稼働や現在の公開結果は未検証。

根拠：[.github/workflows/deploy-pages.yml](https://github.com/gurii-gabreh/App-Development-Info/blob/74dd21a58f133b43c0e0aa7edaa3331f1b296ec1/.github/workflows/deploy-pages.yml)

## 確認範囲

- 確認日：2026-10-17 JST
- 対象ブランチ：`main`
- 対象コミット：[74dd21a58f133b43c0e0aa7edaa3331f1b296ec1](https://github.com/gurii-gabreh/App-Development-Info/tree/74dd21a58f133b43c0e0aa7edaa3331f1b296ec1)
- コード・文書から作成。外部サービスへの書込み、アプリの起動、稼働環境の認証・データ確認は行っていません。
- Canvasは単体で読めるテキストカードです。Vaultへ配置して開いてください。接続先・ID・カード重複は生成スクリプトで検証済み。Obsidian実アプリでの表示確認は未実施です。
- 再生成は個人スキル `obsidian-app-mindmap` の `scripts/build_canvas.py` にJSONと出力先を渡します。
- JPGの作業は含みません。
