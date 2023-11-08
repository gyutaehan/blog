---
layout: post
comments: true
title: 구글홈미니에 에어플레이하기 (ft, 시놀로지 나스)
description: 
- 구글홈미니에 에어플레이하기
- 시놀로지 나스에 에어플레이하기
date: 2021-05-17 10:18:00
categories: dev
author: Gyutae Han
---

**구글홈미니**에 블루투스를 연결하여 음악을 재생하거나 할 수는 있지만, 일일히 재생할 때마다 블루투스를 연결하는 것은 어지간히 귀찮은 일입니다. 그래서 **시놀로지 나스**를 거쳐서 에어플레이를 할 수 있게  [AirConnect-Synology](https://github.com/eizedev/AirConnect-Synology)를 설치를 하였습니다.

# AirConnect Synology

[AirConnect-Synology](https://github.com/eizedev/AirConnect-Synology)는 **시놀로지 나스**와 **크롬캐스트 기기**를 연결해주는 시놀로지 패키지를 제공하고 있습니다. 누구나 전문지식 없이도 간단히 설치가 가능합니다. 

![airconnection-git0](http://localhost/content/images/2021/05/airconnection-git0.png)
먼저 본인이 가지고 있는 나스의 cpu의 종류를 확인합니다. 저는 **DS216j**로 **armada385**라는 cpu를 사용하고 있습니다. ARMv7의 CPU이기 때문에 AirConnect-arm-static-${VERSION}을 설치하면 됩니다. 본인이 가진 나스의 CPU를 자세히 알고 싶다면 [여기](https://www.synology.com/en-global/knowledgebase/DSM/tutorial/Compatibility_Peripherals/What_kind_of_CPU_does_my_NAS_have)서 확인 할 수 있습니다.

![airconnect-git1](http://localhost/content/images/2021/05/airconnect-git1.png)
[AirConnect-Synology](https://github.com/eizedev/AirConnect-Synology)에 접속하여 우측 하단의 가장 **최신 버전의 릴리스**를 클릭합니다. 

![airconnect-git2](http://localhost/content/images/2021/05/airconnect-git2.png)
본인이 가진 시놀로지 나스에 맞는 시놀로지 패키지를 설치합니다. 저는 AirConnect-arm-static-0.2.50.4-20210314.spk 를 설치하였습니다.

![airconnect-set0](http://localhost/content/images/2021/05/airconnect-set0.png)
설치가 끝났다면, 본인의 시놀로지 나스에 접속하여 **패키지 센터** - **설정** - **신뢰하는 레벨**을 **Any publisher**로 설정합니다.

![airconnect-set1](http://localhost/content/images/2021/05/airconnect-set1.png)
**수동설치(Manual Install)** - Browse를 선택하여 설치 받았던 **시놀로지 패키지(.sdk)를 선택**하여 **인스톨**을 진행합니다.

![airconnect-set2](http://localhost/content/images/2021/05/airconnect-set2.png)
인스톨이 완료되었다면, 인스톨이 되었는지 확인하고 **실행**을 해주면 완료입니다.

![airconnect-get0](http://localhost/content/images/2021/05/airconnect-get0.png)
에어플레이 기기에서 본인의 구글홈미니 이름이 표시 되어 있는 지 확인합니다. 물론 에어플레이는 잘 작동하였습니다.

# 실행했는데도 에어플레이에 나오지 않는 경우

1. 시놀로지 나스를 재시작하고 Airconnect가 실행되어 있는 지 확인한다.
2. 구글홈미니가 같은 네트워크에 연결이 되어있는지 확인한다.
3. 에어플레이 기기가 같은 네트워크 상에 연결이 되어 있는지 확인한다.

그 외에 이상한 점이 있으면 댓글 부탁드립니다.

# 만약 시놀로지 나스를 가지고 있지 않다면?

AirConnect-Synology는 [AirConnect](https://github.com/philippe44/AirConnect)를 쉽게 시놀로지 나스에서 설치하도록 패키화한 것으로, 다른 기기에서도 할 수 있습니다. 윈도우, 맥, 리눅스등 거의 모든 플랫폼을 지원하고 있으며, 시놀로지 나스가 아니더라도 피시나 라즈베리파이 같은 곳에 쉽게 설치, 에어플레이를 할 수 있습니다. 설치 방법은 [AirConnect](https://github.com/philippe44/AirConnect)에서 참고하시면 됩니다.