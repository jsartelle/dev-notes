---
{"dg-publish":true,"dg-path":"Cheat sheets/Git and GitHub.md","permalink":"/cheat-sheets/git-and-git-hub/","tags":["language/terminal"]}
---


# Git

## Show all files in index

```shell
git ls-tree -r BRANCH_NAME --name-only
```

## Remove ignored file from index

```shell
git rm --cached <path>
```

## Rebuild Git index

Removes all tracked files that should be ignored

```shell
git rm -r --cached .
git add .
```

## Find out why a file is ignored

```shell
git check-ignore -v <pathname>
```

## Restore a single file from main

```shell
git checkout origin/main -- path/to/file
```

## Reset local branch from remote

`@{u}` represents the upstream branch that the current branch is tracking

```shell
git reset --hard "@{u}"
```

## Change remote tracking branch

- must be on the branch you want to change tracking for
- use `git status` to see which branch is currently being tracked

### Set remote tracking branch

```shell
git branch --set-upstream-to=origin/branch-name
```

### Stop tracking remote branch

```shell
git branch --unset-upstream
```

## Drop a commit

- run `git rebase -i HEAD~N`, where N is the number of commits to list (the commit you want to drop should be less than N commits back)
- in the document that appears, delete the line with the commit you want to drop, then save and exit

### With GitLens

- right-click the commit before the one you want to drop, and select *Rebase Current Branch onto Commit...*
- select *Interactive Rebase*
- in the rebase view, select *Drop* for the commit you want to drop, and *Pick* for the rest
- click *Start Rebase* at the bottom

## Undo reset or dropped commit

```shell
git reflog
```

- will show a list of actions, like this

```
e192732d8 HEAD@{22}: reset: moving to HEAD
e192732d8 HEAD@{23}: commit: Add some stuff
555fe5cf7 HEAD@{24}: checkout: moving from main to cool-branch
62d93e725 HEAD@{25}: checkout: moving from cool-branch to main
555fe5cf7 HEAD@{26}: reset: moving to HEAD
```

- to reset to a particular point:

```shell
git reset --hard HEAD@{23}
# or
git reset --hard e192732d8
```

## Undo dropped stash

- shows unreachable stashes in your repo

```shell
git fsck --unreachable | grep commit | cut -d ' ' -f3 | xargs git log --merges --no-walk --grep=WIP
```

- if you find the stash, apply it:

```shell
git stash apply <SHA>
```

## Abort merge or rebase

```shell
git merge --abort
```

```shell
git rebase --abort
```

## Change author for entire repository

- will rewrite all commits to use your current author info (so don't use on repos with multiple contributors)
- set your new author info first with `git config user.name` and `git config user.email` (add `--global` to do it globally)

```shell
git rebase -r --root --exec "git commit --amend --no-edit --reset-author"
```

# GitHub

> [!warning]
> Be careful when using the Hide Whitespace option in pull requests. This can mask indentation and bracket mismatch errors!

## Filter pull requests & issues by author

```
author:@me
author:username
```

## Filter pull requests by target branch

```
is:pr base:main
```

## Sort pull requests by merge date

- not an option, but two workarounds:
    - use the Insights page (if merged within the last month)
    - use this query to sort by updated date

```
is:pr is:merged sort:updated-desc
```
