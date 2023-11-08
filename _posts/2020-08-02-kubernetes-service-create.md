---
layout: post
comments: true
title: 【IBM Cloud】Kubernetes Service 생성
description: 
- Kubernetes 서비스 생성
- IBM Cloud 쿠버네티스 서비스
date: 2020-08-02 10:18:00
categories: dev
author: Gyutae Han
---

# [IBM Cloud] Kubernetes Service 생성

[IBM Cloud](https://cloud.ibm.com/)  에 접속하여 `Kubernetes Cluster`를 검색합니다.
![kuber1](http://localhost/content/images/2020/08/kuber1.png)

 `무료 클러스터 플랜`을 선택해주고 따로 설정할 것 없이 바로 만들기 해줍니다.
![kuber2](http://localhost/content/images/2020/08/kuber2.png)

만들어주면 클러스터 액세스 화면이 나오는데 아래 절차에 따라 터미널로 실행해줍니다.
![kuber3](http://localhost/content/images/2020/08/kuber3.png)


##### Kubernetes Service 플러그인을 설치
``` shell
# 운영체제마다 다를 수 있으니 위 페이지에서 복사해서 진행하시길 바랍니다.
$ curl -sL https://ibm.biz/idt-installer | bash
```


##### IBM클라우드 클러스터 설정

```shell
# resourceGroupName은 우측상단메뉴에서 관리->액세스(IAM)->리소스그룹에서 ID를 복사하면 됩니다.
$ ibmcloud login -a cloud.ibm.com -r eu-de -g <resourceGroupName>

# 위 페이지 내용 그대로 복사하시면 됩니다.
$ ibmcloud ks cluster config --cluster <클러스터ID>

# IBM클라우드 클러스터정보가 표시되면 성공!
$ kubectl config current-context
```



마지막으로 클러스터 상태가 `정상`이 되면 완료입니다.
![kuber4](http://localhost/content/images/2020/08/kuber4.png)