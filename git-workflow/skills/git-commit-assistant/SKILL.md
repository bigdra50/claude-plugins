---
name: git-commit-assistant
description: Git commit creation assistant with gitmoji prefixes and Japanese messages. Supports two modes staged-only (commits only staged changes) and smart-commit (analyzes all changes and creates multiple commits with appropriate granularity using git add -p). Use when user wants to create git commits with proper gitmoji categorization.
allowed-tools:
  - Bash(git:*)
context: fork
agent: general-purpose
hooks:
  PreToolUse:
    - matcher: "Bash"
      hooks:
        - type: command
          command: "git diff --cached --name-only | xargs -I {} sh -c 'grep -l -E \"(password|secret|api_key|token|private_key)\\s*[:=]\" \"{}\" 2>/dev/null' && echo \"WARNING: Possible secrets in staged files\" >&2 || true"
          once: true
  Stop:
    - type: command
      command: "git log -1 --oneline 2>/dev/null || true"
---

# Git Commit Assistant

## Overview

Assists in creating git commits with gitmoji prefixes and Japanese commit messages. Supports both simple staged-only commits and intelligent multi-commit workflows that automatically split changes into logical units.

## When to Use This Skill

Use this skill when the user requests:
- "Create a commit" / "コミットを作成して"
- "Commit these changes" / "この変更をコミットして"
- "Make a git commit with gitmoji" / "gitmojiでコミットして"
- "Split changes into multiple commits" / "変更を複数のコミットに分けて"
- Any request involving git commit creation with Japanese messages

## Core Capabilities

### 1. Staged-Only Mode (Default)

Commits only the staged changes (files added with `git add`).

**Workflow:**
1. Check git status to verify staged changes exist
2. Review staged diff to understand changes
3. Analyze recent commits to maintain consistency
4. Select appropriate gitmoji based on change type
5. Create concise Japanese commit message
6. Execute commit

**Usage:**
```
User: "コミットを作成して"
Assistant: [Analyzes staged changes and creates single commit]
```

### 2. Smart-Commit Mode

Analyzes all changes (both staged and unstaged) and creates multiple commits with appropriate granularity using `git add -p`.

**Workflow:**
1. Check git status to see all changes
2. Review all diffs (staged and unstaged)
3. Analyze and categorize changes into logical units
4. For each logical unit:
   - Use `git add -p` to selectively stage specific changes
   - Select appropriate gitmoji
   - Create Japanese commit message
   - Execute commit
5. Verify all changes are committed

**Usage:**
```
User: "全ての変更をスマートコミットして"
User: "変更を適切な単位に分けてコミットして"
Assistant: [Creates multiple commits with logical separation]
```

### 3. Triggering Smart-Commit Mode

Smart-commit mode is triggered when the user explicitly requests:
- "スマートコミット"
- "変更を分けてコミット"
- "複数のコミットに分割"
- "適切な単位でコミット"
- Any explicit mention of creating multiple commits or splitting changes

Default to staged-only mode unless explicitly requested otherwise.

## Gitmoji Reference

Select appropriate gitmoji based on the nature of changes:

| Gitmoji | GitHub Shortcode | Use Case |
|---------|------------------|----------|
| ✨ | `:sparkles:` | New features |
| 🐛 | `:bug:` | Bug fixes |
| 📝 | `:memo:` | Documentation updates |
| ♻️ | `:recycle:` | Refactoring |
| ⚡ | `:zap:` | Performance improvements |
| 🎨 | `:art:` | Code structure/format improvements |
| 🔧 | `:wrench:` | Configuration file changes |
| ✅ | `:white_check_mark:` | Adding/updating tests |
| 🚀 | `:rocket:` | Deployment/releases |
| 🔒 | `:lock:` | Security fixes |
| 🚨 | `:rotating_light:` | Fixing linter warnings |
| 🔥 | `:fire:` | Removing code or files |
| 🚚 | `:truck:` | Moving/renaming files |
| 💄 | `:lipstick:` | UI/style updates |
| 🌐 | `:globe_with_meridians:` | Internationalization |
| 🔖 | `:bookmark:` | Release tags |
| 🎉 | `:tada:` | Initial commit |

## Commit Message Guidelines

### Format
```
:gitmoji: 簡潔な日本語での変更内容
```

### Best Practices
- Keep message concise (ideally under 50 characters for title)
- Focus on "what" and "why" rather than "how"
- Use imperative mood in Japanese (e.g., "追加", "修正", "更新")
- Avoid redundant information already conveyed by gitmoji
- Match tone and style of recent commits when possible

### Examples
```
:sparkles: ユーザー認証機能を追加
:bug: ログイン時のバリデーションエラーを修正
:memo: APIドキュメントを更新
:recycle: データ取得ロジックをリファクタリング
:zap: 画像読み込みパフォーマンスを改善
:wrench: ESLint設定を更新
```

## Workflow Decision Tree

```
User requests commit
    │
    ├─ Explicit smart-commit request? ────→ YES ─→ Smart-Commit Mode
    │                                              (Multiple commits with git add -p)
    │
    └─ NO ─→ Staged-Only Mode
              (Single commit with staged changes)
```

## Pre-Commit Checklist

Before creating any commit:

1. ✓ Verify git status shows changes to commit
2. ✓ Review diff to understand nature of changes
3. ✓ Check recent commits for style consistency
4. ✓ Confirm no sensitive data in changes (.env, credentials, etc.)
5. ✓ Select most appropriate gitmoji
6. ✓ Draft clear, concise Japanese message

## Error Handling

**No staged changes:**
- Inform user no changes are staged
- Suggest using `git add` or asking for smart-commit mode

**Merge conflicts:**
- Detect conflict markers in diffs
- Warn user to resolve conflicts before committing

**Pre-commit hooks failure:**
- Show hook error output
- Wait for user to fix issues before retrying

## Resources

This skill does not require additional scripts, references, or assets. All necessary logic is contained in this SKILL.md file.
