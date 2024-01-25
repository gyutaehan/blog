---
layout: post
title: ios(safari)에서 로딩중 새로고침을 했을 때 에러발생
description: 
- ios, safari, 로딩중 새로고침, api통신중 새로고침, 에러
date: 2024-01-25 13:00:00
categories: dev
author: Gyutae Han
---

![Error404](/blog/assets/img/2024/01/25/err404.png)

*위의 화면은 본 내용과 관계없음.*

### 에러개요

리액트 개발중 safari환경에서 서버에 axios통신중에 새로고침이나 돌아가기 버튼처럼 브라우저 조작을 하면 클라이언트에서 에러가 발생하는 것을 확인.

크롬에선 일어나지 않고, 사파리에서 발생하는 것을 확인하였다.



### 원인

safari에서는 response가 돌아오기전에 브라우저 조작을 하면 에러가 발생한다고 한다. 

10년전 글에서도 마찬가지 였다는 글을 발견했기에, 아마 고쳐지지 않을 것같다.




### 해결방법

에러 처리를 추가해서 따로 에러가 발생하지않도록 제어를 할 수 밖에 없는 것 같다.

나는 axios 에러와 network에러인 경우에는 새로고침이 되도록 제어하였다

