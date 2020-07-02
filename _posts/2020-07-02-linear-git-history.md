---
layout: post
title:  "linear git history"
comments: true
date:   2020-07-02 12:11:00
---


간단히 말하면, git 그래프 모양을 single branch 로 유지하는 것.

merge commit 을 허용하지 않고, 항상 rebase 또는 fast forward merge 를 사용하여

브랜치 그래프를 최대한 단순한 모양으로 유지하는 방식이다.

 

* 그림으로 차이 확인 가능.

![](/images/2020-07-02/01-linear.png)
![](/images/2020-07-02/02-non-linear.png) 



참고 : https://www.bitsnbites.eu/a-tidy-linear-git-history/

** 좀 오래된 글이라 github 관련 내용은 맞지 않습니다.

github 에 `Rebase and merge` 옵션이 이미 추가되었습니다.

https://github.blog/2016-09-26-rebase-and-merge-pull-requests/

 

## 장점

한마디로, history 트래킹을 잘(쉽게) 하기 위함이다.

아마도 별로 사용할 일은 없지만 git bisect 도 가능하다.

원인을 알기가 매우 어려운 문제점이 있을 때, 이 문제가 어느 시점에 발생하기 시작했는 지를 추적하기 비교적 쉽다.

git revert 등도 쉽게 사용할 수 있다.

 

 

## 문제점

이 방식은 gitflow 와 충돌한다.

master 브랜치에서 배포한 특정 시점(tag)에서 hotfix 를 해야할 경우가 발생하면

필연적으로 merge commit 이 발생하기 때문입니다.

 

 

## so, how ?

master 브랜치에 linear history 를 항상 유지하기 어렵기 때문에

고민 끝에 master 에서 작업을 하는 경우는 거의 없으므로 master 브랜치에 대해서는 linear history 를 포기하고,

develop 브랜치만 최대한 아름답게 유지하기로 결정.

