Note: This is a point-in-time setup prompt based on Claude Code capabilities available as of 2026-10-01. It requires Claude Code 2.1.94 or later, which added `sessionTitle` to the `UserPromptSubmit` hook output. Paste the part below the separator into Claude Code to install the `session-name` skill and its hook in your user scope. Review current Claude Code documentation before applying it.

---

Claude Code のセッションに `{リポジトリ名}-{PR番号 or チケット番号}-{簡単な説明}` 形式の名前を付けるグローバルスキル `session-name` と、名前を実際に適用する UserPromptSubmit hook を、この環境に入れてください。

## 仕組み

- モデルは `/rename` を実行できない。そこでスキルが名前を組み立て、`~/.claude/state/session-name/<session_id>` に予約として書く
- ユーザーが次にプロンプトを送ると、UserPromptSubmit hook が予約を読み、`hookSpecificOutput.sessionTitle` で名前を設定して予約を消す(公式ドキュメント上、効果は `/rename` と同じ)

## 先に確認すること(満たさなければ何も変更せずに止めて報告する)

1. `claude --version` が 2.1.94 以上であること(UserPromptSubmit hook の `sessionTitle` はこの版で入った)
2. `jq` が使えること(hook が使う)。無ければインストール方法を案内して止める
3. `gh` は任意。無い・未認証なら PR 番号は取れず、チケット番号だけで名前を作る。その旨を報告に書く
4. `~/.claude/skills/session-name/` が無いこと。既にあれば中身を見せ、上書きしてよいか聞く
5. `~/.claude/settings.json` の `hooks.UserPromptSubmit` に、下の command がまだ登録されていないこと

## 手順

1. 下の4ファイルを `~/.claude/skills/session-name/` に、内容を一切変えずに作る。`scripts/*.sh` には `chmod +x` する
2. `~/.claude/settings.json` を `~/.claude/backups/session-name-hook-<日時>/` にコピーしてから、jq で `hooks.UserPromptSubmit` の配列に次の1要素を**追加**する。既存の hook やほかの設定は消さない。settings.json が無ければ `{}` から作る

   ```json
   {"hooks": [{"type": "command", "command": "~/.claude/skills/session-name/scripts/apply_pending_name.sh", "timeout": 5}]}
   ```

   書き込む前に、`hooks.UserPromptSubmit` 以外が変わっていないことを `jq -S 'del(.hooks.UserPromptSubmit)'` の diff で確かめる
3. 動作を確認する
   - git リポジトリの中で `~/.claude/skills/session-name/scripts/session_name_parts.sh` を実行し、`repo` `branch` `pr` `ticket` `pr_title` の5行が出る
   - `echo '{"session_id":"test-x"}' | ~/.claude/skills/session-name/scripts/apply_pending_name.sh` が何も出さずに exit 0 で終わる
   - `~/.claude/skills/session-name/scripts/set_pending_name.sh test-x demo-name` のあとに同じ入力で apply を実行すると `"sessionTitle": "demo-name"` を含む JSON が出て、`~/.claude/state/session-name/test-x` が消えている
4. 作ったファイル、バックアップの場所、確認の結果を報告し、使い方を伝える
   - `/session-name` を実行すると名前が予約され、次のプロンプトを送ったときにセッション名が変わる
   - 説明部分を指定したいときは `/session-name auth refactor` のように引数を渡す
   - 名前が変わらなければ Claude Code を再起動して、もう一度試す

## 作るファイル

### `~/.claude/skills/session-name/SKILL.md`

~~~~~
---
name: session-name
description: >-
  セッション名を `{リポジトリ名}-{PR番号 or チケット番号}-{簡単な説明}` の形で作って付ける。
  「セッション名を付けて」「rename の名前を考えて」「/resume で探せる名前にしたい」等のときに使う。
argument-hint: "[説明部分の指定]"
allowed-tools:
  - Bash(${CLAUDE_SKILL_DIR}/scripts/session_name_parts.sh)
  - Bash(${CLAUDE_SKILL_DIR}/scripts/set_pending_name.sh *)
---

# セッション名を作る

**モデルは `/rename` を実行できない。** そのかわり、名前を予約しておくと、ユーザーが次に
プロンプトを送ったときに UserPromptSubmit hook(`scripts/apply_pending_name.sh`)が
`sessionTitle` として設定する。hook は `~/.claude/settings.json` に登録してある。

## 機械的に決まる部分

!`${CLAUDE_SKILL_DIR}/scripts/session_name_parts.sh`

上が空なら、Bash で `${CLAUDE_SKILL_DIR}/scripts/session_name_parts.sh` を実行する。

## 組み立て方

形は `{repo}-{識別子}-{説明}`。

- **repo**: スクリプトの `repo` をそのまま使う
- **識別子**: 会話で扱っている PR・チケットを最優先する。master 上で他人の PR をレビューして
  いるときなど、ブランチと会話の主題が違う場合は会話のほうを使う。会話から決まらなければ
  スクリプトの `pr`(`pr123` の形)→ `ticket`(`PROJ-123` の形、大文字のまま)の順。
  どちらも無ければ識別子を省いて `{repo}-{説明}` にする。**セッションの作業がブランチの
  PR・チケットと無関係なら**(たまたまそのブランチにいるだけで、グローバル設定やスキルを
  触っているなど)、スクリプトの値は使わず識別子を省く
- **説明**: 英小文字・数字・`-` で 2〜4 語。最初のプロンプトではなく、**このセッションで
  主にやっている作業**を表す。repo 名や識別子と同じ語を繰り返さない。会話がほぼ空のときは
  `pr_title` → `branch` の順で材料にする
- 次の行に引数が展開される。中身があれば説明部分にそれを使う(kebab-case へ直すだけ)。
  空、または `$` で始まる未置換のままなら無視する

  引数: $ARGUMENTS
- 全体で 50 文字程度に収める

例: `my-app-pr123-add-login-form`、`dotfiles-shell-startup-speedup`

## 名前を予約する

Bash で次を実行する(`<name>` を組み立てた名前に置き換える)。

```bash
${CLAUDE_SKILL_DIR}/scripts/set_pending_name.sh ${CLAUDE_SESSION_ID} <name>
```

失敗したら(セッション ID のプレースホルダが展開されずに残っていた、など)予約はせず、
下の `/rename` の行だけを出す。

## 出力

````markdown
次のプロンプトを送ったときに、セッション名が `my-app-pr123-add-login-form` になります。
識別子は ブランチの PR #123、説明は ログインフォームの追加 から。
すぐに変えたい・名前を直したいときは:
```
/rename my-app-pr123-add-login-form
```
````

予約したことと名前、識別子・説明の根拠を1行、手動で付けるときの `/rename` の1行。
~~~~~

### `~/.claude/skills/session-name/scripts/session_name_parts.sh`

~~~~~
#!/usr/bin/env bash
# セッション名のうち機械的に決まる部分を key=value で出す。
# 取れなかった値は空にし、どんな場合も exit 0 で終える(スキルへの埋め込みを止めないため)。

normalize() {
  tr '[:upper:]' '[:lower:]' | tr '_ ' '--'
}

repo=""
branch=""
pr=""
ticket=""
pr_title=""

if git rev-parse --is-inside-work-tree >/dev/null 2>&1; then
  origin=$(git remote get-url origin 2>/dev/null)
  if [ -n "$origin" ]; then
    repo=$(basename "${origin%/}" .git)
  else
    # worktree の中でも本体のリポジトリ名になるよう、作業ツリーではなく共通の .git から辿る
    common=$(git rev-parse --path-format=absolute --git-common-dir 2>/dev/null)
    case "$common" in
      */.git) repo=$(basename "$(dirname "$common")") ;;
      ?*) repo=$(basename "$common" .git) ;;
    esac
  fi

  branch=$(git branch --show-current 2>/dev/null)
  ticket=$(printf '%s' "$branch" | grep -oE '[A-Z][A-Z0-9]+-[0-9]+' | head -n 1)

  if [ -n "$branch" ] && command -v gh >/dev/null 2>&1; then
    # 同じブランチ名を使い回したときに古い PR を拾わないよう、OPEN だけを使う
    pr_line=$(GH_PROMPT_DISABLED=1 gh pr view --json number,state,title \
      -q 'select(.state == "OPEN") | "\(.number)\t\(.title)"' 2>/dev/null)
    if [ -n "$pr_line" ]; then
      pr="pr${pr_line%%$'\t'*}"
      pr_title="${pr_line#*$'\t'}"
    fi
  fi
fi

[ -n "$repo" ] || repo=$(basename "$PWD")
repo=$(printf '%s' "$repo" | normalize)

printf 'repo=%s\nbranch=%s\npr=%s\nticket=%s\npr_title=%s\n' \
  "$repo" "$branch" "$pr" "$ticket" "$pr_title"
exit 0
~~~~~

### `~/.claude/skills/session-name/scripts/set_pending_name.sh`

~~~~~
#!/usr/bin/env bash
# セッション名の予約を書く。次のプロンプト送信時に apply_pending_name.sh(UserPromptSubmit hook)が
# sessionTitle として設定する。モデルは /rename を実行できないため、この2段構えにしている。
#
# 使い方: set_pending_name.sh <session_id> <name>
set -u

session_id=${1:-}
name=${2:-}
dir="$HOME/.claude/state/session-name"

# session_id はファイル名になるので、パスを辿れる文字を通さない
if ! printf '%s' "$session_id" | grep -qE '^[A-Za-z0-9-]+$'; then
  echo "session_id が不正です: '$session_id'" >&2
  exit 1
fi
if ! printf '%s' "$name" | grep -qE '^[A-Za-z0-9][A-Za-z0-9._-]{0,79}$'; then
  echo "名前に使えるのは英数字・. _ - で80文字までです: '$name'" >&2
  exit 1
fi

mkdir -p "$dir" && printf '%s\n' "$name" > "$dir/$session_id" || exit 1
echo "予約しました: $name"
~~~~~

### `~/.claude/skills/session-name/scripts/apply_pending_name.sh`

~~~~~
#!/usr/bin/env bash
# UserPromptSubmit hook。set_pending_name.sh が予約した名前があれば sessionTitle として設定し、
# 予約を消す。予約が無ければ何も出力せず exit 0(全プロンプトで走るので、早く抜ける)。
set -u

dir="$HOME/.claude/state/session-name"
[ -n "$(ls -A "$dir" 2>/dev/null)" ] || exit 0

# セッションが次のプロンプトを送らずに終わると予約が残るので、古いものを掃除する
find "$dir" -type f -mtime +7 -delete 2>/dev/null

session_id=$(jq -r '.session_id // empty' 2>/dev/null)
printf '%s' "$session_id" | grep -qE '^[A-Za-z0-9-]+$' || exit 0

pending="$dir/$session_id"
[ -f "$pending" ] || exit 0
name=$(head -n 1 "$pending")
rm -f "$pending"
[ -n "$name" ] || exit 0

jq -n --arg title "$name" \
  '{hookSpecificOutput: {hookEventName: "UserPromptSubmit", sessionTitle: $title}}'
~~~~~
