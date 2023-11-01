---
layout: post
title: IBM Cloud Essentials V2 수강 후기
date: 2021-11-06 10:18:00
categories: dev
---

# Synology NAS에서 Ghost 블로그 만들기(Without Docker)

### **※서론**

현재 이 블로그는 매달 5달러를 내면서 유지하고 있는데, 어느날 문득 생각이 들었습니다. 



> 집에서 사용하고 있는 나스에 Ghost 블로그를 설치하면 5달러를 내지 않고도 사용할 수 있지 않을까?



지금 제가 가지고 있는 Synology NAS의 기종은 DS216J로 32bit에 armv7를 가진 상당히 오래되고 병든(?) 녀석입니다.

당연하게도 Docker는 지원하지 않고, Docker로 Ghost를 설치하는 것은 불가능 하였습니다.



그래서 결국은 직접 Node를 이용하여 설치하기로 했습니다.



### **※개발 환경**

Synology NAS DS216(32bit armv7 debian) DSM4.1



### **※사전 환경**

![1](http://localhost/content/images/2021/11/1.jpg)

DSM 4.1 (3버전도 괜찮습니다. 오히려 현재 4버전은 호환이 좋지 않아 3버전이 더 좋을 수도 있습니다.)

MariaDB 10 (MariaDB 5버전도 됩니다.)

Node 12 (Node14버전도 될 거 같지만 확인은 안해보았습니다.)



### **※설치 시작**

먼저 ssh에 접속한 후, 마리아DB의 설정부터 해줍니다.

```shell
# root로 접속
$ mysql -u root -p
Enter password:
... 중략
Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.

# mysql 데이터베이스를 사용
MariaDB [(none)]> use mysql;
Database changed

# ghost_db라는 데이터베이스를 생성
MariaDB [mysql]> create database ghost_db;
Query OK, 1 row affected (0.001 sec)

# 유저를 생성
MariaDB [mysql]> create user 유저명@localhost identified by '패스워드';
Query OK, 0 rows affected (0.082 sec)

# 유저에게 ghost_db 데이터베이스 권한 부여
MariaDB [mysql]> GRANT ALL privileges ON ghost_db.* TO '유저명'@'localhost';
Query OK, 0 rows affected (0.012 sec)
```

**데이터베이스이름**은 **ghost_db**라고 해주었지만 자유롭게 생성해주면 됩니다. 나중에 세팅하는 곳이 있는데 거기서 잘 입력만 해주면 괜찮습니다.

그 다음은 **Node**로 **Ghost-cli**를 설치합니다.



```shell
# npm 업데이트
$ sudo npm update
$ sudo npm upgrade
$ sudo npm i -g npm
added 70 packages from 18 contributors

# ghost-cli 설치
$ sudo npm install -g ghost-cli@1.17.4
added 415 packages, and audited 416 packages in 2m
```

여기서 가장 중요한 부분은 바로 **ghost-cli**를 **1.17.4**를 설치해주는 것 입니다.

시놀로지 나스의 node버전이 최신 버전이 아니기 때문에 터질 확률이 매우매우 높기 때문에 (제 경험상....) 1.17.4버전을 설치할 것을 권장드립니다. 



이제부터 Ghost블로그의 설치를 시작합니다.

```shell
# 편한 곳에 Ghost블로그를 설치할 폴더를 생성
$ mkdir ghost; cd ghost

# ghost를 설치합니다.
~/ghost$ ghost install 3.42.7 local --db=mysql
You are running an outdated version of Ghost-CLI.
It is recommended that you upgrade before continuing.
Run `npm install -g ghost-cli@latest` to upgrade.

✔ Checking system Node.js version - found v12.21.0
✔ Checking current folder permissions
✔ Checking memory availability
✔ Checking free space
✔ Checking for latest Ghost version
✔ Setting up install directory
✔ Downloading and installing Ghost v3.42.7
✔ Finishing install process
# 밑은 mysql 설정입니다. 위에서 mysql에서 설정한 유저명과 비밀번호, 디비명을 입력하시면 됩니다.
? Enter your MySQL hostname: localhost
? Enter your MySQL username: ghost_user
? Enter your MySQL password: [hidden]
? Enter your Ghost database name: ghost_db
✔ Configuring Ghost
✔ Setting up instance
✖ Starting Ghost
Message: connect ECONNREFUSED 127.0.0.1:3306
Help: Unknown database error
...이하 에러 발생
```

여기서 중요한 체크포인트는 **두 가지**인데,



**첫 번째**는 4버전이 아닌 3버전의 가장 최신 버전인 **3.42.7**버전을 설치받았습니다. 그 이유는 위의 ghost-cli와 마찬가지로 노드버전이 낮기 때문에 4버전은 설치가 안 될 가능성이 매우매우 높습니다.



**두 번째**는 DB를 **mysql**로 지정한 것입니다. 아무것도 지정하지 않으면 기본값은 sqllite3이 되는데 노드버전과 OS의 문제로 이것도 터질 가능성이 높습니다. 그래서 시놀로지 패키지에서 설치할 수 있는 mysql(MariaDB)를 사용하는 것을 권장드립니다.



마지막에 잘되나 싶었는데 실행에서 에러가 발생했습니다.

![2](http://localhost/content/images/2021/11/2.jpg)

sql에러인데, 이것은 MariaDB 10 패키지를 열어서 3306 포트의 TCP통신을 체크하면 해결됩니다. 



```shell
# Ghost 블로그 실행
~/ghost$ ghost start
Found a development config but not a production config, running in development mode instead
✔ Checking system Node.js version - found v12.21.0
ℹ Ensuring user is not logged in as ghost user [skipped]
ℹ Checking if logged in user is directory owner [skipped]
✔ Checking current folder permissions
✔ Validating config
✔ Checking memory availability
✔ Checking binary dependencies
✔ Starting Ghost: ghost-local

------------------------------------------------------------------------------

Your admin interface is located at:

    http://localhost:2368/ghost/
```

그리고 다시 폴더로 돌아와서 **ghost start** 명령어로 정상정으로 실행을 할 수 있습니다.



```shell
# curl로 서버 실행 확인
$ curl  http://localhost:2368/ghost/
<!doctype html>
<!--[if (IE 8)&!(IEMobile)]><html class="no-js lt-ie9" lang="en"><![endif]-->
<!--[if (gte IE 9)| IEMobile |!(IE)]><!--><html class="no-js" lang="en"><!--<![endif]-->
<head>
... 이하 생략
```

**curl**명령어로 서버가 열려있는 것을 확인 할 수 있습니다.



### **※포트 열기**

자 그럼 로컬에서 curl로 확인했겠다, 인터넷 브라우러로 확인하려면 포트를 여는 작업이 필요합니다.

**DSM4** 기준으로 Login Portal -> Advanced -> Reverse Proxy 에서 나스 내부 IP를 외부에서 접속할 수 있도록 설정이 가능합니다.

(DSM3에도 있는 기능입니다.)

![3](http://localhost/content/images/2021/11/3.jpg)

그림과 같이 Ghost블로그의 서버 포트인 2368을 임의의 IP포트 8888로 변경하여 save버튼을 누른후, 

https://나스IP:8888/ghost/ 에 접속을 해봅니다.


![4](http://localhost/content/images/2021/11/4.jpg)

축하드립니다. 이상으로 NAS에 Ghost블로그를 올리는 것이 되었습니다.



### **※이것저것**

1. 이걸 위해 DSM3도 갔다가 DSM4도 갔다가 나와있는 Ghost버전을 전부 설치도 해보았고, 데비안chroot를 설치하여 gcc를 설치하는 등 엄청난 삽질과 삽질을 하였는데, 되는 걸 보니 뿌듯하네요. 여기서 더 나아가서 서비스에 등록하거나 NginX 설정을 하거나 같은 귀찮은 작업들이 있는데, 그건 귀찮아서 생략합니다. 

2. DNS서버를 추가하실 분들은 Reverse Proxy에서 호스트네임을 DNS서버로 설정을 하고 포트를 443으로 하면 됩니다. 그리고 ghost폴더에 ssh접속을 해서 `ghost setup` 명령어로 도메인을 DNS서버로 설정하고 ghost서버를 재시작하면 DNS서버로 접속을 할 수 있습니다.

3. MariaDB 5버전을 설치할 경우, 컬럼 길이가 초과되었다는 에러가 발생할 수도 있습니다. 그것은 구글검색으로 바로 해결하였는데요, 에러 내용이 기억나질 않아서 생략합니다. 에러 내용 그대로 복붙하면 맨 위에 나오는 게시글로 해결하였습니다. (10버전은 에러가 없네요)

4. 야호! 근데 이걸로 대체 하려면 나스를 계속 켜놓아야하고 고장도 없어야하고 네트워크 관리도 해야하고.. 그냥 5달러씩 주면서 클라우드 쓰는게 편할지도..