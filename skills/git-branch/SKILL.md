---
name: git-branch
description: >-
  Guides Git branching: create and switch branches, naming conventions (feature/,
  fix/, chore/), list and clean up branches, sync with upstream, and avoid
  destructive operations without confirmation. Use when the user asks about
  branches, wants a new branch, or needs help untangling local branch state.
---

# Git branch

## Before changing branches

1. If there are uncommitted changes, either commit, stash, or discard—never assume it is safe to switch without checking `git status`.
2. Confirm the default branch name (`main` vs `master`) with `git symbolic-ref refs/remotes/origin/HEAD` or `git branch -a` when unclear.

## Create a branch

- From current HEAD: `git switch -c <branch>` (or `git checkout -b <branch>` on older Git).
- From a specific base (e.g. latest `main`): `git fetch origin` then `git switch -c <branch> origin/main` (adjust remote and branch names).

Prefer **linear history** off the team’s agreed default branch unless the user explicitly wants something else.

## Naming conventions (suggest unless the user specifies)

| Prefix | Use for |
|--------|---------|
| `feature/` | New user-facing behavior |
| `fix/` | Bug fixes |
| `chore/` | Tooling, deps, formatting |
| `docs/` | Documentation only |

Use lowercase and hyphens: `feature/oauth-refresh-token`.

## List and inspect

- Local: `git branch -v`
- Including remote tracking: `git branch -vv`
- All refs: `git branch -a`

## Switch (safe)

- `git switch <existing-branch>` — fails if it would overwrite local changes without a merge strategy; surface the error and recommend stash/commit.

## Rename / delete

- Rename current branch: `git branch -m <new-name>`
- Delete local (merged): `git branch -d <branch>`
- Force delete local only when the user confirms the branch is obsolete: `git branch -D <branch>`
- Deleting a remote branch: `git push origin --delete <branch>` — only after explicit user confirmation; state impact for collaborators.

## Sync with remote

- Update refs: `git fetch origin`
- Update current branch from its upstream: `git pull --ff-only` when fast-forward is desired; if it fails, explain non-FF situation and options (rebase vs merge) instead of blind `--force`.

## Destructive operations

- Never suggest `git push --force` to shared default branches (`main`, `master`, `develop`) unless the user clearly requests it and understands the risk.
- For `reset --hard`, require explicit user intent; offer safer alternatives first (`git stash`, `git restore`).

## Output

- State which branch the user is on after operations when relevant.
- When proposing commands, use the repo’s actual remote and default branch names when known from context.
