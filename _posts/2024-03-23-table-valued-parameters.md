---
title: Table Valued Parameters (TVP)
date: 2024-03-23 14:00 +0900
author: hoon
category:
- SQL
tags:
- 프로시저
- TVP
---
## 상황
프로시저나 함수에 여러개의 값을 전달하고 싶다. \
값의 개수는 1 ~ 1000개 사이다. \
값의 타입은 언제나 고정적이다.

아래 방법 모두 가능하다.
1. 값마다 매번 프로시저를 호출하거나,
2. 값들을 하나의 csv 스트링으로 만들어서 프로시저에 던진다. 그리고 프로시저 안에서 split해서 사용한다.

근데 위에 방법은 비효율적이다.\
 `프로시저에 TVP를 전달`하는 방법으로 해결해보자.


## TVP 사용 예시

<script src="https://gist.github.com/hooneychoi/c0d5c8358eca2d9eeec12d493d78f78a.js"></script>


## 장점
- set based operation이 가능하다.
- 
## 단점
