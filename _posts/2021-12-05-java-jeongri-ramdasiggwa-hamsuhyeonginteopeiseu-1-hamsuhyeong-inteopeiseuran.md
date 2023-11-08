---
layout: post
comments: true
title: Java 정리, 람다식과 함수형인터페이스 - 1. 함수형 인터페이스란?
description: 
- Java 자격증 함수형 인터페이스
date: 2021-12-05 10:00:00
categories: dev
author: Gyutae Han
---

> ※이 글은 Java SE 8 기준으로 작성되었습니다.



## 함수형 인터페이스(Functional interface)란?

추상메서드를 하나만 선언한 것

Single Abstract Method(SAM)라고도 함.

**static메서드**와 **default메서드**는 추상메서드가 아니므로 상관이 없음.(Java SE 9 이상에서는 private도 사용가능)

**@FunctionalInterface 어노테이션**을 이용하여 함수형인터페이스를 체크가능



## 함수형 인터페이스의 예

```java
@FunctionalInterface
interface Foo { 
    static void x() {}
    default void y() {}
    void z();
}
```



## 잘못된 함수형 인터페이스의 예

```java
// 추상메서드가 두 개 선언되어있으므로 함수형 인터페이스가 아님.
interface Foo {
    void x();
    void y();
}

// 추상메서드가 선언 되어있지않으므로 함수형 인터페이스가 아님.
interface Bar {
    static void x(){}
}
```



## 대표적인 함수형 인터페이스

1. Runnable
2. Supplier
3. Predicate
4. Consumer
5. Function