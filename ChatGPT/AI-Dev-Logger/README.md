# AI Dev Logger：システム内部の連関図

[Obsidian用の連関図](system-map.canvas) ／ [再生成用データ](system-map.data.json)

ZIPに収められたChrome拡張。AIとの会話から進捗・問題・技術を抽出しNotionへ同期する。

## 最初に見るポイント

公開ソースはZIPのみなので中身を展開して確認。Notionの実装はREADMEよりコードを優先して説明する。

## 内部の役割

| 部分 | 何をするか |
|---|---|
| Chromeサイドパネル | 情報取得・システム選択・Notion同期を操作する。 |
| content script | 対応サイトの会話DOMから差分テキストを抽出する。 |
| 拡張のbackground | メッセージを受け、Gemini・Notionへの通信を担当する。 |
| Gemini API | 会話を進捗・問題・技術の構造へ変える。 |
| Notionデータベース | プロジェクト単位の進捗・問題・技術を保存する。 |

## データのつながり

### 会話を解析する

- Chromeサイドパネル → content script：取得ボタンで対象タブへ依頼
- content script → 拡張のbackground：会話の差分を返す
- 拡張のbackground → Gemini API：構造化解析を依頼

### 解析結果を保存する

- Gemini API → 拡張のbackground：結果をマージ
- 拡張のbackground → Notionデータベース：選択プロジェクトのpropertiesを更新
- Notionデータベース → Chromeサイドパネル：同期結果を表示

## 内部仕様と確認事項

### Chromeサイドパネル

Manifest V3の拡張としてsidepanel.html／sidepanel.jsを表示する。設定・累積ログはchrome.storage.localで管理する。

- 通常のWebページではなく、ZIP内のdev-loggerフォルダを拡張として読み込む。

根拠：[dev-logger.zip 内 dev-logger/manifest.json](https://github.com/gurii-gabreh/AI-Dev-Logger/blob/bc1d5af3807cbf6caca2d30d06c444d4255fe5cd/dev-logger.zip)、[dev-logger.zip 内 dev-logger/sidepanel.js](https://github.com/gurii-gabreh/AI-Dev-Logger/blob/bc1d5af3807cbf6caca2d30d06c444d4255fe5cd/dev-logger.zip)

### content script

Claude.ai、Gemini、ChatGPT、AI Studio等の会話を対象とし、前回のターン数等から追加部分を取得する。

- サイトのDOM変更で抽出が壊れる可能性がある。

根拠：[dev-logger.zip 内 dev-logger/content_script.js](https://github.com/gurii-gabreh/AI-Dev-Logger/blob/bc1d5af3807cbf6caca2d30d06c444d4255fe5cd/dev-logger.zip)

### 拡張のbackground

FETCH_AND_ANALYZE等のメッセージを処理し、GeminiのJSONを既存ログやNotionデータとマージする。

- この拡張はClaude用の音声ブリッジとは別アプリ。

根拠：[dev-logger.zip 内 dev-logger/background.js](https://github.com/gurii-gabreh/AI-Dev-Logger/blob/bc1d5af3807cbf6caca2d30d06c444d4255fe5cd/dev-logger.zip)

### Gemini API

generateContentへ会話と抽出指示を送り、progress／issues／techStack／summaryを解析する。

- 選択した会話内容が外部AIへ送られる。キー・モデル設定は利用者側で必要。

根拠：[dev-logger.zip 内 dev-logger/background.js](https://github.com/gurii-gabreh/AI-Dev-Logger/blob/bc1d5af3807cbf6caca2d30d06c444d4255fe5cd/dev-logger.zip)

### Notionデータベース

現行コードのwriteToNotionはページpropertiesをPATCHする。同期日時、進捗タスク、問題ログ、使用技術、廃止技術などのプロパティへマージ済みテキストを書き込む。

- READMEの「本文ブロックへ追記」という説明とは異なる。コードが使うプロパティ名・型の準備が必要。

根拠：[dev-logger.zip 内 dev-logger/background.js](https://github.com/gurii-gabreh/AI-Dev-Logger/blob/bc1d5af3807cbf6caca2d30d06c444d4255fe5cd/dev-logger.zip)、[dev-logger.zip 内 dev-logger/README.md](https://github.com/gurii-gabreh/AI-Dev-Logger/blob/bc1d5af3807cbf6caca2d30d06c444d4255fe5cd/dev-logger.zip)

## 確認範囲

- 確認日：2026-10-17 JST
- 対象ブランチ：`main`
- 対象コミット：[bc1d5af3807cbf6caca2d30d06c444d4255fe5cd](https://github.com/gurii-gabreh/AI-Dev-Logger/tree/bc1d5af3807cbf6caca2d30d06c444d4255fe5cd)
- コード・文書から作成。外部サービスへの書込み、アプリの起動、稼働環境の認証・データ確認は行っていません。
- Canvasは単体で読めるテキストカードです。Vaultへ配置して開いてください。接続先・ID・カード重複は生成スクリプトで検証済み。Obsidian実アプリでの表示確認は未実施です。
- 再生成は個人スキル `obsidian-app-mindmap` の `scripts/build_canvas.py` にJSONと出力先を渡します。
- JPGの作業は含みません。
