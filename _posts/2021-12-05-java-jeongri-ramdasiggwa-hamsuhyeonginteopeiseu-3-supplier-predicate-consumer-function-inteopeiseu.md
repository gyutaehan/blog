---
layout: post
comments: true
title: Java 정리, 람다식과 함수형인터페이스 - 3. Supplier,Predicate,Consumer,Function 인터페이스
date: 2021-12-05 10:00:00
categories: dev
---

> ※이 글은 Java SE 8 기준으로 작성되었습니다.


자바에서 기본적으로 제공되는 **함수형 인터페이스**는 **java.util.function**에 포함되어있는데, 대표적으로 **Supplier,Predicate,Consumer,Function**가 있음.


## 1. Supplier 인터페이스

```java
package java.util.function;

@FunctionalInterface
public interface Supplier<T> {
    T get();
}
```

**Supplier** 인터페이스는 인수가 없고 특정 값을 리턴을 하는 특징이 있음.

**get**이라는 추상 메서드을 이용함.



## 사용법

```java
Supplier<Integer> si = ()-> 10;
System.out.println(si.get()); // 10 출력

Supplier<Boolean> sb = ()-> true;
System.out.println(sb.get()); // true 출력

Supplier<String> ss = ()-> "result";
System.out.println(ss.get()); // result 출력
```



## 2. Predicate 인터페이스

```java
package java.util.function;

import java.util.Objects;

@FunctionalInterface
public interface Predicate<T> {

    boolean test(T t);

    default Predicate<T> and(Predicate<? super T> other) {
        Objects.requireNonNull(other);
        return (t) -> test(t) && other.test(t);
    }

    default Predicate<T> negate() {
        return (t) -> !test(t);
    }

    default Predicate<T> or(Predicate<? super T> other) {
        Objects.requireNonNull(other);
        return (t) -> test(t) || other.test(t);
    }

    static <T> Predicate<T> isEqual(Object targetRef) {
        return (null == targetRef)
                ? Objects::isNull
                : object -> targetRef.equals(object);
    }
}

```

**Predicate** 인터페이스는 하나의 인수를 갖고 boolean 값을 리턴함.

**test**이라는 추상 메서드을 이용함.

**Static 메서드**인 **isEqual**과 **default 메서드**인 **and**, **negate**, **or**를 들고있음.



## 사용법

```java
Predicate<Integer> p1 = (v)-> v.equals(10); // 들어온 인수와 10이 같은지 비교
Predicate<Integer> p2 = (v)-> v.equals(20); //  들어온 인수와 20이 같은지 비교

// 인수 10
System.out.println(p1.test(10)); // true 출력
// 인수 10 (negate: 논리부정, ~p1)
System.out.println(p1.negate().test(10)); // false 출력
// 인수 20 (and: 논리AND, p1 & p2)
System.out.println(p1.and(p2).test(20)); // false 출력
// 인수 20 (or: 논리OR,  p1 | p2)
System.out.println(p1.or(p2).test(20)); // true 출력
// 인수 10 (10과 같은지 비교)
System.out.println(Predicate.isEqual(10).test(10)); // true 출력
```



## 3. Consumer 인터페이스

```java
package java.util.function;

import java.util.Objects;


@FunctionalInterface
public interface Consumer<T> {
    void accept(T t);

    default Consumer<T> andThen(Consumer<? super T> after) {
        Objects.requireNonNull(after);
        return (T t) -> { accept(t); after.accept(t); };
    }
}

```

**Consumer** 인터페이스는 하나의 인수를 갖고 void를 리턴함.

**accept**이라는 추상 메서드을 이용함.

**default 메서드**인 **andThen**를 들고있음.



## 사용법

```java
Consumer<Integer> c1 = (v) -> System.out.println(v + ": c1");
Consumer<Integer> c2 = (v) -> System.out.println(v + ": c2");
// 인수 10
c1.accept(10); // 10: c1 출력
// 인수 10, 처음 c1이 실행되고 c2가 실행됨.
// c1에서 예외가 터졌을 경우는 즉시 중단됨.
c1.andThen(c2).accept(10);  // 10: c1
														// 10: c2 출력

```



## 4. Function 인터페이스

```java
package java.util.function;

import java.util.Objects;

@FunctionalInterface
public interface Function<T, R> {
    R apply(T t);

    default <V> Function<V, R> compose(Function<? super V, ? extends T> before) {
        Objects.requireNonNull(before);
        return (V v) -> apply(before.apply(v));
    }

    default <V> Function<T, V> andThen(Function<? super R, ? extends V> after) {
        Objects.requireNonNull(after);
        return (T t) -> after.apply(apply(t));
    }
  
    static <T> Function<T, T> identity() {
        return t -> t;
    }
}

```

**Function** 인터페이스는 하나의 인수를 갖고 특정 값을 리턴함.

**apply**이라는 추상 메서드을 이용함.

**Static 메서드**인 **identity**과 **default 메서드**인 **compose**, **andThen**를 들고있음.



## 사용법

```java
Function<Integer, String> f1 = (arg) -> "result is " + arg;
Function<Integer, Integer> f2 = (arg) -> ++arg;
Function<String, String> f3 = (arg) -> "after result is " + arg;

// 인수 10
System.out.println(f1.apply(10)); // result is 10 출력
// 인수 10, f2에서 arg가 1이 증가가 된 값이 f1의 인수로서 들어감.
System.out.println(f1.compose(f2).apply(10)); // result is 11 출력
// 인수 10, f1의 결과값(String)이 f3의 인수로서 실행이 됨.
System.out.println(f1.andThen(f3).apply(10)); // after result is result is 10 출력
// 인수 10, 들어간 값 그대로 출력.
System.out.println(Function.identity().apply(10)); // 10 출력
```



## 이전 글

[Java 정리, 람다식과 함수형인터페이스 - 1. 함수형 인터페이스란?](http://localhost/java-jeongri-ramdasiggwa-hamsuhyeonginteopeiseu-1-hamsuhyeong-inteopeiseuran/)

[Java 정리, 람다식과 함수형인터페이스 - 2. Runnable 인터페이스](http://localhost/java-jeongri-ramdasiggwa-hamsuhyeonginteopeiseu-2-runnable-inteopeiseu/)