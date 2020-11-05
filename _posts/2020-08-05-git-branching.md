---
layout: post
title:  "Git branching model"
comments: true
date:   2020-11-05 19:16:00
---

## git flow

https://nvie.com/posts/a-successful-git-branching-model/

![](https://nvie.com/img/git-model@2x.png)


주요 개념

* main : production ready
* develop : feature merged
* hotfix : bug fix from production
* feature : new features


## github flow

https://guides.github.com/introduction/flow/

GitHub flow is a lightweight, branch-based workflow
that supports teams and projects where deployments are made regularly.

* creating a branch
* add commits
* open a Pull Request
* code review
* deploy
* merge

## gerrit flow

https://docs.opendev.org/opendev/infra-manual/latest/sandbox.html

examples: 
https://review.opendev.org/#/q/status:open

특징: 돌고래 아이큐 정도는 되어야 사용할 수 있는 고난이도 사용성
![img](https://review.opendev.org/Documentation/images/intro-quick-new-review.jpg)
[출처](https://review.opendev.org/Documentation/intro-quick.html)

## brancing strategies for pactical applications

![그림 생략](https://some.pictures.com)

* develop : single linear branch
* main : only for production
* others : same as git-flow
