# Supermarket Price Tracker：システム内部の連関図

[Obsidian用の連関図](system-map.canvas) ／ [再生成用データ](system-map.data.json)

住所周辺の店舗とチラシ画像から商品価格を収集し、日付・店舗別に閲覧する。

## 最初に見るポイント

手動取得と定期の更新チェックは別経路。外部にある想定のPython処理を完了済みと扱わない。

## 内部の役割

| 部分 | 何をするか |
|---|---|
| 画面とJSモジュール | 店舗設定・収集操作・価格一覧を分けて表示する。 |
| GAS API | Webからの依頼を受け、店舗検索・取得・保存を振り分ける。 |
| トクバイとチラシ画像 | 店舗URL・掲載画像を調べ、価格解析の入力にする。 |
| Geminiによる価格抽出 | 画像から商品・価格・期間などを構造化する。 |
| Google Sheets | 店舗・価格履歴・設定・平均価格などを保存する。 |
| 更新チェックと収集待ちリスト | 定期処理は更新店舗を待ちリストへ登録する。 |

## データのつながり

### 手動の価格取得

- 画面とJSモジュール → GAS API：actionで収集依頼
- GAS API → トクバイとチラシ画像：店舗・画像を取得
- トクバイとチラシ画像 → Geminiによる価格抽出：画像から商品情報を抽出

### 保存して閲覧

- Geminiによる価格抽出 → Google Sheets：価格・店舗・収集日時を保存
- Google Sheets → 画面とJSモジュール：条件に合う履歴を返す

### 定期処理の範囲

- 更新チェックと収集待ちリスト → Google Sheets：更新店舗の待ちリストと平均価格を更新

## 内部仕様と確認事項

### 画面とJSモジュール

index.html、home.html、prices.htmlとapp.js／ui.js／scraper.jsで構成する。設定済みGASへactionを送って取得・保存する。

- 接続先の稼働環境は未検証。

根拠：[index.html](https://github.com/gurii-gabreh/supermarket-price-tracker/blob/8e4c251f4a91d7b2e424f1cfc5a5c92aab899b97/index.html)、[prices.html](https://github.com/gurii-gabreh/supermarket-price-tracker/blob/8e4c251f4a91d7b2e424f1cfc5a5c92aab899b97/prices.html)、[js/app.js](https://github.com/gurii-gabreh/supermarket-price-tracker/blob/8e4c251f4a91d7b2e424f1cfc5a5c92aab899b97/js/app.js)、[js/scraper.js](https://github.com/gurii-gabreh/supermarket-price-tracker/blob/8e4c251f4a91d7b2e424f1cfc5a5c92aab899b97/js/scraper.js)

### GAS API

doGet／doPostがfindStores、scrape、save等を受け、UrlFetchAppとSpreadsheetAppの処理へ渡す。

- 読み取り以外の操作にもGET入口がある。個別利用者の認証は今回確認していない。

根拠：[Code.gs](https://github.com/gurii-gabreh/supermarket-price-tracker/blob/8e4c251f4a91d7b2e424f1cfc5a5c92aab899b97/Code.gs)

### トクバイとチラシ画像

住所検索、店舗URLの検証、チラシ画像URL抽出を行う。画像取得不能時の処理もGAS側へまとめる。

- 対象サイトのHTMLやURL構造に依存する。

根拠：[Code.gs](https://github.com/gurii-gabreh/supermarket-price-tracker/blob/8e4c251f4a91d7b2e424f1cfc5a5c92aab899b97/Code.gs)、[js/scraper.js](https://github.com/gurii-gabreh/supermarket-price-tracker/blob/8e4c251f4a91d7b2e424f1cfc5a5c92aab899b97/js/scraper.js)

### Geminiによる価格抽出

analyzeChirashiImagesが画像と指示をGeminiへ送り、JSONを解析する。表記揺れ・日付・重複の調整処理を持つ。

- AIの抽出結果であり、印刷価格との一致をコードだけでは保証できない。

根拠：[Code.gs](https://github.com/gurii-gabreh/supermarket-price-tracker/blob/8e4c251f4a91d7b2e424f1cfc5a5c92aab899b97/Code.gs)

### Google Sheets

価格の保存、登録店舗と日付の読込、フィルタ候補生成、平均価格集計をGASが担当し、画面へ返す。


根拠：[Code.gs](https://github.com/gurii-gabreh/supermarket-price-tracker/blob/8e4c251f4a91d7b2e424f1cfc5a5c92aab899b97/Code.gs)、[prices.html](https://github.com/gurii-gabreh/supermarket-price-tracker/blob/8e4c251f4a91d7b2e424f1cfc5a5c92aab899b97/prices.html)

### 更新チェックと収集待ちリスト

autoCollectは自動ONを確認し、更新店舗を「収集待ちリスト」へ記録して平均価格を集計する。トリガーの作成・切替処理がある。

- この定期関数はPython側の価格取得を想定しており、このリポジトリにはそのPythonコードがない。定期実行だけで価格解析まで完了するとは言えない。

根拠：[Code.gs](https://github.com/gurii-gabreh/supermarket-price-tracker/blob/8e4c251f4a91d7b2e424f1cfc5a5c92aab899b97/Code.gs)

## 確認範囲

- 確認日：2026-10-17 JST
- 対象ブランチ：`main`
- 対象コミット：[8e4c251f4a91d7b2e424f1cfc5a5c92aab899b97](https://github.com/gurii-gabreh/supermarket-price-tracker/tree/8e4c251f4a91d7b2e424f1cfc5a5c92aab899b97)
- コード・文書から作成。外部サービスへの書込み、アプリの起動、稼働環境の認証・データ確認は行っていません。
- Canvasは単体で読めるテキストカードです。Vaultへ配置して開いてください。接続先・ID・カード重複は生成スクリプトで検証済み。Obsidian実アプリでの表示確認は未実施です。
- 再生成は個人スキル `obsidian-app-mindmap` の `scripts/build_canvas.py` にJSONと出力先を渡します。
- JPGの作業は含みません。
