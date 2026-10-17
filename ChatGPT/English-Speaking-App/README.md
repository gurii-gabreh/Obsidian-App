# English Speaking Practice：システム内部の連関図

[Obsidian用の連関図](system-map.canvas) ／ [再生成用データ](system-map.data.json)

自作の音声練習と、ChatGPTでのレッスン案内・レポート閲覧を同じ入口にまとめる。

## 最初に見るポイント

自作API連携と、ChatGPTへ手動で渡す学習経路を明確に分けている。

## 内部の役割

| 部分 | 何をするか |
|---|---|
| トップと2つの練習経路 | 自作アプリかChatGPTレッスン案内を選ぶ。 |
| 音声認識・読み上げ | ブラウザの音声機能で発話とお手本を扱う。 |
| Groq API | 対話・翻訳・発音やニュアンスの補助解析を行う。 |
| 端末内の履歴・設定 | 会話・練習・訂正・テンプレートを保存する。 |
| ChatGPT向けの案内 | プロンプトをコピーし、ChatGPTの画面で練習する。 |
| レポートJSONとビューア | 記録済みレポートを読み取り専用で見る。 |

## データのつながり

### 自作の音声練習

- トップと2つの練習経路 → 音声認識・読み上げ：app.htmlへ進む
- 音声認識・読み上げ → Groq API：認識テキストを対話・解析に使う
- Groq API → 端末内の履歴・設定：結果を端末内に保存

### ChatGPTでの練習記録

- トップと2つの練習経路 → ChatGPT向けの案内：案内ページを選ぶ
- ChatGPT向けの案内 → レポートJSONとビューア：外部で練習後、Claude Code経由で記録

## 内部仕様と確認事項

### トップと2つの練習経路

index.htmlからapp.htmlとchatgpt.htmlへ遷移する。lessons.htmlは別途レポート一覧を表示する。


根拠：[index.html](https://github.com/gurii-gabreh/English-Speaking-App/blob/de277a4c68ad1385b2676fb5b400a7f8ec117910/index.html)、[README.md](https://github.com/gurii-gabreh/English-Speaking-App/blob/de277a4c68ad1385b2676fb5b400a7f8ec117910/README.md)

### 音声認識・読み上げ

app.htmlがSpeechRecognition系APIとspeechSynthesisを使い、会話・反復練習・録音状態を制御する。単語一致等の簡易スコアも持つ。

- ブラウザ標準音声認識は端末・ブラウザにより対応や動作が異なる。完全オフラインとは断定しない。

根拠：[app.html](https://github.com/gurii-gabreh/English-Speaking-App/blob/de277a4c68ad1385b2676fb5b400a7f8ec117910/app.html)

### Groq API

ブラウザからchat/completionsへ利用者のAPIキーで送信する。発話補正・日本語入力の英訳等にも使用する。

- アプリにはAPIキー設定が必要。サービスの現行モデルや利用枠は未確認。

根拠：[app.html](https://github.com/gurii-gabreh/English-Speaking-App/blob/de277a4c68ad1385b2676fb5b400a7f8ec117910/app.html)

### 端末内の履歴・設定

localStorageにキー・履歴・テンプレート等を保存し、再表示やファイル出力を行う。

- この経路はGitHubへ自動同期しない。

根拠：[app.html](https://github.com/gurii-gabreh/English-Speaking-App/blob/de277a4c68ad1385b2676fb5b400a7f8ec117910/app.html)

### ChatGPT向けの案内

chatgpt.htmlにレッスンの指示文・進行コマンド・記録方法を掲載する。

- この案内ページがChatGPT APIを呼ぶわけではない。教材本体のアップロード等は利用者側の操作。

根拠：[chatgpt.html](https://github.com/gurii-gabreh/English-Speaking-App/blob/de277a4c68ad1385b2676fb5b400a7f8ec117910/chatgpt.html)、[README.md](https://github.com/gurii-gabreh/English-Speaking-App/blob/de277a4c68ad1385b2676fb5b400a7f8ec117910/README.md)

### レポートJSONとビューア

Claude Codeへレポートを渡してdata/lessons.jsonを更新する運用。lessons.htmlはJSONをfetchして表示する。

- サイトからレポートを保存・編集する機能ではない。

根拠：[lessons.html](https://github.com/gurii-gabreh/English-Speaking-App/blob/de277a4c68ad1385b2676fb5b400a7f8ec117910/lessons.html)、[README.md](https://github.com/gurii-gabreh/English-Speaking-App/blob/de277a4c68ad1385b2676fb5b400a7f8ec117910/README.md)

## 確認範囲

- 確認日：2026-10-17 JST
- 対象ブランチ：`main`
- 対象コミット：[de277a4c68ad1385b2676fb5b400a7f8ec117910](https://github.com/gurii-gabreh/English-Speaking-App/tree/de277a4c68ad1385b2676fb5b400a7f8ec117910)
- コード・文書から作成。外部サービスへの書込み、アプリの起動、稼働環境の認証・データ確認は行っていません。
- Canvasは単体で読めるテキストカードです。Vaultへ配置して開いてください。接続先・ID・カード重複は生成スクリプトで検証済み。Obsidian実アプリでの表示確認は未実施です。
- 再生成は個人スキル `obsidian-app-mindmap` の `scripts/build_canvas.py` にJSONと出力先を渡します。
- JPGの作業は含みません。
