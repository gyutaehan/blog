---
layout: post
comments: true
title: VMware Fusion에서 아무 화면이 나오지 않는 현상(블랙스크린) 해결법
date: 2020-09-17 10:18:00
categories: dev
---

# 개요

![Screen-Shot-2020-09-17-at-14.52.08](http://localhost/content/images/2020/09/Screen-Shot-2020-09-17-at-14.52.08.png)
**macOS 카탈리나**으로 올리면 최신 버전이 아닌 **VMware fusion**에서 화면이 표시되지않는 문제가 발생함.
![Screen-Shot-2020-09-17-at-14.53.34](http://localhost/content/images/2020/09/Screen-Shot-2020-09-17-at-14.53.34.png)
과거 버전은 스크린 레코딩 접근 허용이 안됨.

# 해결법

## 리커버리 모드 접속
1. 컴퓨터를 **재부팅**하고 `CMD + R`을 눌러 **리커버리 모드**에 접속합니다.
2. 위 메뉴에서 터미널을 실행합니다.
3. 아래 명령어를 **실행**하고, 컴퓨터를 **재부팅**합니다.
   ```shell
   csrutil disable
   ```

## 컴퓨터를 재시동
1. 터미널을 열어 아래 네 개의 명령어를 실행합니다.
```shell
$ tccutil reset All com.vmware.fusion

$ sudo sqlite3 "/Library/Application Support/com.apple.TCC/TCC.db" 'insert into access values ("kTCCServiceScreenCapture", "com.vmware.fusion", 0, 1, 1, "", "", "", "UNUSED", "", 0,1565595574)'

$ sudo sqlite3 "/Library/Application Support/com.apple.TCC/TCC.db" 'insert into access values ("kTCCServiceListenEvent", "com.vmware.fusion", 0, 1, 1, "", "", "", "UNUSED", "", 0,1565595574)'

$ sudo sqlite3 "/Library/Application Support/com.apple.TCC/TCC.db" 'insert into access values ("kTCCServicePostEvent", "com.vmware.fusion", 0, 1, 1, "", "", "", "UNUSED", "", 0,1565595574)'
```

## 다시 리커버리 모드 접속
1. 컴퓨터를 **재부팅**하고 `CMD + R`을 눌러 **리커버리 모드**에 접속합니다.
2. 위 메뉴에서 터미널을 실행합니다.
3. 아래 명령어를 **실행**하고, 컴퓨터를 **재부팅**합니다.
   ```shell
   csrutil enable
   ```
# 확인
![Screen-Shot-2020-09-17-at-15.50.26](http://localhost/content/images/2020/09/Screen-Shot-2020-09-17-at-15.50.26.png)
**재부팅**하고 **VMware fusion**을 확인하면 제대로 화면이 나오는 것을 확인할 수 있고, 스크린 **레코딩**에도 VMware fusion이 있는 것을 확인할 수 있음.