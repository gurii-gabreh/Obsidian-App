# My Dashboard：システム内部の連関図

[Obsidian用の連関図](system-map.canvas) ／ [再生成用データ](system-map.data.json)

作成したアプリや作業用リンクを、タブ別にまとめる個人ポータル。

## 最初に見るポイント

アプリ同士をAPIでつなぐ仕組みではなく、人が移動する入口をまとめたポータル。

## 内部の役割

| 部分 | 何をするか |
|---|---|
| 静的なリンク集 | タブ切替で作業先やアプリを探す。 |
| 他アプリへの入口 | 学習・英会話・価格収集・AIツール等へ移動する。 |
| 文字抽出用プロンプト | 作業の指示文を表示・コピーする。 |
| HTMLに埋め込んだ情報 | 一覧内容をHTMLの編集で更新する。 |

## データのつながり

### アプリへ移動

- 静的なリンク集 → 他アプリへの入口：リンクカードから別サイトへ開く

### 作業指示を渡す

- 静的なリンク集 → 文字抽出用プロンプト：モーダルで指示文を表示・コピー

## 内部仕様と確認事項

### 静的なリンク集

index.htmlがリンクカードとshowTab等を内包する。外部アプリはアンカーリンクで開く。


根拠：[index.html](https://github.com/gurii-gabreh/my-dashboard/blob/f1fd4dabd7a6fa057c56e5343820bc8995dd5595/index.html)

### 他アプリへの入口

English-Speaking-App、PROMPT-MANAGER、ai-tool-manager、gemini-monitor、study-app、supermarket-price-tracker等の公開ページへのリンクが記載される。

- リンクの存在は他アプリとのデータ同期を意味しない。リンク先の現在の公開状態は未検証。

根拠：[index.html](https://github.com/gurii-gabreh/my-dashboard/blob/f1fd4dabd7a6fa057c56e5343820bc8995dd5595/index.html)

### 文字抽出用プロンプト

openTexModal／copyTexPromptで、別のAIへ渡すための文字抽出指示を表示しクリップボードへコピーする。

- このページ自身で画像を解析したりClaudeを呼び出したりする処理ではない。

根拠：[index.html](https://github.com/gurii-gabreh/my-dashboard/blob/f1fd4dabd7a6fa057c56e5343820bc8995dd5595/index.html)

### HTMLに埋め込んだ情報

リンクと説明はindex.htmlに直接書かれている。動的DB取得や保存APIのコードは見当たらない。

- 共有データの正本や個人別の同期機能を持つアプリではない。

根拠：[index.html](https://github.com/gurii-gabreh/my-dashboard/blob/f1fd4dabd7a6fa057c56e5343820bc8995dd5595/index.html)

## 確認範囲

- 確認日：2026-10-17 JST
- 対象ブランチ：`main`
- 対象コミット：[f1fd4dabd7a6fa057c56e5343820bc8995dd5595](https://github.com/gurii-gabreh/my-dashboard/tree/f1fd4dabd7a6fa057c56e5343820bc8995dd5595)
- コード・文書から作成。外部サービスへの書込み、アプリの起動、稼働環境の認証・データ確認は行っていません。
- Canvasは単体で読めるテキストカードです。Vaultへ配置して開いてください。接続先・ID・カード重複は生成スクリプトで検証済み。Obsidian実アプリでの表示確認は未実施です。
- 再生成は個人スキル `obsidian-app-mindmap` の `scripts/build_canvas.py` にJSONと出力先を渡します。
- JPGの作業は含みません。
