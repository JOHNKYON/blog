---
title: Start a Smart Project with Github
tags:
  - Github
  - Engineer
categories:
  - Engineer
date: 2018-05-11 13:41:39
---


**GitHub** is now widely used platform that helps people develop and distribute their project. Knowing how to use some interesting function of GitHub can make your developing process fluent and stable.

- Issue and pull-request
- Automatic test
- Protected Master branch

----------

# Issue and Pull Request

When building a project, the developing history should be open and trackable. In order to do so, it is recommended to use some method to track every change and improvement. Use **Issue** to track every improvements that will do developers good favour.

Every issue have an index, which is marked by #, looks like `#3`.

**Pull request** is used for cooperation, GitHub provides many interesting functions with pull request, one of them is *Close an issue with pull request*. In order to do so, just make the description of your pull request look like this:

```
Message: Pull request from forked "xxx".

Description: Resolve #3.
```

*"Resolve"* is a token that will close an issue, so will *"Fix"* and *"close"* do. Check [official doc](https://help.github.com/articles/closing-issues-using-keywords/) for further information.

# Automatic test
Every time you commit something to your project, you have to make sure that the whole project doesn't break down because of your commit contains bugs or something. Luckily, Travis-CI can do it and its also supported by GitHub, wichi means you can view if your test passed or not before admit a pull request.
![](\images\github_project\1.png)

To distribute the automatic test is very simple. All you need to do is add a ```.travis.yml``` file to the root directory of your project.

See [official doc](https://docs.travis-ci.com/) for more help.

# Protected Master branch
Master branch is very important to your project, which should not be manipulated casually, it is recommended to add several protection checks when merge any commit to your master branch.

It is recommended to do as follows:
![](\images\github_project\2.png)

Then look! Your master branch is under protection!
![](\images\github_project\3.png)
