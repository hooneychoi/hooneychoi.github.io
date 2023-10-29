---
layout: post
title: Clustered Index VS Non-Clustered Index
date: 2023-10-28 23:37 +0900
author: hoon
category:
- SQL
tags:
- 인덱스
- 클러스터드인덱스
- 넌클러스터드인덱스
---
# 학습 목표

<br>
<br>

# B-Tree에서 차이점

B-Tree구조는 일반적인(로우스토어 인덱스) 테이블에서 논리적, 물리적 데이터가 저장되는 모습을 나타낸 것이다.

![standard_b_tree](/assets/img/sql/standard_b_tree.jpg)
*Leaf Level이 데이터 페이지인 것에 주목하자.*

논리적으로만 표현하는 것을 넌클러스터드 인덱스, 물리적인 것까지 포함해서 표현하는 것을 클러스터드 인덱스라고 한다. 

넌클러스터드 인덱스는 RID만 가르키는 포인터다. 그 테이블의 RID의 위치를 저장한 별개의 파일을 만든다고 생각하면 된다. 즉 테이블의 물리적 저장 상태에는 영향을 끼치지 않는다. 따라서 한 테이블에 여러개를 만들 수 있다. 

반대로 클러스터드 인덱스는 물리적으로 데이터 페이지의 저장 상태에 영향을 끼친다. 그 인덱스의 키값으로 정렬해서 데이터 페이지를 저장해버린다. 따라서 한 테이블에 한개만 만들 수 있다.

아래 표의 수준으로만 이해하면 된다.

|현실|SQL Server|
|:-|:-|
|책|테이블|
|목차|클러스터드 인덱스|
|색인1(단어의 페이지)|넌클러스터드 인덱스|
|색인2(단어의 페이지)|넌클러스터드 인덱스|
|색인3(단어의 페이지와 간략한 설명)|넌클러스터드 인덱스 with Include|

<br>
<br>

# 클러스터드 인덱스

<br>
<br>

# 클러스터드 인덱스
# 참조
<https://www.sqlservercentral.com/articles/understanding-curd-operations-on-tables-with-b-tree-indexes-page-splits-and-fragmentation>