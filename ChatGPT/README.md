# ChatGPTで作成したObsidian連関図

2026-10-17にgurii-gabrehのアクセス可能な全22リポジトリを確認しました。**19アプリを新規作成、2アプリは既存の図を使用、1リポジトリは図の保管庫**です。既存のCanvasは作り直していません。

各アプリのフォルダには、内部構成の説明、編集可能なCanvas、再生成用JSONを収録しています。CanvasをObsidianのVaultへ入れて開くと、拡大・縮小・移動・編集できます。GitHubのファイル画面は操作可能なCanvasビューアではありません。

## アプリ一覧

| アプリ | 状態 | 内部構成の説明 | 連関図 |
|---|---|---|---|
| AI Dev Logger | 新規・5項目／2経路 | [AI-Dev-Logger](AI-Dev-Logger/) | [Canvas](AI-Dev-Logger/system-map.canvas) |
| AI Oracle | 新規・4項目／2経路 | [AI-Oracle](AI-Oracle/) | [Canvas](AI-Oracle/system-map.canvas) |
| AI Research Radar | 新規・7項目／3経路 | [ai-research-radar](ai-research-radar/) | [Canvas](ai-research-radar/system-map.canvas) |
| AI Tool Manager | 新規・6項目／2経路 | [ai-tool-manager](ai-tool-manager/) | [Canvas](ai-tool-manager/system-map.canvas) |
| App Development Info | 新規・5項目／2経路 | [App-Development-Info](App-Development-Info/) | [Canvas](App-Development-Info/system-map.canvas) |
| Claude Voice Bridge | 新規・7項目／3経路 | [claude-voice-bridge](claude-voice-bridge/) | [Canvas](claude-voice-bridge/system-map.canvas) |
| Credit Radar | 新規・5項目／2経路 | [credit-radar](credit-radar/) | [Canvas](credit-radar/system-map.canvas) |
| English LevelUp Reader | 新規・5項目／2経路 | [English-LevelUp-Reader](English-LevelUp-Reader/) | [Canvas](English-LevelUp-Reader/system-map.canvas) |
| English Speaking Practice | 新規・6項目／2経路 | [English-Speaking-App](English-Speaking-App/) | [Canvas](English-Speaking-App/system-map.canvas) |
| Gemini Monitor | 新規・5項目／2経路 | [gemini-monitor](gemini-monitor/) | [Canvas](gemini-monitor/system-map.canvas) |
| 結（Yui／Kizashi） | 新規・7項目／3経路 | [Kizashi](Kizashi/) | [Canvas](Kizashi/system-map.canvas) |
| Knowledge Dashboard | 新規・4項目／2経路 | [Knowledge-Dashboard](Knowledge-Dashboard/) | [Canvas](Knowledge-Dashboard/system-map.canvas) |
| Mirai Journal | 新規・4項目／1経路 | [mirai-journal](mirai-journal/) | [Canvas](mirai-journal/system-map.canvas) |
| My Dashboard | 新規・4項目／2経路 | [my-dashboard](my-dashboard/) | [Canvas](my-dashboard/system-map.canvas) |
| Prompt Manager | 新規・5項目／2経路 | [PROMPT-MANAGER](PROMPT-MANAGER/) | [Canvas](PROMPT-MANAGER/system-map.canvas) |
| Study App | 新規・5項目／2経路 | [study-app](study-app/) | [Canvas](study-app/system-map.canvas) |
| Supermarket Price Tracker | 新規・6項目／3経路 | [supermarket-price-tracker](supermarket-price-tracker/) | [Canvas](supermarket-price-tracker/system-map.canvas) |
| Task WBS／Study Schedule | 新規・5項目／2経路 | [Task-WBS](Task-WBS/) | [Canvas](Task-WBS/system-map.canvas) |
| Text Extraction | 新規・5項目／2経路 | [Text-Extraction](Text-Extraction/) | [Canvas](Text-Extraction/system-map.canvas) |
| Progress Tracker Dashboard | 作成済みを使用 | [progress](progress/) | [仕様](progress/progress-tracker-final-spec.canvas)・[課題と関連](progress/progress-tracker-task-resolutions.canvas) |
| ServiceNow Research Viewer | 作成済みを使用 | [servicenow-sub-agent](servicenow-sub-agent/) | [Canvas](servicenow-sub-agent/servicenow-system-map.canvas) |

## 共通の見方

- 上側はカテゴリ別の役割と内部仕様、下側の「つながり」はデータ・結果の受け渡しです。
- 短い説明を先に読み、右側で具体的な処理・保存先・根拠ファイルを確認できます。
- 「実装あり」はコードを確認した意味で、稼働環境での動作確認済みという意味ではありません。
- 外部API・GAS・ローカルCLI等への実アクセス、アプリ起動、設定変更は行っていません。
- 複数画面・サイドパネル・Web版・補助ツールは、同じアプリ内の構成要素としてまとめています。
- AI Dev LoggerはZIP内部のソースを確認し、各カードにアーカイブ内のパスを明記しています。
- main以外が既定ブランチのアプリは、その既定ブランチのコミットを確認しました。
- 各図の根拠はコミット固定リンクです。モデル名・料金・公開状態等の現行条件を保証しません。
- アプリ本体や、以前作成したprogress・servicenow-sub-agentの図は変更していません。
- Obsidian-Appは図・スキルの保管リポジトリとして扱い、独立した実行アプリの図は作成していません。
- JPGの作業は停止したままで、今回の作業に含みません。

## 共通の学習用マップ

[app-architecture](app-architecture/)：初心者向けのアプリ作成の全体像。特定アプリの内部構成とは別に、設計・開発の検討事項をまとめたものです。

[調査対象・コミット・作成状況のデータ](catalog.json)

新規図の再生成は、個人スキル `obsidian-app-mindmap` の `scripts/build_canvas.py` へ各フォルダのsystem-map.data.jsonと出力先を渡して行えます。
