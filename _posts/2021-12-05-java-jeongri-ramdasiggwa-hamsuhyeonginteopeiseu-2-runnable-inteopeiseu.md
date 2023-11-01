---
layout: post
title: Java 정리, 람다식과 함수형인터페이스 - 2. Runnable 인터페이스
date: 2021-12-05 10:00:00
categories: dev
---

> ※이 글은 Java SE 8 기준으로 작성되었습니다.



```java
package java.lang;

@FunctionalInterface
public interface Runnable {
    public abstract void run();
}
```

Runnable 인터페이스는 추상메서드 run을 갖고 있는 함수형 인터페이스로 Java.lang 패키지안에 있음.

인수가 없고 void를 리턴하는 것이 특징임(걍 선언하고 싶은 걸 선언해두면 실행되므로 쓰레드에서 뭔가를 실행시킬때 사용함)



## 사용법

```java
Runnable r = ()->{
  System.out.println("run...");
};
r.run(); // run... 출력
```


## 이전 글

[Java 정리, 람다식과 함수형인터페이스 - 1. 함수형 인터페이스란?](http://localhost/java-jeongri-ramdasiggwa-hamsuhyeonginteopeiseu-1-hamsuhyeong-inteopeiseuran/)