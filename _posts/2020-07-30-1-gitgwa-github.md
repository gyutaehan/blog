---
layout: post
comments: true
title: 1. 깃허브에 프로젝트를 올려보자.
date: 2020-07-30 10:18:00
categories: dev
---

# 1. Git

Git은 형상관리도구중 하나로, SVN과 함께 전세계적으로 인기가 많은 소프트웨어입니다.
저희 회사에선 SVN을 사용하지만, SVN보다는 Git을 사용하는 추세입니다.


기본적으로 Git의 흐름에 대해선 [Oliver Steele](https://blog.osteele.com/2008/05/my-git-workflow/) 님께서 작성한 그림을 확인해보면,



![gitflow](https://images.osteele.com/2008/git-transport.png)



**workspace**, **index**, **local** **repository**, **remote** **repository**가 나누어져 있습니다.

**workspace**(작업공간)는 직접 사용자가 작업하는 공간입니다.
**local repository**(로컬 저장소)Git서버에 실제로 Commit되는 데이터가 들어갑니다.
**index**(스테이징 영역)는 스테이징된 파일들이 들어가게됩니다. 스테이징 된 파일들을 Commit을 하면 원격 저장소에 올라가게 됩니다.
**remote repository**(원격 저장소)는 인터넷이나 네트워크 어디엔가 있는 원격 저장소입니다. 그렇다고 해서, 로컬에도 원격저장소를 구축할 수 없는 것은 아닙니다. 여기선 Github를 원격저장소로 이용할 생각입니다.



### Git 설치

https://git-scm.com/downloads 에서 설치할 수 있습니다.
자신의 운영체제에 맞는 버전을 다운로드 하여 설치하시면 됩니다.



### Git 로컬 서버에 커밋하기

```
안녕하세요!
테스트 파일입니다!
```

git-test 폴더 안에 test.txt라는 파일을 작성하였습니다.
전 VSCode의 터미널을 이용하였지만,
윈도우라면 커맨드프롬프트나 PowerShell, 리눅스나 맥이라면 터미널을 이용하시면 됩니다.

아래의 명령어를 이용하여 Git로컬서버에 커밋을 합니다.

```
# /.git/ 폴더를 생성
git-test $ git init

# add를 이용하여, 스테이징 영역에 test.txt를 스테이징한다.
git-test $ git add test.txt

# 스테이징된 내용을 로컬 저장소에 커밋한다.
git-test $ git commit -m "첫 번째 커밋"
```


### 깃 서버에 있는 내용을 깃허브에 올리기

[깃허브](https://github.com/)에 접속하여 계정 생성 및 로그인을 해줍니다.
우 상단에 위치한 +를 눌러 New repository를 클릭해줍니다.
리포지토리 이름과 설명을 입력해주고 리포지토리를 생성합니다.
생성을 하게되면 다음과 같은 창이 나오는데 복사를 합니다.

```
# 원격 저장소를 지정해준다.
# 예를들면, git remote add origin https://github.com/trapkka997/git-test.git
git-test $ git remote add origin {위에서 복사한 리포지토리 주소}

# 로컬 저장소에 있는 내용을 원격 저장소에 푸시한다.
git-test $ git push -u origin master

# origin : 저장소 이름 
# master : 브랜치 이름 
```

*여담이지만 브랜치 이름의 "master"가 master/slave 같은 노예제도 용어라서 그걸 없애고 "main"으로 바꾸겠다는 [기사](https://www.bbc.com/news/technology-53050955)가 있었는데, 그게 언제가 될지는 모르겠습니다.*


깃허브 저장소를 확인해보면, 위와 같이 test.txt가 푸시 된 것을 확인할 수 있습니다.