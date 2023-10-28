---
layout: post
title: 컬럼스토어 vs 로우스토어 간략 비교
date: 2023-10-28 20:56 +0900
author: hoon
category:
- SQL
tags:
- 컬럼스토어
---
# 학습 목표

로우스토어 저장방식과 컬럼스토어의 가장 기본적인 차이에 대해 간략히 알고 넘어가보자.

<br>
<br>

# 로우스토어
로우스토어는 가장 기본적이고 고전적인 데이터 저장 방식이다. 데이터가 논리적, 물리적으로 동일하게 저장된다. 아마도 익숙하기 때문에 이해하는데에 큰 어려움이 없을 것이다. 

아래 사진에서는 각 페이지마다 6개 컬럼 8개 행의 데이터가 저장돼있다.

![rowstore_pages](/assets/img/sql/rowstore_pages.jpg)

인덱스가 적절히 만들어져 있다고 가정할 때 seek 탐색에 유용하다. 따라서 OLTP에서 사용되는 저장 방식이다. 

예를들어 `SELECT * FROM TableName WHERE SalesOrderID = 43659 and ProductID = 776` 쿼리는 단 하나의 페이지만 읽으면 된다.

<br>
<br>

# 컬럼스토어
컬럼스토어는 로우스토어와 물리적으로 다르게 저장된다. 컬럼마다 페이지가 따로 저장된다.

![columestore_pages](/assets/img/sql/columestore_pages.jpg)

컬럼이 물리적으로 분리되어 저장됐기 때문에 `SELECT * FROM TableName WHERE SalesOrderID = 43659 and ProductID = 776` 쿼리를 수행하려면 모든 페이지를 다 읽는 수 밖에 없다. 왜냐하면 SELECT절에 모든 컬럼(페이지)이 포함됐기 때문이다.

그럼 컬럼스토어는 언제 사용할까? 바로 적은 개수의 컬럼에 대해 스캔(어차피 실행계획에서는 항상 Columestore Index Scan이다.)할 때 쓰인다. 주로 OLAP환경에서 쓰는 방법이다. 

예를들어 `SELECT MAX(OrderQty) FROM TableName` 쿼리를 수행하기 위에 단 하나의 페이지만 읽으면 된다. 아까 로우스토어 환경에서는 4개 페이지를 모두 읽었어야 했을 것이다.

사실 여기서 끝이 아니라 압축까지 된다. 압축됐기 때문에 테이블 사이즈도 줄고 스캔해야할 범위도 줄어든다. 압축 방식은 다른 포스팅에서 자세히 다룰 것이다.