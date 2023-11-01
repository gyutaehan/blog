---
layout: post
title: 깃허브 초보가 써보는 GitHub CLI 1.0
date: 2020-09-22 10:18:00
categories: dev
---

2020년 9월 16일 릴리즈된 GitHub 커맨드라인, [**GitHub CLI**](https://github.blog/2020-09-17-github-cli-1-0-is-now-available/)가 릴리즈 되었습니다. 기존 GitHub에서 하던 작업들을 커맨드라인에서 할 수 있다고 하는데, GitHub 생초짜인 제가 한 번 써보았습니다.

## INSTALL

**Windows**, **Linux**, **Mac**에서 지원을 하며 설치 방법은 [CLI](https://github.com/cli/cli#installation-and-upgrading)에서 확인할 수 있습니다.
**MacOS**기준으로 **Homebrew**나 **MacPorts**를 이용하여 설치가 가능합니다.
```shell
# Homebrew 인 경우
$ brew install gh
$ brew upgrade gh

# MacPorts 인 경우
$ sudo port install gh
$ sudo port selfupdate && sudo port upgrade gh
```

명령어는 `gh`이며 현재 버전은 **1.0.0**입니다.
```shell
$ gh version

# output
gh version 1.0.0 (2020-09-16)
https://github.com/cli/cli/releases/tag/v1.0.0
```



# 그래서 이걸로 무엇이 가능한 걸까??

**GitHub**에서 하는거야.. 뭐 푸시나 풀 그게 뭐 다 아니야? 그건 **Git**에서 다 가능한거잖아. 그래서 이걸로 대체 뭐가 가능한건데? 라는 의문이 있었기 때문에 그것을 해결하기 위해 [공식 문서](https://cli.github.com/manual/gh_pr_checks)에서 **명령어**들을 살펴보았습니다.

- **ADDITIONAL COMMANDS**
  1. [Auth](#1.-auth)
  2. [Alias](#2.-alias)
  3. [API](#3.-api)
  4. [Completion](#4.-completion)
  5. [Config](#5.-config)
  6. [Help](#6.-help)

- **CORE COMMANDS**
  1. [Repo](#1.-repo)
  2. [Gist](#2.-gist)
  3. [Issue](#3.-issue)
  4. [PR](#4.-pr)
  5. [Release](#5.-release)

---

## ADDITIONAL COMMANDS



## 1. Auth
깃허브의 **인증 관련**을 관리하는 명령어입니다.
- **Login**
  로그인합니다.
- **Logout**
  로그아웃합니다.
- **Refresh**
  인증 정보를 새로고침합니다.
- **Status**
  인증 상태를 확인합니다.
---

### Login
[GitHub.com](https://github.com)뿐만 아니라 [GitHub Enterprise Server](https://github.com/enterprise)에도 연결이 가능하며, [토큰을 발급](https://docs.github.com/en/github/authenticating-to-github/creating-a-personal-access-token)받거나 브라우저에서 로그인이 가능함.
```shell
# 로그인 명령어
$ gh auth login 

# output
...
✓ Configured git protocol
✓ Logged in as < 계정 이름 >
```

### Logout
```shell
# 로그아웃 명령어
$ gh auth logout

# output
? Are you sure you want to log out of github.com account < 계정 이름 >? Yes
✓ Logged out of github.com account < 계정 이름>
```

### Refresh
```shell
# 래플래시 명령어
$ gh auth refresh

# output
✓ Authentication complete. Press Enter to continue...
```

### Status
```sh
# 상태 명령어
$ gh auth status

# output
github.com
✓ Logged in to github.com as < 계정 이름 > (~/.config/gh/hosts.yml)
✓ Git operations for github.com configured to use https protocol.
```



## 2. Alias
`gh명령어`의 알리아스 관련 명령어입니다.
- **Delete**
  알리아스를 삭제합니다.
- **List**
  알리아스들의 리스트를 표시합니다.
- **Set**
  알리아스를 생성합니다.

---

### Login
```shell
# gh alias delete <알리아스> [flags]
$ gh alias delete pv

# output
✓ Deleted alias pv; was pr view
```

### List
```shell
$ gh alias list

# output
co:  pr checkout
pv:  pr view
```

### Set

```shell
# gh alias set <알리아스> <expansion> [flags]
$ gh alias set pv 'pr view'

# output
- Adding alias for pv: pr view
✓ Added alias.

=>
# gh pr view를 gh pv로 알리아스 설정이 가능해짐.
```



## 3. API
 **GitHub API**에 인증된 HTTP 요청을 하고 응답을 표시합니다.

---
```shell
$ gh api repos/:owner/:repo/releases 

# output
# JSON 형식으로 응답을 받음
```



## 4. Completion
 **GitHub CLI** 명령어에 대한 지정된 자동완성 쉘 스크립트를 생성합니다.

---

```shell
$ gh completion -s zsh   

# output
# zsh 쉘 스크립트가 생성이 됨
```



## 5. Config
 **GitHub CLI** 세팅들을 관리합니다.
- **Get**
  알리아스를 삭제합니다.
- **Set**
  알리아스들의 리스트를 표시합니다.
---

### Get
```shell
# git 프로토콜에 설정에 대해 표시합니다.
$ gh config get git_protocol

# output
https
```

### Set
```shell
# git 에디터에 대해여 vim으로 설정합니다.
$ gh config set editor vim   
```



## 6. Help
 **GitHub CLI** 명령어의 도움말을 표시합니다.

---
```shell
# --help 옵션을 주는 것도 가능함.
$ gh help alias

# output
Aliases can be used to make shortcuts for gh commands or to compose multiple commands.

Run "gh help alias set" to learn more.


USAGE
  gh alias [flags]

CORE COMMANDS
  delete:     Delete an alias
  list:       List your aliases
  set:        Create a shortcut for a gh command

INHERITED FLAGS
  --help   Show help for command

LEARN MORE
  Use 'gh <command> <subcommand> --help' for more information about a command.
  Read the manual at https://cli.github.com/manual
```



## CORE COMMANDS

## 1. Repo
**깃허브**의 **리포지토리**를 **관리**하는 명령어입니다.

이 명령어를 보자마자 git과의 차이점을 느꼈는데 git으로는 당연하겠지만 깃허브의 리포지토리까지는 생성이 불가능하니까요. 
- **Create**
  새로운 리포지토리를 생성합니다.
- **Clone**
  리포지토리를 클론합니다.
- **Fork**
  포크를 생성합니다.
- **View**
  리포지토리를 확인합니다.

---

### Create
```shell
# gh-test라는 이름의 리포지토리를 생성한다.
$ gh repo create gh-test 

# output
? This will create 'gh-test' in your current directory. Continue?  Yes
✓ Created repository < 계정 이름 >/gh-test on GitHub
✓ Added remote https://github.com/< 계정 이름 >/gh-test.git
```

[GitHub](https://github.com/)에 접속해보면 리포지토리가 만들어진 것을 확인할 수 있다.

### Clone
```shell
# 위에서 만든 리포지토리를 로컬로 클론한다.
$ gh repo clone https://github.com/trapkka997/gh-test 

# output
Cloning into 'gh-test'...
warning: You appear to have cloned an empty repository.
```

### Fork
```shell
# 다른 리포지토리를 fork한다.
$ gh repo fork https://github.com/cli/cli

# output
✓ Created fork trapkka997/cli
? Would you like to clone the fork? Yes
...
✓ Cloned fork
```

### View
```shell
# .git 파일이 존재하는 디렉토리에서 실행한다.
$ gh repo view

# output
리포지토리를 확인할 수 있다.
```



## 2. Gist
깃허브의 **gist를 관리**하는 명령어입니다.
- **Create**
  새로운 gist를 생성합니다.
- **Edit**
  gist를 편집합니다.
- **List**
  gist들의 리스트를 표시합니다.
- **View**
  특정한 gist를 표시합니다.
---

### Create
```shell
# gist-test.txt파일을 gist에 생성을 한다.
$ gh gist create gist-test.txt -d "gh-gist-test"

# output
- Creating gist gist-test.txt
✓ Created gist gist-test.txt
```

Gist가 생성이 되었는지 생성이 된 것을 확인할 수 있다.
<script src="https://gist.github.com/trapkka997/81c72373800ab970379c2732a7fe7d0e.js"></script>

### Edit
```shell
# 편집 명령어
$ gh gist edit {<gist ID> | <gist URL>}

# output
편집할 수 있는 NANO같은 에디터가 표시가 됨.
```

### List
```shell
# 리스트 명령어 * ls는 되지 않습니다.
$ gh gist list

# output
<gist ID>  gh-gist-test  1 file  secret  about 2 minutes ago
```

### view
```shell
# 확인 명령어
$ gh gist list {<gist id> | <gist url>}

# output
gh-gist-test
gist 테스트입니다.
```

## 3. Issue
깃허브의 **issue를 관리**하는 명령어입니다.

- **Create**
  새로운 issue를 생성합니다.
- **Close**
  issue를 클로즈합니다.
- **List**
  현재의 리포지토리에 이슈들의 리스트를 표시합니다.
- **Reopen**
  issue를 다시 open합니다.
- **Status**
  관련된 issue의 상태를 확입니다.
- **View**
  특정한 issue를 표시합니다.
---

### Create
```shell
# .git파일이 존재하는 디렉토리에서 실행한다.
$ gh issue create --title "I found a bug" --body "Nothing works"

# output
Creating issue in trapkka997/gh-test
https://github.com/trapkka997/gh-test/issues/1
```
이슈가 생성된 것을 확인할 수 있다.
![gh1](http://localhost/content/images/2020/09/gh1.png)

### Close
```shell
# .git파일이 존재하는 디렉토리에서 실행한다.
$ gh issue close {<number> | <url>}

# output
Closed issue #1 (I found a bug)
```

이슈가 클로즈 된 것을 확인할 수 있다.
![gh2](http://localhost/content/images/2020/09/gh2.png)

### List
```shell
# .git파일이 존재하는 디렉토리에서 실행한다.
$ gh issue list -s all 

# output
Showing 1 of 1 issue in trapkka997/gh-test that matches your search
#1  I found a bug    about 15 minutes ago
```

### Reopen
```shell
# .git파일이 존재하는 디렉토리에서 실행한다.
$ gh issue reopen {<number> | <url>} [flags]

# output
✔ Reopened issue #1 (I found a bug)
```

### status
```shell
# .git파일이 존재하는 디렉토리에서 실행한다.
$ gh issue status

# output
Relevant issues in trapkka997/gh-test

Issues assigned to you
  There are no issues assigned to you

Issues mentioning you
  There are no issues mentioning you

Issues opened by you
  #1  I found a bug    about 5 minutes ago
```

### view
```shell
# .git파일이 존재하는 디렉토리에서 실행한다.
$ gh issue view {<number> | <url>} [flags]

# output
I found a bug
Open • trapkka997 opened about 44 minutes ago • 0 comments
  Nothing works                                                               
View this issue on GitHub: https://github.com/trapkka997/gh-test/issues/1
```

## 4. PR
깃허브의 **풀, 리퀘스트를 관리**하는 명령어입니다. 
- **Checkout**
  체크아웃 합니다.
- **Checks**
  CI 상태를 표시합니다.
- **Close**
  현재의 리포지토리에 이슈들의 리스트를 표시합니다.
- **Create**
  pr을 생성합니다.
- **Diff**
  변화한 내용을 표시합니다.
- **List**
  pr들의 리스트를 표시합니다.
- **Merge**
  merge 합니다.
- **Ready**
  ready상태로 만듭니다.
- **Reopen**
  pr을 Reopen합니다.
- **Review**
  pr에 리뷰를 추가합니다.
- **View**
  특정한 pr을 표시합니다.
---

### Checkout
```shell
# .git파일이 존재하는 디렉토리에서 실행한다.
$ gh pr checkout {<number> | <url> | <branch>} [flags]

# output
Switched to branch 'trapkka997'
Your branch is up to date with 'origin/trapkka997'.
Already up to date.
```

### Checks
```shell
# .git파일이 존재하는 디렉토리에서 실행한다.
$ gh pr checks

# output
```

### Close
```shell
# .git파일이 존재하는 디렉토리에서 실행한다.
$ gh pr close {<number> | <url> | <branch>} [flags]

# output
✔ Closed pull request #3 (두 번째 pr)
```

### Create
```shell
# .git파일이 존재하는 디렉토리에서 실행한다.
$ gh pr create

# output
? Title The bug is fixed!
? Body <Received>
? What's next? Submit
https://github.com/trapkka997/gh-test/pull/2
```

pr이 생성된 것을 확인할 수 있다.
![gh3](http://localhost/content/images/2020/09/gh3.png)

### Diff
```shell
# .git파일이 존재하는 디렉토리에서 실행한다.
$ gh pr diff [<number> | <url> | <branch>] [flags]

# output
diff --git a/README.md b/README.md
index 3682bf3..272afa4 100644
--- a/README.md
+++ b/README.md
@@ -1,4 +1,4 @@
-# GH-TEST
+## GH-TEST

 GitHub CLI
--- add ghb
\ No newline at end of file
+깃허브 CLI

```

### List
```shell
# .git파일이 존재하는 디렉토리에서 실행한다.
$ gh pr list

# output
Showing 1 of 1 open pull request in test-gth/test-gh
#1  help this  trapkka997:trapkka997
(END)
```

### Merge
```shell
# .git파일이 존재하는 디렉토리에서 실행한다.
$ gh pr merge

# output
? What merge method would you like to use? Create a merge commit
? Delete the branch locally and on GitHub? Yes
✔ Merged pull request #2 (The bug is fixed!)
✔ Deleted branch ghb and switched to branch master
```

### Ready
```shell
# .git파일이 존재하는 디렉토리에서 실행한다.
$ gh pr ready [<number> | <url> | <branch>] [flags]

# output
```

### Reopen
```shell
# .git파일이 존재하는 디렉토리에서 실행한다.
$ gh pr reopen {<number> | <url>} [flags]

# output
✔ Reopened pull request #3 (두 번째 pr)
```

### Review
```shell
# .git파일이 존재하는 디렉토리에서 실행한다.
$ gh pr review {<number> | <url>} [flags]

# output
? What kind of review do you want to give? Request changes
? Review body <Received>
Got:
  이상한데요..﻿                                                                
? Submit? Yes
+ Requested changes to pull request #4
```

**Comment**, **Approve**, **Request Changes**를 선택하여 리뷰를 남길 수 있습니다.

### view
```shell
# .git파일이 존재하는 디렉토리에서 실행한다.
$ gh pr view {<number> | <url>} [flags]

# output
Open • trapkka997 wants to merge 1 commit into master from trapkka997
  oops<U+FEFF>                                                                        
View this pull request on GitHub: https://github.com/test-gth/test-gh/pull/1
```



## 5. Release

깃허브의 **릴리즈를 관리**하는 명령어입니다.
- **Create**
  새로운 릴리즈를 생성합니다.
- **Delete**
  릴리즈를 삭제합니다.
- **Download**
- **List**
- **Upload**
- **view**
  릴리즈의 대한 정보를 표시합니다.
---

### Create
```shell
# .git파일이 존재하는 디렉토리에서 실행한다.
$ gh release create v1.0.1   

# output
? Title (optional) Release v1.0.1 !
? Release notes Write my own
? Is this a prerelease? Yes
? Submit? Publish release
https://github.com/trapkka997/gh-test/releases/tag/v1.0.1
```

릴리즈가 생성된 것을 확인할 수 있다.
![gh4](http://localhost/content/images/2020/09/gh4.png)

### Delete
```shell
# .git파일이 존재하는 디렉토리에서 실행한다.
$ gh release delete <tag> [flags]

# output
Closed issue #1 (I found a bug)
```

### Downolad
```shell
# .git파일이 존재하는 디렉토리에서 실행한다.
$ gh release download [<tag>] [flags]

# output asset이 다운로드 됨.
```

### List
```shell
# .git파일이 존재하는 디렉토리에서 실행한다.
$ gh release list [flags]

# output
Release v1.0.1 !  Pre-release  (v1.0.1)  about 4 hours ago
```

### Upload
```shell
# .git파일이 존재하는 디렉토리에서 실행한다.
$ gh release upload <tag> <files>... [flags]

# output
Successfully uploaded 1 asset to v1.0.1
```

### View
```shell
# .git파일이 존재하는 디렉토리에서 실행한다.
$ gh release view [<tag>] [flags]

# output
v1.0.1
Pre-release • trapkka997 released this about 4 hours ago
  beta﻿                                                                        
Assets
test  12 B
View on GitHub: https://github.com/trapkka997/gh-test/releases/tag/v1.0.1
```

# 결론

**GitHub CLI**명령어들을 하나씩 써가면서 **git**과의 차이점을 생각해봤는데, 써보니까 느낀게 결국 **GitHub**에서 제공하는 기능들, 즉 **GitHub**에서만 가능한 것들 전부였습니다.
**Git**명령어로 **GitHub**에 있는 기능들을 쓰지 못하고, 물론 **GitHub CLI**명령어가 **Git**명령어을 대체할 수도 없고 양쪽 전부 써야하는 것을 느꼈습니다.(**Git**은 **Git**이고 **GitHub**는 **GitHub**였습니다.)
CLI를 이용하여 **GitHub**를 다룰 수 있다는 건, **GitHub**도 프로그램으로 인한 자동화가 된다는 것이고 더더욱 간편해질 것이라고 생각되네요.