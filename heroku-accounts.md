---
title: Heroku multiple accounts
category: services
intro: How to use multiple heroku accounts with the heroku CLI.
keywords:
  - Heroku
  - Heroku cli
  - PaaS
  - Multiple accounts
---

### Install official plugin
{: .-intro}

- [Heroku Account](https://github.com/heroku/heroku-accounts) _heroku/heroku-accounts_

### Add new account

```shell
$ heroku accounts:add personal
Enter your Heroku credentials.
Email: david@heroku.com
Password: ******
```

### List accounts

```sh
$ heroku accounts
```

### Switch to a given account

```sh
$ heroku accounts:set personal
```
