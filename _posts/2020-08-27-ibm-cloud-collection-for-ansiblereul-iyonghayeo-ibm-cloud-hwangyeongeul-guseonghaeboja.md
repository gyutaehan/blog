---
layout: post
comments: true
title: IBM Cloud Collection for Ansible를 이용하여 IBM Cloud 환경을 구성해보자.
description: 
- IBM Cloud Collection for Ansible
- IBM Cloud 환경을 구성
date: 2020-08-27 10:18:00
categories: dev
author: Gyutae Han
---

[IBM Cloud Collection for Ansible](https://www.ibm.com/cloud/blog/announcements/ibm-cloud-collection-for-ansible)은 2020년 2월 10일에 발표된 Ansible과 IBM Cloud를 연결하는 **표준 모듈**입니다. **Ansible**은 많은 서버들을 동시에 배포를 할 때 인프라 상태를 코드로 작성하여 이를 모든 서버에 배포가 가능하게 해주는 도구입니다.
**Ansible Playbook**라는 yaml 형식의 **언어**가 있는데 그것으로 인프라상태를 작성이 가능합니다. IBM Cloud Collection for Ansible가 나옴으로 인해, **Ansible Playbook**로 IBM Cloud의 리소스들을 간단히 관리가 가능해졌습니다.

# 환경 구축
https://github.com/IBM-Cloud/ansible-collection-ibm 를 참고하면, 다음과 같은 항목이 설치가 필요합니다.
- **Python 3**
- **RedHat** Ansible
- **IBM Cloud Collection**

로컬에 설치해서 테스트를 해도 되지만, **도커**에 환경을 구축해서 테스트를 해보려고 합니다.
먼저 도커 이미지를 생성하기 위한 **Dockerfile**을 작성합니다.
###### Dockerfile
```dockerfile
FROM python:3.8

# 앤서블 설치
RUN pip install --upgrade pip && pip install "ansible>=2.9.2"

# IBM Cloud Collection 설치 및 튜토리얼 파일을 GitHub에서 클론
RUN ansible-galaxy collection install ibm.cloudcollection && \
git clone https://github.com/IBM-Cloud/ansible-collection-ibm.git

# IBM Cloud API키 설정
ENV IC_API_KEY=<YOUR_API_KEY_HERE>
```

<YOUR_API_KEY_HERE>에 본인의 IBM Cloud의 **API키**를 넣으시면 됩니다.
예를들어 API키가 **ABCDEFGHIJKLMN**이라면 `ENV IC_API_KEY=ABCDEFGHIJKLMN`가 됩니다.

> **IBM Cloud API키 확인방법**
>
> https://cloud.ibm.com/iam 에 접속하여 왼쪽 메뉴에서 **API 키**를 클릭합니다.
> ![apikey](http://localhost/content/images/2020/08/apikey.png)
> 우측 **IBM Cloud API** 키 작성을 눌러 **이름**과 **설명**을 적은다음 **작성**을 클릭하면 생성이 됩니다.
> **중요한 정보**이므로 유출되지 않도록 주의하시길 바랍니다.

도커파일을 빌드하고 이미지를 실행합니다. 용량이 크기 때문에 빌드하는 데에 시간이 꽤 소모됩니다. 여유를 가지고 해주세요~
```shell
$ docker build -t ansible . 
$ docker run -d -it --name ansible ansible
$ docker exec -it ansible /bin/bash 
root@4193758e46d6:/#
```
API키가 설정이 되어있는지 **env**로 확인을 합니다. 만약 설정이 안되어있다거나 잘못되어 있을 경우, `export IC_API_KEY=<YOUR_API_KEY_HERE>` 로 API키를 설정을 한 후, 다시 확인 해주세요!

```shell
root@4193758e46d6:/# env | grep IC_API_KEY

IC_API_KEY=<설정했던 API키를 확인할 수 있습니다.>
```

# IBM Cloud App ID를 만들어보자!
**ansible-collection-ibm/examples/** 폴더에는 https://github.com/IBM-Cloud/ansible-collection-ibm 에서 공개중인 예제파일(playbook)들이 있습니다.
여러가지 예제가 있지만, 그 중에서 **IBM Cloud App ID** 인스턴스를 만들어볼까 합니다.
먼저 **플레이북**들을 확인해보겠습니다.
###### vars.yml

```
---
instance_name: test-instance
location_info: us-south
plan_type: lite
```

###### create.yml

```
---
- name: Create APPID instance.
  hosts: localhost
  collections:
   - ibm.cloudcollection
  tasks:
    - name: Read the variables from var file
      include_vars:
        file: vars.yml
    - name: Create a APPID instance
      ibm_resource_instance:
        name: "{{ instance_name }}"
        service: "appid"
        plan: "{{ plan_type }}"
        location: "{{ location_info }}"
```

**vars.yal**은 변수를 지정하는 파일입니다. 
- **instance_name** : 인스턴스 이름으로, 지정된 이름으로 인스턴스가 생성이 됩니다.
- **location_info** : 기본은 미국으로 설정되어 있으며, 이 값으로 지역 변경이 가능합니다.
- **plan_type** : 플랜으로 기본은 lite플랜입니다. 다른 플랜으로 변경이 가능합니다.

**create.yml**은 실제로 App ID 인스턴스를 생성하기 위한 설정파일입니다. 
- **name** : Ansible 플레이 이름입니다. 로그에서 이 이름으로 출력이 됩니다.
- **hosts** : Ansible 대상 호스트입니다.
- **collections** : collection의 설정하는 것으로, ansible-galaxy를 이용하여 ibm.cloudcollection를 설치를 하고 설정을 합니다.
- **tasks** : 실제 명령을 내릴 태스크의 설정 값입니다.

위는 **test-instance**라는 **App ID 인스턴스**를 **미국 서버**에 **lite 플랜**으로 작성하는 코드입니다.
실행을 위해 플레이북이 있는 폴더에 이동을 하여, `ansible-playbook`을 이용하여 **create.yml**을 실행합니다.

```shell
root@4193758e46d6:/# cd ansible-collection-ibm/examples/appid-instance-creation/
root@4193758e46d6:/# ansible-playbook create.yml

[WARNING]: No inventory was parsed, only implicit localhost is available
[WARNING]: provided hosts list is empty, only localhost is available. Note that the implicit localhost does not match 'all'

PLAY [Create APPID instance.] ***********************************************************************************************************

TASK [Gathering Facts] ******************************************************************************************************************
ok: [localhost]

TASK [Read the variables from var file] *************************************************************************************************
ok: [localhost]

TASK [Create a APPID instance] **********************************************************************************************************
changed: [localhost]

PLAY RECAP ******************************************************************************************************************************
localhost                  : ok=3    changed=1    unreachable=0    failed=0    skipped=0    rescued=0    ignored=0  
```

실행이 완료되고 https://cloud.ibm.com/ 에 접속해서 설정한 이름(test-instance)의 App ID가 생성이 된 것을 확인하면 완료입니다!

![ibmcloud_appid](http://localhost/content/images/2020/08/ibmcloud_appid.png)



# 참고
- [IBM](https://www.ibm.com/cloud/blog/announcements/ibm-cloud-collection-for-ansible)
- [Ansaible-Collection-IBM GitHub](https://github.com/IBM-Cloud/ansible-collection-ibm)
- [Qiita-kentarok님](https://qiita.com/kentarok/items/f303d0cb3bf156b4833d)