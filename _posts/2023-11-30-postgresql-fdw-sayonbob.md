---
layout: post
title: PostgreSQL FDW 사용법
description: 
- PostgreSQL DBLING, fdw 사용법
date: 2023-11-30 11:00:00
categories: dev
author: Gyutae Han
---

### dblink와 차이점

[이전 글:dblink](https://gyutaehan.github.io/blog/dev/2023/11/16/postgresql-dblink-sayonbob.html)에서 dblink로 했다.
둘 다 해본 결과, dblink는 테이블 하나하나씩 sql을 실행해서 링크하는 느낌이라면, fdw는 서로 연결해준다는 느낌이었다.
dblink는 접속할때마다 연결 실행이 필요했는데, fdw는 한번 처음에 설정해놓으면 다음부턴 아무것도 안해도 되는게 편했다.


개발 환경은 [이전 글:dblink](https://gyutaehan.github.io/blog/dev/2023/11/16/postgresql-dblink-sayonbob.html)와 같다.
### 환경

**서버 1**

연결 대상 서버

**서버 2**

연결 접속 서버


### FDW 설치

여기서부터는 기본적으로 서버2에서만 밑 과정을 진행하면 되고, 서버1은 아무것도 할 필요없다.

```sh
postgres=> create extension postgres_fdw;
```

### FDW 서버 생성

```sql
CREATE SERVER <서버이름> FOREIGN DATA WRAPPER postgres_fdw
OPTIONS (
    host '<서버1 ip주소>',
    dbname '<서버1 데이터베이스 이름>',
    port '<서버1 포트넘버>'
);
```

연결할 서버를 생성해준다.

### 유저 설정

```sql
CREATE USER MAPPING FOR <서버2 유저명> SERVER <서버이름>
OPTIONS (
    user '<서버1 유저명>',
    password '<서버1 패스워드>'
);
```

접속할 서버의 유저와, 매핑할 유저를 설정해준다.

### 스키마 연결

```sql
IMPORT FOREIGN SCHEMA <서버1 스키마 이름>
FROM SERVER <서버이름>
INTO <서버2 스키마 이름>;
```

연결할 스키마를 설정한다. 이것까지 실행하면 연결된 것을 확인할 수 있다.