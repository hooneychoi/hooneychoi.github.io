---
title: 도커 활용 m1에 sql server 설치하기
date: 2024-03-23 13:15 +0900
author: hoon
category:
- SQL
tags:
- 설치
---
- [1. 도커 설치](#1-도커-설치)
- [2. Azure SQL Edge 설치](#2-azure-sql-edge-설치)
- [3. Azure Data Studio 설치](#3-azure-data-studio-설치)
- [4. 샘플 데이터베이스 설치](#4-샘플-데이터베이스-설치)
- [5. 도커 컨테이너로 복사](#5-도커-컨테이너로-복사)
- [6. 복원](#6-복원)
---

## 1. 도커 설치
## 2. Azure SQL Edge 설치
```shell
docker pull mcr.microsoft.com/azure-sql-edge
```
```shell
docker run --cap-add SYS_PTRACE -e 'ACCEPT_EULA=1' -e 'MSSQL_SA_PASSWORD=<password>' -p 1433:1433 --name <container name> -d mcr.microsoft.com/azure-sql-edge
```
## 3. Azure Data Studio 설치
**Server**: localhost

**User name**: sa

**Password**: 위에서 적은거

## 4. 샘플 데이터베이스 설치
[AdventureWorks 설치 링크](https://learn.microsoft.com/en-us/sql/samples/adventureworks-install-configure?view=sql-server-ver16&tabs=ssms)

## 5. 도커 컨테이너로 복사
```shell
docker cp AdventureWorks2017.bak azuresqledge:/var/opt/mssql/data
```

## 6. 복원
1. 오브젝트 익스플로러에서 우클릭
2. Restore Database(Preview) 클릭
3. Source - Restore from: `Backup file`
4. Backup file path: `/var/opt/mssql/data/AdventureWorks2017.bak`