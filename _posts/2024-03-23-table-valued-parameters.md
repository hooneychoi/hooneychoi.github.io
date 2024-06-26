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
## 1. 상황
프로시저나 함수에 여러개의 값을 전달하고 싶다. \
값의 개수는 1 ~ 1000개 사이다. \
값의 타입은 언제나 고정적이다.

아래 방법 모두 가능하다.
1. 값마다 매번 프로시저를 호출하거나,
2. 값들을 하나의 csv 스트링으로 만들어서 프로시저에 던진다. 그리고 프로시저 안에서 split해서 사용한다.

근데 위에 방법은 비효율적이다.\
`프로시저에 TVP`를 전달하는 방법으로 해결해보자.

## 2. TVP 사용 예시
<script src="https://gist.github.com/hooneychoi/c0d5c8358eca2d9eeec12d493d78f78a.js"></script>

## 3. 장점
- set based operation이 가능하다.
- 머지문이랑 같이쓰면 좋다.
- 클라이언트에서 원하는 값을 제어해서 전달할 수 있다.
- 임시 테이블 안만들어도 된다.

## 4. 단점
- 형변환 할 수 없다. READONLY
- 1000개 행 미만에만 쓰자. 1000개 행 넘으면 벌크 인서트하자. [참고](https://www.sqlshack.com/table-valued-parameters-in-sql-server/), [참고2](https://thwack.solarwinds.com/groups/data-driven/b/blog/posts/table-valued-parameters-vs-sqlbulkcopy-for-bulk-loading-data-into-sql-server)