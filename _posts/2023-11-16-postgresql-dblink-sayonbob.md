---
layout: post
title: PostgreSQL DBLink로 view만들기
description: 
- PostgreSQL DBLING, 디비링크 사용법, View만들기
date: 2023-11-16 11:00:00
categories: dev
author: Gyutae Han
---

서버2의 DB에서 서버1의 DB를 링크해서 view를 만들어 보자.
### 환경

**서버 1**

테이블명 myschema.accounts

| user_id | serial |
| ---- | ---- |
| username | character varying(50) |
| password | character varying(50) |
| email | character varying(255) |
| created_on | timestamp(6) without time zone |
| last_login | timestamp(6) without time zone |


**서버 2**

아무것도 없는 상태


### dblink설치

여기서부터는 기본적으로 서버2에서만 밑 과정을 진행하면 되고, 서버1은 아무것도 할 필요없다.

```sh
postgres=> create extension dblink;
```

### dblink커넥션 생성

```sql
SELECT dblink_connect('<커넥션 이름>', 'dbname=mydb port=<서버1 포트넘버> host=<서버1 ip주소> user=<서버1 유저명> password=<서버1 패스워드>');
```

연결을 위한 커넥션을 생성해준다. 없어도 되는데 나중에 귀찮으니 만드는게 편하다.

### dblink 연결확인

```sql
select * 
 from dblink('<커넥션 이름>','select * from myschema.accounts') 
  as t1 (user_id int, username varchar(50), password varchar(50), email varchar(255), created_on date, last_login date);
```

위에서 생성한 커넥션이름 첫번째 인자에 넣고, 두 번째엔 검색할 sql, 그리고 as뒤에는 알리아스랑 테이블 정의를 집어넣는다. 
자세히는 모르겠는데, 대충 문자열은 varchar로, 날짜는 date로 하니까 에러없이 표시되었다.

### dblink view로 연결

```sql
CREATE VIEW view_name AS
  SELECT *
    FROM dblink('<커넥션 이름>','select * from myschema.accounts')
     as t1 (user_id int, username varchar(50), password varchar(50), email varchar(255), created_on date, last_login date);
```

위에서 연결한것 그대로 복사해와서 create view로 view를 만들어주면 되고, 서버1에 view가 만들어진것을 확인할 수 있다.