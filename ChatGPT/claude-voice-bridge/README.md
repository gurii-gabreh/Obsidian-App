# Claude Voice Bridge：システム内部の連関図

[Obsidian用の連関図](system-map.canvas) ／ [再生成用データ](system-map.data.json)

Chrome拡張でClaude／Geminiの画面と音声・タスク・ログをつなぎ、GAS経由でGitHubへ記録する。

## 最初に見るポイント

AI API連携ではなくWeb画面を操作する拡張。共有記録はGAS→GitHub、Web版は参照・補助に分担する。

## 内部の役割

| 部分 | 何をするか |
|---|---|
| Chromeサイドパネルとウィジェット | 音声会話、タスク監査、定型文、設計、ジャーナルを操作する。 |
| ブラウザの音声入出力 | 呼びかけを認識して文字を送り、返答を読み上げる。 |
| content scriptの橋渡し | AIサイトの入力欄と応答DOMを操作する。 |
| タスクとログの状態管理 | 抽出結果と会話ログを端末に保持する。 |
| GASのGitHub書込み中継 | tracker・knowledge・room-log・journalを保存する。 |
| GitHubのJSON | タスク・テンプレート・ログ・ジャーナルを共有する。 |
| 拡張なしのWebビューア | JSONや設計情報を通常ページで参照し、ジャーナルを渡す。 |

## データのつながり

### 音声でAI画面と会話する

- ブラウザの音声入出力 → Chromeサイドパネルとウィジェット：認識した文字を渡す
- Chromeサイドパネルとウィジェット → content scriptの橋渡し：対象タブへ送信依頼
- content scriptの橋渡し → ブラウザの音声入出力：抽出した返答を読み上げへ返す

### タスク・ログを共有する

- タスクとログの状態管理 → GASのGitHub書込み中継：保存actionで送信
- GASのGitHub書込み中継 → GitHubのJSON：GitHub Contents APIで書込
- GitHubのJSON → 拡張なしのWebビューア：公開JSONを読んで表示

### 設計情報の参照

- GitHubのJSON → Chromeサイドパネルとウィジェット：テンプレート・タスクを再読込。設計は別RepoのJSONも参照

## 内部仕様と確認事項

### Chromeサイドパネルとウィジェット

Manifest V3の拡張としてsidepanelとwidget群を提供する。widget-common等で共通動作をまとめる。

- AI Dev Loggerとは別の拡張。対象AIサイトのログイン状態とUIが必要。

根拠：[manifest.json](https://github.com/gurii-gabreh/claude-voice-bridge/blob/f6e832014f183fa6890008cfab0d9969b2484c61/manifest.json)、[sidepanel.js](https://github.com/gurii-gabreh/claude-voice-bridge/blob/f6e832014f183fa6890008cfab0d9969b2484c61/sidepanel.js)、[widget-common.js](https://github.com/gurii-gabreh/claude-voice-bridge/blob/f6e832014f183fa6890008cfab0d9969b2484c61/widget-common.js)

### ブラウザの音声入出力

SpeechRecognitionとspeechSynthesisを使い、読み上げ中の再認識を避ける。対象をClaude／Geminiで切り替える。

- 独自のAI音声APIサーバーではない。ブラウザの音声機能・権限に依存する。

根拠：[sidepanel.js](https://github.com/gurii-gabreh/claude-voice-bridge/blob/f6e832014f183fa6890008cfab0d9969b2484c61/sidepanel.js)、[permission.js](https://github.com/gurii-gabreh/claude-voice-bridge/blob/f6e832014f183fa6890008cfab0d9969b2484c61/permission.js)

### content scriptの橋渡し

chrome.tabs.sendMessageで文字列を渡し、content.jsが入力・送信・応答監視・抽出を担当する。セレクタ上書きもできる。

- AI APIを直接呼ぶ方式ではなく、Web UIの構造変更に影響される。

根拠：[content.js](https://github.com/gurii-gabreh/claude-voice-bridge/blob/f6e832014f183fa6890008cfab0d9969b2484c61/content.js)、[sidepanel.js](https://github.com/gurii-gabreh/claude-voice-bridge/blob/f6e832014f183fa6890008cfab0d9969b2484c61/sidepanel.js)

### タスクとログの状態管理

tracker-store.jsとroom-log-store.jsがchrome.storage.local等へ記録し、一覧や除外状態を管理する。

- 自動抽出だけで全タスクを確定するのではなく、AIによる監査結果を受ける経路もある。

根拠：[tracker-store.js](https://github.com/gurii-gabreh/claude-voice-bridge/blob/f6e832014f183fa6890008cfab0d9969b2484c61/tracker-store.js)、[room-log-store.js](https://github.com/gurii-gabreh/claude-voice-bridge/blob/f6e832014f183fa6890008cfab0d9969b2484c61/room-log-store.js)、[sidepanel.js](https://github.com/gurii-gabreh/claude-voice-bridge/blob/f6e832014f183fa6890008cfab0d9969b2484c61/sidepanel.js)

### GASのGitHub書込み中継

doPostが各actionを受け、Script PropertiesのGitHubトークンでContents APIを更新する。ログはidで重複を扱い、競合時は再試行する。

- GASのデプロイ・Script Propertiesは別途設定が必要。今回は実送信していない。

根拠：[gas/code.gs](https://github.com/gurii-gabreh/claude-voice-bridge/blob/f6e832014f183fa6890008cfab0d9969b2484c61/gas/code.gs)、[gas/README.md](https://github.com/gurii-gabreh/claude-voice-bridge/blob/f6e832014f183fa6890008cfab0d9969b2484c61/gas/README.md)

### GitHubのJSON

data配下を共有データとして読み、GASまたはClaude Codeによる更新を受ける。ジャーナルの生トランスクリプトはmirai-journalの分析済みJSONとは別。


根拠：[sidepanel.js](https://github.com/gurii-gabreh/claude-voice-bridge/blob/f6e832014f183fa6890008cfab0d9969b2484c61/sidepanel.js)、[gas/code.gs](https://github.com/gurii-gabreh/claude-voice-bridge/blob/f6e832014f183fa6890008cfab0d9969b2484c61/gas/code.gs)、[widget-journal.js](https://github.com/gurii-gabreh/claude-voice-bridge/blob/f6e832014f183fa6890008cfab0d9969b2484c61/widget-journal.js)

### 拡張なしのWebビューア

webapp/index.htmlがトラッカー・定型文・ジャーナル等を表示する。設計はprogress-tracker-dashboardのrequirements.jsonを参照する。

- Web版にはchrome.tabsによるAI画面操作をそのまま実装できない。音声ブリッジ本体と役割が異なる。

根拠：[webapp/index.html](https://github.com/gurii-gabreh/claude-voice-bridge/blob/f6e832014f183fa6890008cfab0d9969b2484c61/webapp/index.html)、[widget-design.js](https://github.com/gurii-gabreh/claude-voice-bridge/blob/f6e832014f183fa6890008cfab0d9969b2484c61/widget-design.js)、[.github/workflows/deploy-pages.yml](https://github.com/gurii-gabreh/claude-voice-bridge/blob/f6e832014f183fa6890008cfab0d9969b2484c61/.github/workflows/deploy-pages.yml)

## 確認範囲

- 確認日：2026-10-17 JST
- 対象ブランチ：`main`
- 対象コミット：[f6e832014f183fa6890008cfab0d9969b2484c61](https://github.com/gurii-gabreh/claude-voice-bridge/tree/f6e832014f183fa6890008cfab0d9969b2484c61)
- コード・文書から作成。外部サービスへの書込み、アプリの起動、稼働環境の認証・データ確認は行っていません。
- Canvasは単体で読めるテキストカードです。Vaultへ配置して開いてください。接続先・ID・カード重複は生成スクリプトで検証済み。Obsidian実アプリでの表示確認は未実施です。
- 再生成は個人スキル `obsidian-app-mindmap` の `scripts/build_canvas.py` にJSONと出力先を渡します。
- JPGの作業は含みません。
