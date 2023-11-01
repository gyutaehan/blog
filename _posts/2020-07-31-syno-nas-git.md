---
layout: post
comments: true
title: 시놀로지 나스 Git Server에 사용자가 나오지 않을 때 해결 방법
date: 2020-07-31 10:18:00
categories: dev
---

시놀로지나스에 관리자로 접속하여, SYNO.Git.lib 파일을 연다.

```
$ sudo -i
# vi /var/packages/Git/target/webapi/SYNO.Git.lib
```



appPriv라는 내용을 찾아서 공백으로 바꿔주면 해결!



##### Before
```
"appPriv": "SYNO.SDS.GIT.Instance" 
```

##### After
```
"appPriv": ""
```

---


참고: https://community.synology.com/enu/forum/10/post/135039