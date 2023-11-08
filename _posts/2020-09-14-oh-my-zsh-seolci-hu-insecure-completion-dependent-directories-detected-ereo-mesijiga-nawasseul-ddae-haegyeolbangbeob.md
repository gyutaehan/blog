---
layout: post
comments: true
title: oh-my-zsh 설치 후 Insecure completion-dependent directories detected 에러 메시지가 나왔을 때 해결방법
description: 
- oh-my-zsh 설치 후 에러 메시지 해결방법
- Insecure completion-dependent directories detected
date: 2020-09-14 10:18:00
categories: dev
author: Gyutae Han
---

# 에러 내용

oh-my-zsh 설치 후, 아래와 같은 경고 메시지가 나온다.

```shell
Insecure completion-dependent directories detected
...
```



# 해결 방법

퍼미션 문제가 있는 디렉토리를 찾는다.
```shell
$ compaudit

# output
/usr/local/share/zsh     
/usr/local/share/zsh/site-functions 
```


문제가 있는 디렉토리를 퍼미션 설정을 해준다.
```shell
$ chmod 755 /usr/local/share/zsh     
$ chmod 755 /usr/local/share/zsh/site-functions 
```

터미널을 다시 껐다가 켜면 에러메시지가 없어진 것을 확인할 수 있다.
![Screen-Shot-2020-09-14-at-0.02.14](http://localhost/content/images/2020/09/Screen-Shot-2020-09-14-at-0.02.14.png)