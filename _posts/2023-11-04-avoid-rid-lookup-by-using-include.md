---
title: RID Lookup을 Index seek으로 개선
date: 2023-11-04 17:34 +0900
author: hoon
category:
- SQL
tags:
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

```sql
CREATE NONCLUSTERED INDEX NIX ON TestTable (ID)
```
## RID Lookup
```sql
SELECT Id, First_Name
FROM TestTable
WHERE ID = 5
```

![rid_lookup](/assets/img/sql/rid_lookup2.png){: width="400"}

## INCLUDE
```sql
CREATE NONCLUSTERED INDEX NIX2 ON TestTable (ID) INCLUDE (First_Name)
```
```sql
SELECT Id, First_Name
FROM TestTable
WHERE ID = 5
```
![index_seek](/assets/img/sql/index_seek.png){: width="400"}
인덱스 파일에 저 컬럼의 데이터가 물리적으로 저장되었기 때문에 데이터 리프까지 갈 필요가 없어서 쿼리가 개선됐다.