# Memory Bank Bootstrap — モデル非依存・初回導入指示書

> **使い方：** このMarkdownを、記憶管理を導入したい作業フォルダを開いたAIエージェントに**初回だけ**渡し、「この指示書を実行し、現在の環境にMemory Bankをセットアップしてください」と依頼する。
>
> **目的：** 以後、Codex・Claude Code・Antigravity・Cursor・Cline・その他のAIが、同じ作業フォルダ内の同じ記憶を参照・更新できるようにする。専用アプリ・GitHub連携・外部サーバーの導入は必須ではない。
>
> **位置付け：** Cline Memory Bankの6ファイル構成・更新思想を参考にした、クロスエージェント向けの独立した初期設定指示書。Cline公式の原文をそのまま再配布するものではない。

---

## 0. AIエージェントへの実行指示

あなたはこの文書の**説明や要約だけをするのではなく、可能な範囲でセットアップを実行する**担当者です。

1. **現在の作業フォルダを対象**にする。明示的に要求されない限り、ホームディレクトリ全体や全プロジェクト共通の設定は変更しない。
2. 使用中の**モデル名**ではなく、実際の**エージェント／IDE／実行環境**（Codex、Claude Code、Antigravity、Cursor、Cline、Copilot等）と、その永続指示の読み込み仕様を確認する。
3. 指示ファイル、Memory Bank、既存の設定を調査する。無断で上書き・削除・置換しない。
4. 利用可能なファイル操作ツールで、以下の手順を**その場で実行**する。ユーザーに手作業を求めるのは、アクセス権や製品仕様により実行できない操作だけにする。
5. GitHub・API・MCP・パッケージのインストール・外部サービスへのアップロードは不要。勝手に実施しない。
6. 必要な承認や実行権限がない場合は、**実行済みと偽らず**、未実施箇所と最小限の操作を示す。
7. このブートストラップは**再実行しても重複設定を増やさない（冪等）**ように扱う。

**基本原則：`_MemoryBank/` が記憶内容の唯一の共通保管先。各AI固有の指示ファイルは、そこへの短い「入口」にする。**

## 1. 環境の調査

読み取れる範囲で、次を調べる。

- 現在開いているワークスペース・リポジトリのルート。
- `AGENTS.md`、`AGENTS.override.md`、`CLAUDE.md`、`GEMINI.md`、`.clinerules/`、`.claude/`、`.agents/`、`.cursor/`、`.github/copilot-instructions.md`、そのほか既存の指示書。
- `_MemoryBank/`と旧名`memory-bank/`の有無、既存の記憶や進捗資料、README、ソースコード、実験・業務の説明資料。旧名がある場合は、入口の参照変更や新規作成の前に3.4の移行方針を確認する。
- 自分がファイルを作成・編集できるか。パスの解釈、ワークスペース外アクセス、実行承認に制限があるか。
- 現在のエージェントが**どの永続指示ファイルを本当に自動検出するか**。未知の製品では名前だけで推測せず、利用可能な公式資料・設定・既存例で確かめる。

エージェント名から使用モデルを推測してはいけない。例えば、Claudeモデルを使っていても、エージェントがClineならClineの仕様に従う。

## 2. プロジェクト内に共通の入口を設置

### 2.1 原則：`AGENTS.md`を優先

ワークスペースのルートの`AGENTS.md`を、共通の記憶運用ルールの入口とする。

- 存在しなければ作成する。
- 存在する場合は**既存ルールを維持**し、後述の「共通運用ブロック」を**1回だけ**追加・統合する。重複や矛盾を増やさない。
- `AGENTS.override.md`や上位階層の指示など、より優先順位の高い既存設定がある場合は確認する。`AGENTS.md`に書くだけでは有効にならないときは、その実行環境で**既に使われている指示の入口**へ同じ案内を追加する。
- 独自形式のルールとAGENTS.mdが両方読み込まれる環境では、同じ全文を二重に配置しない。

**共通運用ブロック（`AGENTS.md`へ追加・統合する内容）：**

<!-- BEGIN SHARED MEMORY BANK RUNTIME -->

### Shared Memory Bank — cross-agent project memory

- This workspace uses `_MemoryBank/` as the **shared, model-independent project memory**. Use this exact directory name at the workspace root for setup and all routine reads and updates. These Markdown files, not any provider's private memory, are the source of handoff context across agents.
- At the beginning of each new session or substantial task, read `_MemoryBank/activeContext.md` and `_MemoryBank/progress.md`; then read `_MemoryBank/projectBrief.md`, `productContext.md`, `systemPatterns.md`, `techContext.md`, and any relevant supplementary files as needed. On a brand-new or small Memory Bank, read all six core files.
- For the complete maintenance rules, read `_MemoryBank/PROTOCOL.md` before modifying the Memory Bank. Initialize missing files using that protocol if this is a new workspace.
- Treat source code, real documents, tests, and explicit user corrections as stronger evidence than stale memory. Separate verified facts, hypotheses, and pending decisions.
- During work, record significant decisions, reusable solutions, important constraints, resource locations, and progress changes in the appropriate Memory Bank file. Before finishing a substantial task, update `activeContext.md` (current state and next steps) and `progress.md` (completed, pending, and blocked items). Do not produce meaningless updates for every tiny chat turn.
- Preserve information needed by the next AI agent to continue the work without retelling its background. Keep entries concise, dated when relevant, and non-contradictory. Do not store transcripts wholesale.
- Do not create independent provider-specific copies of project memory. If a provider has native auto-memory, use it only as a supplement; important cross-agent facts must be present in `_MemoryBank/`.
- Do not store secrets, authentication tokens, recovery phrases, identifiable research-participant data, or other unnecessary sensitive information. Never publish or push memories automatically.
- If a change is risky, potentially destructive, or affects an existing user-defined rule, request the required approval. Do not overwrite existing instructions silently.

<!-- END SHARED MEMORY BANK RUNTIME -->

### 2.2 エージェント固有の入口を必要に応じて接続

**現在使っているAIと、実際に検出できるAIだけ**を設定する。未使用AIのために不要なファイルを乱立させない。

- **Codex**：基本はルートの`AGENTS.md`。既存の`AGENTS.override.md`等が優先されている場合は、その設定が共通ブロックを無効化していないことを確認する。
- **Claude Code**：現行版は条件付きで`AGENTS.md`を直接利用できる。既存の`CLAUDE.md`がある、または読み込みに不確実性があるなら、`CLAUDE.md`に`@AGENTS.md`というインポート行を1回だけ追加する。既存の`CLAUDE.md`のルールは維持する。必要であれば新規`CLAUDE.md`を`@AGENTS.md`のみで作成する。
- **Google Antigravity**：ワークスペースの`AGENTS.md`を認識するので、通常は`GEMINI.md`を追加作成しない。既存の`GEMINI.md`や`.agents/rules/`等とルールが衝突・重複する場合だけ、それらを確認して適切に接続する。
- **Cursor / Cline / GitHub Copilot**：`AGENTS.md`を認識する対応バージョン・実行形態では追加アダプターを作らない。既存の`.cursor/rules/`、`.clinerules/`、`.github/copilot-instructions.md`などが優先される／`AGENTS.md`を読まないときだけ、**そのAIが普段最初に参照する指示書**に「`AGENTS.md`と`_MemoryBank/`を読み、共通運用に従う」という短い案内を追加する。
- **その他のエージェント**：製品名のハードコードに頼らない。①自動読み込みファイルの仕様を検出、②既存指示を保持、③共通運用へ導く最小限の記述を追加、④新しいセッションで再読込を検証、という手順で接続する。
- **チャットWeb版など、ファイルに継続アクセスできない環境**：永続的なプロジェクト指示・ナレッジ機能が利用できればそこへ共通指示と記憶を登録する。AIに設定変更権限がなければ、貼り付けるべき文章・アップロードすべき資料を生成して案内する。**一度のチャットメッセージだけで設定が永続化されたと主張しない。**

> 注意：`AGENTS.md`は広く採用された共通形式だが、全AIが必ず同じ優先順位・読み込み範囲で扱うことを保証しない。ネイティブ仕様を優先し、不明ならテストして確認する。

## 3. Memory Bankの生成・既存データの統合

### 3.1 保管先とファイル構造

ワークスペースのルートに`_MemoryBank/`を作成し、**Cline Memory Bankの基本6ファイル**を整備する。大文字・小文字も含めてこの名前に統一し、初回導入後の参照・更新にも同じフォルダを使う。元から存在するファイルは消さず、内容を確認して更新する。旧名のフォルダがある場合は3.4に従い、空の記憶を別に作らない。

```text
workspace/
├── AGENTS.md                 # 共通エントリーポイント（既存内容は保持）
├── CLAUDE.md                 # 必要なときだけ作るアダプター
├── _MemoryBank/
│   ├── PROTOCOL.md           # 継続運用ルール
│   ├── projectBrief.md       # 目的・範囲・成功条件
│   ├── productContext.md     # 背景・利用者・期待する体験
│   ├── activeContext.md      # 現状・直近の変化・次の一手
│   ├── systemPatterns.md     # 構成・判断理由・作業方法
│   ├── techContext.md        # 環境・制約・ツール・資料の所在
│   └── progress.md           # 完了・未完了・問題・検証結果
└── ...                       # 既存の実プロジェクト資料
```

研究・文章執筆・個人プロジェクトなど非プログラミング用途でも、ファイル名は統一したまま意味を読み替える。例えば`systemPatterns.md`には業務の進め方や判断基準、`techContext.md`には利用サービス・資料の所在・制約を記述する。

### 3.2 初期内容の生成

各ファイルは既存の作業フォルダ、現在の会話、および確認済みの資料に基づいて作成する。未確認事項は「未確認」と明示し、推測で埋めない。

- **projectBrief.md**：何のための作業か、目的、範囲、要件、完了条件。
- **productContext.md**：なぜ必要なのか、対象利用者、問題、理想の使用体験・成果。
- **activeContext.md**：現在の状態、直前に行ったこと、決まっていること、次の具体的な作業、保留・注意事項。**新しいAIが最初に見る引き継ぎメモ**として短く保つ。
- **systemPatterns.md**：構成、採用した設計、重要な判断と理由、よく使う手順、避けるべき失敗。
- **techContext.md**：OS、言語、実行環境、関連ツール、データの保存場所、アクセス条件、確認手順。PCごとの絶対パスは必要な場合のみ記録し、環境依存であることを明示する。
- **progress.md**：完了したこと、テスト結果、未着手事項、進行中の課題、ブロッカー。完了と予定を区別する。

### 3.3 `_MemoryBank/PROTOCOL.md`に必ず保存する運用規則

以下の内容を**独立した永続ファイルとして**記録する。初期指示書の再提示がなくても、これを読めば管理方法が分かる状態にする。既存のPROTOCOLがあれば併合して矛盾を解消する。

共通記憶の保管先はワークスペースルートの`_MemoryBank/`のみ。初期構築後もこの名前を使い、別名の記憶フォルダを新設しない。以下のファイル名はこのフォルダを基準とする。

**記憶の参照：**
1. 新規セッション・大きな作業の開始時に`activeContext.md`と`progress.md`を読む。
2. 目的・構成・環境などについて不足があれば残りの基本ファイルも確認する。初期の小さなMemory Bankなら6ファイルすべてを読む。
3. 記憶が古い場合は、実コード・原資料・検証結果で確認する。記憶だけを根拠に断定しない。

**記憶の更新：**
1. 重要な仕様・設計判断、検証済みの解決方法、失敗原因、資料の所在、制約の変更、作業の進捗・次の一手が生じたときに更新する。
2. ユーザーの明示的な修正・指摘は優先する。古い情報と矛盾したら、単なる追記で済ませず、現状の記述を更新する。過去の判断理由が重要なら別の履歴として残す。
3. 作業途中で重要な知識を得たら必要な時点で記録し、**重要なタスクの終了前**に`activeContext.md`と`progress.md`を点検する。
4. 状況の変化がない場合は無理に書き換えない。全文会話ログ・冗長な経過説明は保存しない。
5. 重要な事実については可能なら出典ファイル・検証方法・確認日を記す。確定事項と未確認事項を分離する。
6. 記憶ファイルは共有資産として読み書きする。特定AI独自の記憶ファイルへしか保存しない運用は禁止する。

**情報保護：**
- パスワード、秘密鍵、APIキー、リカバリーフレーズ、不要な個人情報、研究参加者を特定できる情報は保存しない。
- ユーザーから明示的な指示がない限り外部サーバーに送信・Push・公開しない。Gitの追跡対象になっている場合は公開範囲に注意を促す。
- 他のAIや利用者の作業と衝突する可能性がある場合は、最新版を読み直してから編集する。

**既存資料の保全：**
- 運用設定の統合で他の規則を消さない。重複する指示は統一し、仕様差がある場合は理由を説明する。
- 既存のAuto Memory、履歴、Skillsを削除・無効化しない。必要な内容を共通Memory Bankに反映するだけにする。ユーザーから求められた場合のみ無効化等を検討する。
- 実データやプログラムをMemory Bank構築のために変更しない。

### 3.4 旧名・既存Memory Bankの移行と再利用

既存の`_MemoryBank/`、旧名の`memory-bank/`、`.clinerules/memory-bank.md`などがある場合、その内容を尊重して統合する。プロジェクト固有の大切な情報を別方式へ置き換えない。

1. `_MemoryBank/`だけがある場合は、その記憶をそのまま使い、不足分だけを補う。
2. 旧名の`memory-bank/`だけがある場合は、既存内容を確認し、許可された範囲でフォルダを`_MemoryBank/`へ改名する。補足ファイルも含めて保持し、単にコピーして二つの運用先を残さない。改名前後でファイル一覧と内容が保たれたことを確認する。
3. 両方ある場合は、ファイルを比較して固有の情報を保ち、`_MemoryBank/`へ統合する。競合する記述や同名ファイルを無断で上書き・削除しない。判断や承認が必要なら、該当部分の移行を保留して確認する。統合完了までは完了扱いにしない。
4. 入口の共通ブロックは重複追加せず、既存ブロック内のパスを更新する。`AGENTS.md`、実際に使われるアダプター・ルール・Skills・スクリプト、`PROTOCOL.md`、記憶内の現行パス・相対リンクを調べ、参照・更新先を`_MemoryBank/`へ揃える。外部の出典URL、`.clinerules/memory-bank.md`というルールファイル名、履歴として残す旧名は機械的に置換しない。
5. 既存ルールが旧名を要求している場合は、その参照を変更できるか確認する。権限不足などで改名・参照更新ができなければ、旧名の記憶を保全して未完了箇所を報告する。空の`_MemoryBank/`で代用して成功としない。

日常運用では`AGENTS.md`から`_MemoryBank/PROTOCOL.md`と基本ファイルを参照する。再実行時も旧名のフォルダや別の記憶コピーを再作成せず、同じ`_MemoryBank/`を更新する。

参考元：Cline Memory Bank（コミュニティ公開ルール）
https://github.com/cline/clinerules/blob/main/.clinerules/memory-bank.md

外部へ接続できなくても、この文書だけで基本6ファイルと運用方法を構築できるようにする。原文の再取得は必須ではない。

## 4. Skillsについての将来拡張（初期必須ではない）

- 既に`SKILL.md`形式のSkillsがある場合、利用可能なエージェントではそれを活用する。無関係なSkillsをコピーしない。
- 複数回再利用できる手順が見つかったら、独立Skill化を検討する。必要な場合の共通候補場所は`.agents/skills/<skill-name>/SKILL.md`。
- そのエージェントがその場所を自動認識するか検証する。認識しないAIには、適切な入口から共通Skillへ誘導する。**AIごとに内容の異なるSkillを複製しない**。
- 使い捨てのノートを大量にSkill化しない。初期構築段階では空のSkillsフォルダさえ必須ではない。

## 5. 安全な初期構築と差分確認

- 既存ファイルに対しては**非破壊的な追記・最小限の統合**を行う。
- `AGENTS.md`にすでに`<!-- BEGIN SHARED MEMORY BANK RUNTIME -->`という識別子がある場合は、新しいブロックを重ねず、既存内容を検証する。
- `CLAUDE.md`などのアダプターも、同じ参照行を重複追加しない。
- 既存のMemory Bankを初期化し直してはいけない。構造に不足がある場合だけ補う。
- Git・クラウドへの公開設定は勝手に変更しない。公開リポジトリの可能性があるときは、個人情報を記録せず、必要なら`_MemoryBank/`の追跡を除外する方法を提案する。
- フォルダの権限外操作・機密性の高い変更・ユーザーの既存運用を壊す恐れのある変更は、理由を示して確認する。

## 6. セットアップ結果の検証（必須）

次をできる限り確認し、実測した結果として報告する。

1. `AGENTS.md`（または当該AIが実際に読める入口）に共通運用の案内が一つだけある。
2. 当該AIの既存指示が失われていない。
3. `_MemoryBank/PROTOCOL.md`と基本6ファイルが存在する。
   旧名から移行した場合は、既存内容の保持と、入口・運用規則・現行の参照先が`_MemoryBank/`へ揃っていることも確認する。旧名への未解決の運用参照があれば移行未完了とする。
4. `activeContext.md`と`progress.md`を読み、現在の状況・次にする作業を要約できる。
5. 使用中AIの新しいセッションでも永続指示が読み込まれる**見込み**を確認する。実際の新規セッションでの動作を試せなければ「未検証」と記載する。
6. 別のAIで同じフォルダを開けば共通の記憶を参照できる構成である。別AIを実際に起動できない場合は、動作保証せず、試験手順を示す。
7. 作成／変更したファイル名、適用したAIの入口、残課題だけを短く報告する。

**ユーザー向けの試験例**：
- AI Aに「このプロジェクトの現状と次のタスクを整理し、Memory Bankを更新して」と依頼する。
- AI Bで同じフォルダを開き、新規セッションで「プロジェクトの続きを把握して。まだ編集しないで」と依頼する。
- AI Bが直前の進捗と判断理由を、Memory Bankに基づいて説明できるか確認する。逆方向も試す。

## 7. 運用可能性の限界

このファイルは**ブートストラップ指示**であって、すべてのAIに同一のファイル操作権限や自動実行機能を付与するものではない。

- ファイル操作できるコーディングエージェントでは、セットアップを直接実行できる可能性が高い。
- 読み取り専用・Webチャットなどでは、生成したファイルの保存や永続指示の登録にユーザー操作が必要な場合がある。
- 指示ファイルを置いても、すべてのセッションで記憶の更新が必ず実行される保証はない。必要なら、将来Hooks・定期チェック・スクリプトなどを追加する。
- 自動引き継ぎの対象は**記録された重要な情報**であり、保存していない会話細部や内部推論を復元するものではない。

---

## 設計の根拠・確認先

- Cline Memory Bank: https://github.com/cline/clinerules/blob/main/.clinerules/memory-bank.md
- AGENTS.mdオープン形式: https://agents.md/
- CodexのAGENTS.md: https://learn.chatgpt.com/docs/agent-configuration/agents-md
- Claude Codeの記憶とプロジェクト指示: https://code.claude.com/docs/en/memory
- Google AntigravityのRules: https://antigravity.google/docs/rules/
- ClineのRules: https://docs.cline.bot/features/cline-rules

*この文書は、モデル共通のMemory Bank導入を目的とする独立した派生設計です。各製品の仕様変更時には設定方法を再確認してください。*
