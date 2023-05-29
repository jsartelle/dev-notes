---
{"dg-publish":true,"dg-path":"Cheat sheets/Git cheat sheet.md","permalink":"/cheat-sheets/git-cheat-sheet/","created":"","updated":""}
---


# Git

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

## Restore a single file from main

```shell
git checkout origin/main -- path/to/file
```

## Recreate local branch from remote

`@{u}` represents the upstream branch that the current branch is tracking

```shell
git reset --hard @{u}
```

## Find out why file is ignored

```shell
git check-ignore -v <pathname>
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
