---
title: 도커 활용 m1에 sql server 설치하기
date: 2024-03-23 13:15 +0900
author: hoon
category:
- SQL
tags:
- 설치
---
## 도커 설치
## Azure SQL Edge 설치
```shell
docker pull mcr.microsoft.com/azure-sql-edge
```
```shell
docker run --cap-add SYS_PTRACE -e 'ACCEPT_EULA=1' -e 'MSSQL_SA_PASSWORD=<password>' -p 1433:1433 --name <container name> -d mcr.microsoft.com/azure-sql-edge
```
## Azure Data Studio 설치
**Server**: localhost

**User name**: sa

**Password**: 위에서 적은거