# Study App：システム内部の連関図

[Obsidian用の連関図](system-map.canvas) ／ [再生成用データ](system-map.data.json)

教材・クイズ・復習・メモを表示し、学習履歴と途中経過をGitHub JSONへ保存する。

## 最初に見るポイント

端末内キャッシュとGitHubの共有JSONを併用し、GASが書込みを中継する。

## 内部の役割

| 部分 | 何をするか |
|---|---|
| 教材・クイズ画面 | カテゴリ別の教材、複数選択クイズ、苦手問題、履歴を表示する。 |
| GitHubの3つのJSON | 教材・メモ、履歴、途中経過を分けて管理する。 |
| ブラウザのキャッシュ | 端末上の状態を保持し、取得失敗時にも使う。 |
| GASの書込み中継 | 画面から受けた履歴・メモ・途中経過をGitHubへ書く。 |
| GitHub認証の管理 | GitHubトークンをGASのプロパティから取り出す。 |

## データのつながり

### 教材と状態を読む

- GitHubの3つのJSON → 教材・クイズ画面：問題と履歴を取得
- 教材・クイズ画面 → ブラウザのキャッシュ：端末内へ状態をキャッシュ

### 学習結果を共有保存

- 教材・クイズ画面 → GASの書込み中継：保存操作でPOST
- GASの書込み中継 → GitHubの3つのJSON：トークンを使いJSONを更新

## 内部仕様と確認事項

### 教材・クイズ画面

index.htmlが教材表示・採点・誤答集計・メモ・ランダム出題をまとめて処理する。旧all_lessons_data.htmlも別ファイルとして残る。


根拠：[index.html](https://github.com/gurii-gabreh/study-app/blob/bce3874eb42d035aaa49f2a9264c3ea7a6ebd004/index.html)

### GitHubの3つのJSON

data/lessons.json、history.json、progress.jsonを起動時に取得する。教材の問題形式をnormalizeQuizで統一する。

- 画面が取得する公開先とGitHubの更新タイミングには差が生じうる。

根拠：[index.html](https://github.com/gurii-gabreh/study-app/blob/bce3874eb42d035aaa49f2a9264c3ea7a6ebd004/index.html)、[gas/code.gs](https://github.com/gurii-gabreh/study-app/blob/bce3874eb42d035aaa49f2a9264c3ea7a6ebd004/gas/code.gs)

### ブラウザのキャッシュ

localStorageへクイズ状態や誤答集合等を保存する。JSON読込後はローカルのメモ編集を優先する。

- localStorageだけでは他端末に共有されない。

根拠：[index.html](https://github.com/gurii-gabreh/study-app/blob/bce3874eb42d035aaa49f2a9264c3ea7a6ebd004/index.html)

### GASの書込み中継

doPostのsaveHistory／saveMemo／saveProgressがGitHub Contents APIを使う。履歴はidで再送重複を防ぎ、競合時の再試行を行う。

- 回答ごとに自動commitする構成ではなく、保存操作に応じて同期する。

根拠：[gas/code.gs](https://github.com/gurii-gabreh/study-app/blob/bce3874eb42d035aaa49f2a9264c3ea7a6ebd004/gas/code.gs)、[index.html](https://github.com/gurii-gabreh/study-app/blob/bce3874eb42d035aaa49f2a9264c3ea7a6ebd004/index.html)

### GitHub認証の管理

GITHUB_TOKEN、REPO_OWNER、REPO_NAMEをスクリプトプロパティに設定し、GAS側でGitHub APIへ送る。

- GASの実デプロイと権限、利用者ごとの認証は未検証。

根拠：[gas/code.gs](https://github.com/gurii-gabreh/study-app/blob/bce3874eb42d035aaa49f2a9264c3ea7a6ebd004/gas/code.gs)、[gas/README.md](https://github.com/gurii-gabreh/study-app/blob/bce3874eb42d035aaa49f2a9264c3ea7a6ebd004/gas/README.md)

## 確認範囲

- 確認日：2026-10-17 JST
- 対象ブランチ：`main`
- 対象コミット：[bce3874eb42d035aaa49f2a9264c3ea7a6ebd004](https://github.com/gurii-gabreh/study-app/tree/bce3874eb42d035aaa49f2a9264c3ea7a6ebd004)
- コード・文書から作成。外部サービスへの書込み、アプリの起動、稼働環境の認証・データ確認は行っていません。
- Canvasは単体で読めるテキストカードです。Vaultへ配置して開いてください。接続先・ID・カード重複は生成スクリプトで検証済み。Obsidian実アプリでの表示確認は未実施です。
- 再生成は個人スキル `obsidian-app-mindmap` の `scripts/build_canvas.py` にJSONと出力先を渡します。
- JPGの作業は含みません。
