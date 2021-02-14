---
title: Heroku multiple accounts
category: services
intro: How to use multiple heroku accounts, branches, fork heroku apps,
keywords:
  - Heroku
  - Heroku cli
  - PaaS
  - Multiple accounts
  - Fork
---

## Branches

### Add a remote for an existing app

```shell
$ heroku git:remote -a app-name
```

Sets git remote `heroku` to `https://git.heroku.com/app-name.git`

### Renaming a remote

```shell
$ git remote rename heroku heroku-staging
```

## Multiple accounts

### Install official plugin
{: .-intro}

```shell
$ heroku plugins:install heroku-accounts
```

- [Heroku Account Documentation](https://github.com/heroku/heroku-accounts) _heroku/heroku-accounts_

### Add new account

```shell
$ heroku accounts:add personal
Enter your Heroku credentials.
Email: david@heroku.com
Password: ******
```

### List accounts

```shell
$ heroku accounts
```

### Switch to a given account

```shell
$ h![](http://)eroku accounts:set personal
```

## Copy env

### Export app env

```shell
$ heroku config -s -a existing-heroku-app > config.txt
```

### Push to another app

```shell
$ cat config.txt | tr '\n' ' ' | xargs heroku config:set -a new-heroku-app
```

## Fork

### Install deprecated plugin
{: .-intro}

```bash
$ heroku plugins:install heroku-fork
```

- [Heroku Fork Documentation](https://github.com/heroku/heroku-fork) _heroku/heroku-fork_


## Setup on mac

### Install heroku CLI

```
brew tap heroku/brew && brew install heroku
```

## See also

### References
{: .-intro}

- [Heroku CLI Commands Documentation](https://devcenter.heroku.com/articles/heroku-cli-commands)

