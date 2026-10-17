# English LevelUp Reader：システム内部の連関図

[Obsidian用の連関図](system-map.canvas) ／ [再生成用データ](system-map.data.json)

段階的な英語読解と読み上げを行う本体と、Whisper発音チェックの試作を分けたアプリ。

## 最初に見るポイント

本体の読解・TTSと、発音認識の検証ページは別。試作があることを本体完成とは扱わない。

## 内部の役割

| 部分 | 何をするか |
|---|---|
| 教材JSON | レベル・トピック・英文等を静的データで管理する。 |
| レベル別の読解画面 | 教材を選んで表示し、読み上げる。 |
| localStorageの進捗 | レッスンの完了状態を端末ごとに保存する。 |
| Whisper/WASM検証ページ | 録音した音声をブラウザ内モデルで認識する試作。 |
| モデル配信とブラウザ内推論 | ライブラリとモデルを取得し、端末内で認識する。 |

## データのつながり

### 本体で教材を読む

- 教材JSON → レベル別の読解画面：教材を読み込む
- レベル別の読解画面 → localStorageの進捗：完了状態を端末に保存

### 試作の発音チェック

- Whisper/WASM検証ページ → モデル配信とブラウザ内推論：ライブラリ・モデルを読み込む
- モデル配信とブラウザ内推論 → Whisper/WASM検証ページ：ブラウザ内で文字起こしして結果を表示

## 内部仕様と確認事項

### 教材JSON

app.jsがdata/lessons.jsonを取得し、レベル一覧と教材を描画する。

- READMEではレベル1の骨組みまで、2〜10の教材は準備中とされる。実教材の全件内容までは今回評価していない。

根拠：[js/app.js](https://github.com/gurii-gabreh/English-LevelUp-Reader/blob/31eb7971f0583586e53b3e19b0e34440112ebbd2/js/app.js)、[README.md](https://github.com/gurii-gabreh/English-LevelUp-Reader/blob/31eb7971f0583586e53b3e19b0e34440112ebbd2/README.md)

### レベル別の読解画面

index.htmlとapp.jsが画面切替、レッスン表示、SpeechSynthesisによる読み上げを行う。

- 今回の対象端末はREADME上PC専用。

根拠：[index.html](https://github.com/gurii-gabreh/English-LevelUp-Reader/blob/31eb7971f0583586e53b3e19b0e34440112ebbd2/index.html)、[js/app.js](https://github.com/gurii-gabreh/English-LevelUp-Reader/blob/31eb7971f0583586e53b3e19b0e34440112ebbd2/js/app.js)、[README.md](https://github.com/gurii-gabreh/English-LevelUp-Reader/blob/31eb7971f0583586e53b3e19b0e34440112ebbd2/README.md)

### localStorageの進捗

loadProgress／saveProgressがlocalStorageへ記録する。

- GitHubや他端末へ進捗を送信しない。

根拠：[js/app.js](https://github.com/gurii-gabreh/English-LevelUp-Reader/blob/31eb7971f0583586e53b3e19b0e34440112ebbd2/js/app.js)

### Whisper/WASM検証ページ

prototype/whisper-test.htmlがTransformers.jsを読込み、WhisperモデルとMediaRecorder等で認識・所要時間・単語一致率を確認する。

- 本体app.jsへの発音チェック統合は未完了。モデルとライブラリの初回取得は通信が必要で、精度・速度の実機確認待ち。

根拠：[prototype/whisper-test.html](https://github.com/gurii-gabreh/English-LevelUp-Reader/blob/31eb7971f0583586e53b3e19b0e34440112ebbd2/prototype/whisper-test.html)、[prototype/README.md](https://github.com/gurii-gabreh/English-LevelUp-Reader/blob/31eb7971f0583586e53b3e19b0e34440112ebbd2/prototype/README.md)

### モデル配信とブラウザ内推論

CDNのTransformers.jsとWhisper系モデルを試作で使用する。録音・波形変換・ASRパイプラインをつなぐ。

- クラウド音声APIへ録音を送る実装とは異なるが、配信元の取得可否や端末性能に依存する。

根拠：[prototype/whisper-test.html](https://github.com/gurii-gabreh/English-LevelUp-Reader/blob/31eb7971f0583586e53b3e19b0e34440112ebbd2/prototype/whisper-test.html)

## 確認範囲

- 確認日：2026-10-17 JST
- 対象ブランチ：`main`
- 対象コミット：[31eb7971f0583586e53b3e19b0e34440112ebbd2](https://github.com/gurii-gabreh/English-LevelUp-Reader/tree/31eb7971f0583586e53b3e19b0e34440112ebbd2)
- コード・文書から作成。外部サービスへの書込み、アプリの起動、稼働環境の認証・データ確認は行っていません。
- Canvasは単体で読めるテキストカードです。Vaultへ配置して開いてください。接続先・ID・カード重複は生成スクリプトで検証済み。Obsidian実アプリでの表示確認は未実施です。
- 再生成は個人スキル `obsidian-app-mindmap` の `scripts/build_canvas.py` にJSONと出力先を渡します。
- JPGの作業は含みません。
