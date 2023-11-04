---
title: RID Lookup VS Key Lookup
date: 2023-11-04 16:21 +0900
author: hoon
category:
- SQL
tags:
- 클러스터드인덱스
- 넌클러스터드인덱스
---
## 데이터 준비

```sql
CREATE TABLE TestTable (
    ID INT IDENTITY(1, 1)
    , First_Name VARCHAR(50)
    , Last_name VARCHAR(50)
    , Address VARCHAR(MAX)
    )

INSERT INTO TestTable VALUES ('AA','BB','CC')
GO 1000
INSERT INTO TestTable VALUES ('DD','EE','FF')
GO 1000
```

## 힙테이블 조회
```sql
SELECT *
FROM TestTable
```
![heap_scan](/assets/img/sql/heap_scan.png){width=300}

## 넌클러스터드 인덱스 생성
```sql
CREATE NONCLUSTERED INDEX NIX ON TestTable (ID)
```

### 인덱스가 있는데도 테이블 스캔이 발생
아마도 인덱스를 사용할 필요가 없어서 인듯.
```sql
SELECT *
FROM TestTable
```
![heap_scan](/assets/img/sql/heap_scan.png){width=300}

```sql
SELECT *
FROM TestTable
WHERE ID > 5
```
![heap_scan](/assets/img/sql/heap_scan.png)

### RID Lookup
인덱스의 키값을 조회하는 쿼리에서 드디어 인덱스가 사용됐다.
`SELECT`절에 `NIX`에 포함되지 않은 열(여기서는 전체)이 있으므로 나머지 값을 구하려면 데이터 페이지까지 도달해야한다.
인덱스의 포인터(RID)를 따라 데이터 페이지를 찾는 것이 RID Loopup이다.
```sql
SELECT *
FROM TestTable
WHERE ID = 5
```

![rid_lookup](/assets/img/sql/rid_lookup.png)

## 클러스터드 인덱스 생성
이제 이 테이블은 더이상 힙테이블이 아니다.
```sql
CREATE CLUSTERED INDEX CIX ON TestTable (ID)
```

```sql
SELECT *
FROM TestTable
WHERE ID = 5
```

![key_lookup](/assets/img/sql/key_lookup.png)

`SELECT`절에 `CIX`에 포함되지 않은 열(여기서는 전체)이 있지만, 어차피 조건절의 키값으로 데이터가 물리적으로 정렬되어 있어서 스캔이 아니라 검색이 사용됐다.