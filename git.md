# Git

## Commit

### Undo last commit

```shell
git reset --soft HEAD~1
git reset
```
Undo the last commit then unstage everything.

## Staging

### Diff staging

```shell
git diff --staged
```

### Add new file as unstaged

```shell
git add -n
```
Warning: this is incompatible with stashing unstaged files somehow.

## Stash

### Stash unstaged & untracked

```shell
git stash -k -u
```
Stash unstaged files & untracked files (-u).

