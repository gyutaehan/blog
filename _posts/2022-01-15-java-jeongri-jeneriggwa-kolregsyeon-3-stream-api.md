---
layout: post
comments: true
title: Java 정리 3. Stream API
date: 2022-01-15 10:18:00
categories: dev
---

### ※이 글은 Java SE 8 기준으로 작성되었습니다.

### Stream API이란

[Stream API](https://docs.oracle.com/javase/8/docs/api/java/util/stream/package-summary.html) 는 데이터 집합(Source)들의 처리(Operation)를 위한 API임.

java.util.stream패키지로 제공되어 있으며, 자바에서는 흔히 함수형 인터페이스(람다식)를 이용하여 만듬.

Stream이라는 용어에 대하여 혼동이 있을 수 있는데, 파일(File)이나 네트워크에서 사용하는 java.io.InputStream이나 OutputStream같은 Stream과는 별개임.


![Screen-Shot-2022-01-02-at-18.51.40](http://localhost/content/images/2022/01/Screen-Shot-2022-01-02-at-18.51.40.png)

**데이터집합(Source)** : 어떤 처리를 할 데이터로서 시작데이터임. 데이터의 집합이므로 Array나 Set등 데이터구조에서 사용가능

**중간조작(파이프라인)**: Source가 들어오면 그 데이터들을 처리하는 곳임. 처리는 여러번 몇번이고 나열할 수 있음.

**종단조작(Result)**: 처리가 끝난 결과값임. 



### Stream API 종류

* BaseStream  인터페이스 

  기본적인 Stream임. 밑 네 개의 Stream 인터페이스들의 부모 인터페이스

* Stream< T >

  제네릭 인터페이스로서 타입 설정 가능

* IntStream

  정수형을 가진 Stream

* LongStream

  Long타입을 가진 Stream

* DoubleStream

  Double타입을 가진 Stream

**BaseStream**을 제외한 Stream들은 **filter**이나 **map**, **sorted**등 데이터를 다루는 유용한 추상메서드를 가지고 있음.



### 자주 사용하는 메서드

*IntStream 타입으로 기술되어 있습니다.*

* filter

  ```java
  public static void main(String... args) {
    int[] array = {1, 2, 3, 4, 5};
    IntStream is = Arrays.stream(array);
    is.filter(value -> value % 2 == 0) // 짝수만 필터처리
      .forEach(value -> System.out.print(value + " ")); // 2 4 출력
  }
  ```

  IntStream filter(IntPredicate) 는 **Predicate 함수형 인터페이스**를 받아서 boolean값을 리턴하여, 그에 해당하는 데이터만 처리하는 메서드임.

  위 코드에서는 짝수일 경우만 값을 출력

* map

  ```java
  public static void main(String... args) {
    int[] array = {1, 2, 3, 4, 5};
    IntStream is = Arrays.stream(array);
    is.map(operand -> operand * operand) // 각 값을 제곱
      .forEach(value -> System.out.print(value + " ")); // 1 4 9 16 25 출력
  }
  ```

  IntStream map(IntUnaryOperator) 는 **IntUnaryOperator 함수형 인터페이스**를 받아서 리턴되는 int형 값으로 출력하는 메서드

  위 예제에서는 각 값의 제곱을 출력

* sorted

  ```java
  public static void main(String... args) {
    int[] array = {2, 4, 3, 1, 5};
    IntStream is = Arrays.stream(array);
    is.sorted() // 정렬
      .forEach(value -> System.out.print(value + " ")); // 1 2 3 4 5 출력
  }
  ```

  IntStream sorted() 는 들어온 값들을 정렬함

* peek

  ```java
  public static void main(String... args) {
    int[] array = {2, 4, 3, 1, 5};
    IntStream is = Arrays.stream(array)
      .sorted() // 정렬
      .peek(value -> System.out.println(value + "A")) // 1A 2A 3A 4A 5A
      .filter(value -> value > 3)
      .peek(value -> System.out.println(value + "B")) // 4B, 5B
      .map(operand -> operand * operand)
      .peek(value -> System.out.println(value + "C")); //16C, 25C
  
    System.out.println(is.count()); // 2 (개수 출력)
  
    // 출력 결과
    // 1A
    // 2A
    // 3A
    // 4A
    // 4B
    // 16C
    // 5A
    // 5B
    // 25C
    // 2
  }
  ```

  IntStream peek(IntConsumer) 는 Consumer를 받는 메서드로 forEach와 비슷한 메서드처럼 보이는데, forEach는 종단처리로 마지막에 실행하고, peek는 intStream을 리턴하는 중간처리임.

  종단처리가 없으면 결국 실행되지도 않음. 위 코드에서는 count() 메서드를 이용하여 종단처리를 했기 때문에 peek메서드에서 출력이 된 것. count같은 종단 처리가 없을 경우에는 실행 되지 않는다.

  출력결과를 보면 순서가 뒤죽박죽인것을 알 수 있는데 중간처리라서 순서대로 작동하지도 않음.