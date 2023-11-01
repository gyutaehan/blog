---
layout: post
comments: true
title: Java 정리, 제네릭과 콜렉션 - 1. 제네릭(Generics)이란?
date: 2021-12-11 10:18:00
categories: dev
---

### ※이 글은 Java SE 8 기준으로 작성되었습니다.

## 제네릭(Generics)

제네릭(Generics)은 어떤 타입 A(클래스나 인터페이스)나 메서드가 어떤 타입B를 다룰때 그 타입B를 컴파일러에 대하여 명시하는 구조를 말함. 즉 맘대로 갖다 쓸수있음

제네릭을 사용할 수 있는 타입을 **제네릭 타입**, 클래스를 제네릭 클래스, 인터페이스를 **제네릭 인터페이스**라고 부름.



### 제네릭의 정의

```java
//제네릭 클래스 Foo 생성
class Foo<T> {
    //제네릭 타입 T가 사용가능하다.
    private T t;
    Foo() {}
    public T getT() {
        return t;
    }
    public void setT(T t) {
        this.t = t;
    }
}

public class Test{
    public static void main(String[] args) throws Exception {
        // 제네릭 클래스 인스턴스 선언
        Foo<Integer> f = new Foo<Integer>();
        // 제네릭 메서드 실행
        bar(f);
    }
    // 제네릭 메서드 bar 생성
    // 리턴 타입 앞에 <T>선언함으로서 T타입 사용이 가능해짐
    public static <T> Foo bar(T t) {
        Foo result = new Foo<T>();
        result.setT(t);
        return result;
    }
}
```

제네릭 클래스는 클래스명 뒤에 꺽쇠안에 제네릭 타입을 명시함.

제네릭 클래스를 사용할 때는 똑같이 꺽쇠안에 타입을 명시함(선언부에 명시 안하면 컴파일 에러)

제네릭 메서드는 리턴 타입앞에 꺽쇠안에 타입을 명시함.

위 소스에서는 제네릭 타입을 T라고 명시했는데, 이름은 맘대로 해도 됨.

제네릭 타입은 여러개 사용해도 무관.



### 타입 제한

```java
// 제네릭 클래스 생성
// Number타입이거나 또는 서브 타입만 사용하도록 제한
class Foo<T extends Number> {
    private T t;
}

// &를 이용하여 부모클래스 & 인터페이스를 명시하여 제한이 가능
class Bar<T extends Number & Serializable> {
    private T t;
}

public class Test{
    public static void main(String[] args) throws Exception {
        Foo<Integer> f1 = new Foo<Integer>();
        Foo<String> f2 = new Foo<String>(); // String은 Number의 자식 클래스가 아니므로 컴파일 에러 발생
        Bar<Integer> b1 = new Bar<Integer>();
    }
}
```

T extends Number를 이용하여 타입제한이 가능함.

모든 클래스의 부모는 Object이기 때문에, T와 T extends Object는 같아보임 -> 결국 따로 명시안하면 Object형으로 인식해서 동작이 된 것을 확인

&기호를 이용하여 클래스 & 인터페이스 명시가 가능함.