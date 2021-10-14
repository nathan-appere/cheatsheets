---
title: Git
category: Git
description: Git commands arround commits, stash, staging
type: personal
---

## Commit

### Only commit files in specific dir

```shell
git commit -m "Message" -- ./dir
```

### Undo last commit

```shell
$ git reset --soft HEAD~1
$ git reset
```
Undo the last commit + unstage everything.

```shell
$ git stash; git reset --soft HEAD~1; git stash pop;
```
Stash changes + undo the last commit + re-add changes.

### Empty commit

```shell
$ git commit --allow-empty -m "Trigger re-deploy"
```

### Show commit content

```shell
git diff COMMIT^!
```

## Staging

### Diff staging

```shell
$ git diff --staged
```

### Add new file as unstaged

```shell
$ git add -n
```
Warning: this is incompatible with stashing unstaged files somehow.

## Stash

### Stash unstaged & untracked

```shell
$ git stash -k -u
```
Stash unstaged files & untracked files (-u).
