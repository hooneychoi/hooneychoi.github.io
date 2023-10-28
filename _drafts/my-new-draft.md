---
layout: post
title: My new draft
description:
image:
category:
tags:
---

# 학습 목표

로우 스토어 저장방식과 컬럼 스토어의 가장 기본적인 차이에 대해 간략히 알고 넘어가보자.

<br>
<br>

# 로우 스토어
로우 스토어는 가장 기본적이고 고전적인 데이터 저장 방식이다. 데이터가 논리적, 물리적으로 동일하게 저장된다. 아마도 익숙하기 때문에 이해하는데에 큰 어려움이 없을 것이다. 

아래 사진에서는 각 페이지마다 6개 컬럼 8개 행의 데이터가 저장돼있다.

![row_store_pages](/assets/img/sql/row_store_pages.jpg)

인덱스가 적절히 만들어져 있다고 가정할 때 seek 탐색에 유용하다. 따라서 OLTP에서 사용되는 저장 방식이다. 

예를들어 `SELECT * FROM TableName WHERE SalesOrderID = 43659 and ProductID = 776` 쿼리는 단 하나의 페이지만 읽으면 된다.

<br>
<br>

# 컬럼 스토어
컬럼 스토어는 로우 스토어와 물리적으로 다르게 저장된다. 컬럼 마다 페이지가 다르게 저장된다.

![colume_store_pages](/assets/img/sql/colume_store_pages.jpg)

물리적으로 아예 저장됐기 때문에 `SELECT * FROM TableName WHERE SalesOrderID = 43659 and ProductID = 776` 쿼리를 수행하려면 모든 페이지를 다 읽는 수 밖에 없다. 왜냐하면 SELECT절에 모든 컬럼(페이지)가 포함됐기 때문이다.

그럼 컬럼 스토어는 언제 사용할까? 바로 적은 개수의 컬럼에 대해 스캔할 때 쓰인다. 주로 OLAP환경에서 쓰는 방법이다. 

예를들어 `SELECT MAX(OrderQty) FROM TableName` 쿼리를 수행하기 위에 단 하나의 페이지만 읽으면 된다. 아까 로우 스토어 환경에서는 4개 페이지를 모두 읽었어야 했을 것이다.

사실 여기서 끝이 아니라 압축까지 된다. 압축됐기 때문에 테이블 사이즈도 줄고 스캔해야할 범위도 줄어든다. 압축 방식은 다른 포스팅에서 자세히 다룰 것이다.

# 컬럼 스토어의 압축 방식

## 데이터 준비
```sql
DROP TABLE IF EXISTS dbo.STAGING_TABLE;
CREATE TABLE dbo.STAGING_TABLE (
    ID BIGINT NOT NULL,
    STR1 VARCHAR(16) NOT NULL,
    PRIMARY KEY (ID)
);

INSERT INTO dbo.STAGING_TABLE WITH (TABLOCK)
SELECT TOP (4 * 1048576)
  ROW_NUMBER() OVER (ORDER BY (SELECT NULL))
, LEFT(CAST(NEWID() AS VARCHAR(36)), 16)
FROM master..spt_values t1
CROSS JOIN master..spt_values t2
OPTION (MAXDOP 1);

DROP TABLE IF EXISTS dbo.DELTA_STORE_DUMPING_GROUND;
CREATE TABLE dbo.DELTA_STORE_DUMPING_GROUND (
    ID BIGINT NULL,
    STR1 VARCHAR(100) NULL,
    STR2 VARCHAR(100) NULL,
    STR3 VARCHAR(100) NULL,
    STR1_MAX VARCHAR(MAX) NULL,
    INDEX CCI CLUSTERED COLUMNSTORE
);
```

<br>
<br>

## 102400개 미만
```sql
TRUNCATE TABLE dbo.DELTA_STORE_DUMPING_GROUND;

INSERT INTO dbo.DELTA_STORE_DUMPING_GROUND (ID)
SELECT TOP (102399) ID
FROM dbo.STAGING_TABLE
OPTION (MAXDOP 1);

select row_group_id, delta_store_hobt_id, state_desc, total_rows
from sys.dm_db_column_store_row_group_physical_stats
```
|row_group_id|delta_store_hobt_id|state_desc|total_rows|
|---|---|---|---|
|0|72057594046119936|OPEN|102399|

<br>
<br>

## 102400개 이상 1048576개 이하

```sql
TRUNCATE TABLE dbo.DELTA_STORE_DUMPING_GROUND;

INSERT INTO dbo.DELTA_STORE_DUMPING_GROUND (ID)
SELECT TOP (102400) ID
FROM dbo.STAGING_TABLE
OPTION (MAXDOP 1);

INSERT INTO dbo.DELTA_STORE_DUMPING_GROUND (ID)
SELECT TOP (1048576) ID
FROM dbo.STAGING_TABLE
OPTION (MAXDOP 1);

select row_group_id, delta_store_hobt_id, state_desc, total_rows
from sys.dm_db_column_store_row_group_physical_stats
```
|row_group_id|delta_store_hobt_id|state_desc|total_rows|
|---|---|---|---|
|1|NULL|COMPRESSED|1048576|
|0|NULL|COMPRESSED|102400|

<br>
<br>

# 1048576개 초과
```sql
TRUNCATE TABLE dbo.DELTA_STORE_DUMPING_GROUND;

INSERT INTO dbo.DELTA_STORE_DUMPING_GROUND (ID)
SELECT TOP (1048576+1) ID
FROM dbo.STAGING_TABLE
OPTION (MAXDOP 1);

select row_group_id, delta_store_hobt_id, state_desc, total_rows
from sys.dm_db_column_store_row_group_physical_stats
```
|row_group_id|delta_store_hobt_id|state_desc|total_rows|
|---|---|---|---|
|1|72057594046185472|OPEN|1|
|0|NULL|COMPRESSED|1048576|

<br>
<br>

## LOB 컬럼
VARCHAR(MAX)같은 LOB컬럼은 threshold가 낮다.
아래 예시에서는 행 1개 차이로 델타 스토어가 사용된다.
```sql
TRUNCATE TABLE dbo.DELTA_STORE_DUMPING_GROUND;
INSERT INTO dbo.DELTA_STORE_DUMPING_GROUND (STR1_MAX)
SELECT TOP (250) REPLICATE(STR1, 500)
FROM dbo.STAGING_TABLE
OPTION (MAXDOP 1);

INSERT INTO dbo.DELTA_STORE_DUMPING_GROUND (STR1_MAX)
SELECT TOP (251) REPLICATE(STR1, 500)
FROM dbo.STAGING_TABLE
OPTION (MAXDOP 1);

select row_group_id, delta_store_hobt_id, state_desc, total_rows
from sys.dm_db_column_store_row_group_physical_stats
```
|row_group_id|delta_store_hobt_id|state_desc|total_rows|
|---|---|---|---|
|1|NULL|COMPRESSED|251|
|0|72057594046251008|OPEN|250|

<br>
<br>

# 참고
<https://erikdarling.com/surprise-delta-stores/>