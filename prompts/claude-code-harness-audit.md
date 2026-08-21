Note: This is a point-in-time personal workflow prompt based on Claude Code capabilities and official guidance available as of 2026-08-21. It reflects personal operational preferences and is not an official Anthropic recommendation. Review current Claude Code documentation before applying it.

---

私が利用している Claude Code の **Personal Harness** を調査し、複数のリポジトリ・言語・技術スタックで汎用的に利用できる形へ改善してください。

対象は特定の repository の `.claude/` ではなく、主として以下のユーザースコープ設定です。

* `~/.claude/CLAUDE.md`
* `~/.claude/rules/`
* `~/.claude/skills/`
* `~/.claude/agents/`
* ユーザー単位の Claude Code settings / hooks
* その他、全プロジェクトで利用される Claude Code 設定

目的は、

**どの repository でも「正しく・速く・tokenを無駄遣いせず」Claude Codeを利用できる汎用Harnessを構築すること**

です。

## Scope の原則

Personal Harness には、特定 repository や技術スタックに依存しない原則だけを置いてください。

例えば以下は Personal Harness の対象です。

* Model / effort の選択方針
* 高性能モデルへのタスク委譲方針
* Context Hygiene
* `/compact` / `/clear` / fresh session の判断
* Sub agent の利用判断
* 並列 Sub agent の判断
* Plan / Implement / Review の分離
* Plan Review
* Fresh Context Review
* 観点あり / 観点なしレビュー
* Harness 自体の継続改善
* 公式 Claude Code 情報の確認方法
* 不要な探索や過剰な tool call を避ける原則

一方、以下は原則として Personal Harness に入れないでください。

* Rails 固有ルール
* Vue 固有ルール
* RSpec / Vitest 固有ルール
* repository 固有ディレクトリ構成
* repository 固有 test command
* Jira ticket の運用
* 特定チームの Git convention
* 特定ドメインの設計ルール

これらは必要に応じて各 repository の、

* `CLAUDE.md`
* `.claude/rules/`
* `.claude/skills/`
* `.claude/agents/`

に置くものとして扱ってください。

## 最重要設計方針

Personal Harness は全セッションに影響するため、

**常時contextに載せる情報を最小化すること**

を非常に重視してください。

`~/.claude/CLAUDE.md` には、

* 短い
* 汎用的
* 毎回必要
* 長期間変わりにくい

原則だけを残してください。

複数ステップの手順や特定タスク時だけ必要な指示は、可能な限り Personal Skill に移してください。

独立contextで実行する価値がある専門作業は Personal Subagent にしてください。

「便利そうだからCLAUDE.mdに追加する」という判断は禁止してください。

---

## Audit

まず現在の `~/.claude/` を調査してください。

この段階では変更しないでください。

以下を確認してください。

* `~/.claude/CLAUDE.md`
* `~/.claude/rules/`
* `~/.claude/skills/`
* `~/.claude/agents/`
* settings
* hooks
* その他 Claude Code のPersonal設定

各項目を次の観点で評価してください。

1. 全repositoryで本当に有効か
2. 毎session contextへ入れる必要があるか
3. Skillへ移せないか
4. Subagentへ移せないか
5. Hookで機械的に処理すべきか
6. repository側へ移すべき内容ではないか
7. 現在のClaude Codeならモデル自身に任せられないか
8. 不要なtoken / tool callを増やしていないか
9. 古くなったClaude Code前提を含んでいないか
10. 重複・矛盾・過剰な指示がないか

---

## Personal CLAUDE.md に残す原則

以下のような内容を候補として評価してください。

### Task proportionality

タスクの複雑さに応じて使用する推論量・Plan・Subagentを変える。

小さく明確な作業では過剰な探索・Plan・Subagentを避ける。

### High-performance model autonomy

高性能モデルには、

* Outcome
* Constraints
* Done criteria

を中心に与える。

必要ならモデル自身に、

* Plan
* Subagent
* 並列探索
* 検証方法

を選ばせる。

人間がオーケストレーションを過剰指定しない。

### Context Hygiene

Context は使用率ではなく情報密度で管理する。

不要な、

* 古い前提
* 却下案
* 解決済みエラー
* 大量tool output
* 関係ない探索結果

が増えたら整理する。

### Compact / Clear

`/compact` は容量閾値ではなくフェーズ境界を重視する。

タスク変更や独立レビューでは fresh session / `/clear` を優先する。

### Subagent

広く独立した探索をsubagentへ委譲する。

局所的・設計判断に直結する情報はmainが直接確認する。

### Parallel research

独立した複数領域・複数repositoryの調査は、効果がある場合だけ並列subagent化する。

### Independent review

複雑なPlanやImplementationは、必要に応じてfresh contextの別sessionでレビューする。

観点ありレビューと観点なしレビューを使い分ける。

---

## Personal Skills 候補

以下をSkill化する価値があるか評価してください。

* `plan-review`
* `implementation-review`
* `architecture-review`
* `cross-repo-research`
* `harness-maintenance`
* `official-docs-check`
* `context-cleanup`
* `test-strategy-review`

Skillは「常時必要ではないが、繰り返し利用する手順」を優先してください。

似たSkillを大量に作らず、統合できるものは統合してください。

---

## Personal Subagents 候補

Subagentは本当にcontext分離する価値がある場合だけ作成してください。

候補:

### researcher

広いコードベース調査・複数ファイル探索。

重要事項には根拠ファイルを付ける。

### reviewer

Plan / diff / architectureを独立contextからレビューする。

### cross-repo-researcher

複数repositoryを同じフォーマットで調査し比較可能な結果を返す。

ただし、高性能モデルが通常の汎用Subagentで十分に処理できる場合は、専用Subagentを増やさないでください。

---

## Claude Code公式情報との照合

現在のClaude Code公式ドキュメントとCHANGELOGを確認してください。

特に以下を確認してください。

* Personal CLAUDE.md
* user-level rules
* Personal Skills
* Personal Subagents
* Model / effort
* Plan mode
* Context management
* Prompt caching
* Subagents
* Hooks
* Costs

現在のHarnessとの差分だけを抽出してください。

最新仕様だからという理由だけで変更しないでください。

---

## Proposal

改善案を、

* KEEP
* MODIFY
* MOVE TO PERSONAL SKILL
* MOVE TO PERSONAL AGENT
* MOVE TO PROJECT SCOPE
* REMOVE
* ADD

に分類してください。

それぞれについて、

* 現在の状態
* 問題
* 提案
* 適用scope
* correctnessへの効果
* speedへの効果
* token/contextへの効果
* 根拠
* リスク

を説明してください。

この段階ではまだ変更しないでください。

---

## Review

提案を自己レビューしてください。

特に、

* Personal CLAUDE.mdを肥大化させていないか
* 全repositoryへ適用すると有害なルールがないか
* repository固有ルールをPersonalへ持ち込んでいないか
* Skill / Agentを増やしすぎていないか
* 高性能モデルの自律性を奪っていないか
* Context Hygieneのための仕組み自体がcontextを増やしていないか

を確認してください。

---

## Implementation

価値が明確な改善だけを実施してください。

変更は可能な限り小さくしてください。

既存のPersonal Harnessに有効な部分がある場合は維持してください。

---

## Verification

最低でも以下の性質が異なるタスクを想定して、Personal Harnessが汎用的に機能するか評価してください。

1. 小さなbug fix
2. フロントエンドテスト基盤導入
3. 複雑な設計変更
4. 開発ルールの妥当性検証
5. 複数repository横断調査
6. 大規模実装後のレビュー

特定言語・frameworkへの依存がないか確認してください。

---

## 最終原則

Personal Harness の目的は、

**Claude Codeへ大量のルールを与えることではありません。**

目標は、

> どのrepositoryでも、必要最小限の普遍的な原則だけでClaude Codeが適切なModel・Context・Tool・Subagent・Review戦略を選択できる状態を作ること

です。

Harnessを強化するときは、

**追加より削除、常時読込よりオンデマンド、手順固定より適切な自律判断**

を優先してください。
