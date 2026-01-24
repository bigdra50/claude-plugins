---
allowed-tools:
  - Bash(git status:*)
  - Bash(git diff --staged:*)
  - Bash(git branch:*)
  - Bash(git log:*)
  - Bash(git commit:*)
  - Bash(git reset)
  - Bash(git add:*)
  - Read
description: Create a git commit with gitmoji prefix and Japanese message for staged changes
model: haiku
---

# Create a git commit with staged changes using gitmoji prefix and Japanese commit message

## Context

- Current git status: !`git status`
- Current git diff(staged): !`git diff --staged`
- Current branch: !`git branch --show-current`
- Recent commits: !`git log --oneline -10`

## Your task

Follow these rules:

1. **Use gitmoji as prefix (GitHub shortcode format)**
   - :sparkles: New features
   - :bug: Bug fixes
   - :memo: Documentation updates
   - :recycle: Refactoring
   - :zap: Performance improvements
   - :art: Code structure/format improvements
   - :wrench: Configuration file changes
   - :white_check_mark: Adding/updating tests
   - :rocket: Deployment/releases
   - :lock: Security fixes

2. **Write commit message in Japanese**

3. **Analyze changes and create appropriate gitmoji with concise description**

4. **Prohibited commands (NEVER use)**
   - `git add .` - Use specific file paths instead
   - `git add -A` - Use specific file paths instead
   - `git reset --hard` - Irreversible operation

Workflow:

1. Based on above context, understand changes
2. Analyze the nature of changes to select appropriate gitmoji
3. **Check if multiple concerns exist in staged changes**
   - If changes contain different concerns (e.g., feat + docs, fix + refactor):
     - Inform the user about the mixed concerns
     - Ask user to unstage some changes and commit separately
     - Do NOT proceed with a mixed-concern commit
   - Example of mixed concerns that should be split:
     - New feature (feat) → 1st commit
     - Documentation (docs) → 2nd commit
     - Test addition (test) → 3rd commit
4. Create concise and clear Japanese commit message
5. Execute the commit

If $ARGUMENTS is provided, use it as reference for the commit message.
