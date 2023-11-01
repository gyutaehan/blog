---
layout: post
comments: true
title: Java 정리 4. 예외처리와 Assert
date: 2022-01-15 15:18:00
categories: dev
---

### ※이 글은 Java SE 8 기준으로 작성되었습니다.

### 예외처리에 대하여

예외처리는 **RuntimeException**와 **Checked Exception**로 나뉘는데 프로그램 동작방식이 달라서 자격증시험 단골 문제중 하나이다. (특히 예외가 상속된 클래스의 상속관련 문제가 골치 아픈데, 보통 런타임예외라면 에러가 발생하지 않고 아니라면 에러가 발생한다가 정답이었다.)

런타임예외는 unchecked예외라고도 부르며 에러가 있어도 실행을 할 수 있다.하지만 Checked예외는 컴파일 에러가 발생하여 아예 실행이 불가능하다.

예외 처리는 **Try-catch**문으로 처리를 하며, **throws**를 이용하여 불려진 메서드로 에러를 넘겨줄 수가 있다.

main 메서드에서 throws 처리를 하면 JVM에서 마지막으로 예외처리를 해준다. 



### 예외처리 예제

```java
public class Test{
    public static void main(String... args) { 
        try (tryResourceTest trt = new tryResourceTest()) { // *1
            System.out.println(" RUN ");
        } catch (ArithmeticException e) { // *2
            System.out.println("ArithmeticException");
            e.printStackTrace();
        } catch (IOException | NumberFormatException e) { // *3
            System.out.println("IOException | NumberFormatException");
            e.printStackTrace();
        }
    }
}

class tryResourceTest implements AutoCloseable { // *1
    @Override
    public void close() throws IOException {
        throw new IOException();
    }
}

//출력
 RUN 
IOException | NumberFormatException
java.io.IOException
	at tryResourceTest.close(Test.java:25)
	at Test.main(Test.java:12)
```



*1 : 기본적인 try-catch문이 아닌, try-with-resource문이다. AutoCloseable를 상속받아서 close라는 메서드를 구현해주면 try문안에 넣을 수 가 있다. C#에서 자주보던 형식인데 try-with-resource문이 끝이 나면 리소스가 자동적으로 종료된다. 자동으로 종료되면 close메서드가 실행이 되는데 위 코드에서는 IOException을 발생시키고 있다.

*2: catch문은 여러개 사용할 수 있다. 주의할 점은 부모 상속관계인데, 위에 있는 예외가 가장 큰 부모일 경우에는 컴파일 에러를 발생시킨다. 예를들어 위에 Exception, 밑에 IOExpcetion이면 컴파일에러가 발생한다. 그리고 try문에는 catch나 finally 없이 try만 사용할 수 있다.

*3: **|** 를 이용한 multi-catch문이다. 위 예제에서 IOExpcetion이나 NumberFormatException가 발생하면 저기로 catch된다. multi-catch문도 마찬가지로 부모관계에 있는 예외들은 함께 들어갈 수 없다.



### Assert

Assert는 테스트를 할 때 사용하는 구문으로서 접하는 경우가 많이 없기 때문에 시험에서 당황하는 구문중 하나이다.

```java
public static void main(String... args) {
  boolean b = true;
  assert b != true : "테스트입니다.";
  // assert b != true;
  System.out.println("테스트가 아닙니다.");
}

//출력
Exception in thread "main" java.lang.AssertionError: 테스트입니다.
	at Test.main(Test.java:11)
```

구조는 `assert boolean ` 과  `assert boolean: "에러내용"` 이다.

boolean값이 false인 경우에 에러가 발생하며, assert로 확인하려면 `java -ea 클래스명`으로 실행하면 된다.