---
layout: post
title: Code Engine으로 쉽고 빠르게 배포하기
date: 2020-09-23 15:18:00
categories: dev
---

[IBM Code Engine](https://cloud.ibm.com/docs/codeengine?topic=codeengine-getting-started)은 컨테이너된 워크로드를 실행하는 **서버리스 플랫폼**입니다. 이러한 워크로드는 동일한 쿠버네티스 인프라 위에서 호스팅이 되어지고 있기 때문에 모두 원활하게 작동이 가능합니다.



# Code Engine 특징

[공식문서 원문](https://cloud.ibm.com/docs/codeengine?topic=codeengine-about)에 따르면 특징은 아래와 같습니다.

| 특징                          | 설명                                                         |
| ----------------------------- | ------------------------------------------------------------ |
| 워크로드를 실행한다.          | Code Engine은 HTTP 기반 앱과 run-to-completiton(완료할 때까지 끼어들지 못하는) 배치 작업을 실행합니다. |
| 완전 관리형 서비스            | Code Engine은 서버 프로비저닝, 구성, 확장 및 관리를 포함한 모든 클러스터 관리를 관리하므로 기본 인프라에 대해 걱정할 필요가 없습니다. |
| 코드를 빌드한다.              | Code Engine은 소스 코드를 이용하여 컨테이너 이미지를 만들고, Dockerfile과 Cloud Native Buildpack을 모두 지원합니다. |
| 워크로드를 비공개로 유지한다. | 소스 코드를 프라이빗 리포지토리에 저장하고 이미지를 프라이빗 레지스트리에 푸시하더라도 Code Engine은 엑세스가 가능합니다. |
| 완전 통합                     | Code Engine은 IBM Cloud에 완벽하게 통합되어 있어 IBM 클라우드 서비스의 전체 카탈로그를 활용할 수 있다. |
| 이벤트 중심 워크로드          | 프로그램은 이벤트에 반응하고 해당 이벤트에 기반한 작업을 수행할 수 있습니다. |
| 오토 스케일링                 | Code Engine은 요청이 없을 때 자동으로 워크로드를 0으로 스케일 다운할 수 있습니다. |
| 제어 접근                     | IBM Cloud Identity과 Access Management의 프로젝트에 플랫폼 및 서비스 액세스 권한을 할당하여 IBM Cloud 계정에서 리소스를 프로비저닝하고 관리할 수 있는 사용자를 제어할 수있습니다. |
| 오픈 소스 베이스              | Code Engine은 오픈소스 기술을 기반으로 구축돼 앱과 작업을 포터블(설치가 필요없는 무설치)하게 할 수있다. |
| 워크로드의 보안 유지          | Code Engine은 워크로드가 인터넷에 안전하게 노출, 모니터링, 통제될 수 있도록하고, 데이터 암호화를 통해 보안성을 높입니다. |



# 자 그럼 직접 한 번 해보자



## * Node-red 이미지를 배포해서 사용해보기

1. [IBM Cloud](https://cloud.ibm.com)에 접속하여 왼쪽 메뉴에서 Code Engine - [Project](https://cloud.ibm.com/codeengine/projects)를 클릭합니다.
2. **Create project**를 선택합니다.

![ce1](/Users/hangyutae/Desktop/ce1.png)

3. **프로젝트 이름**을 설정한 후 **Create** 버튼을 클릭합니다.

![ce2](/Users/hangyutae/Desktop/ce2.png)

4. 프로젝트가 만들어지면 프로젝트를 클릭하여 왼쪽 메뉴에서 **Applcations**를 클릭 후 **Create Application**을 클릭합니다.

![Screen Shot 2020-09-23 at 3.50.39](/Users/hangyutae/Desktop/ce3.png)

5. Code는 **Container image**를 클릭하고 밑 **Image reference**에 `docker.io/nodered/node-red` 를 입력한 후, **Deploy** 버튼을 클릭합니다.

![ce4](/Users/hangyutae/Desktop/ce4.png)

6. 배포가 끝나면 **Application URL**버튼을 눌러 Node-red 화면이 나오면 완료입니다.

![ce5](/Users/hangyutae/Desktop/ce5.png)



# 이리저리 만져보고 느낀 점

10분도 안되서 이미지 배포가 끝났는데 컨테이너 이미지만 있다면 마우스 클릭 몇 번으로 누구나 쉽고 간편하게 웹에서 배포가 가능하기 때문에 ([IBM Cloud CLI](https://cloud.ibm.com/docs/codeengine?topic=codeengine-kn-install-cli)로도 가능합니다.) 정말 간단하구나..라고 생각했습니다. 그리고 프라이빗 저장소에 푸시된 것들을 읽을 수 있으니 보안면에도 상당한 이점이 있다고 느껴졌고 요청할 때마다 과금이 이루어지므로 요청만 없다면 계속 배포해놓을 수 있다는 장점(?)이 있을 수 있다고 생각하였습니다. IBM Cloud와의 통합성도 좋으니 다른 서비스들과의 연계도 상당히 좋지않을까라고 생각합니다!







틀린 부분이 있으면 지적 감사히 받겠습니다.