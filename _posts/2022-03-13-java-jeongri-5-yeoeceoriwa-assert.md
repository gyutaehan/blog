---
layout: post
comments: true
title: Java 정리 5. Date, Time
date: 2022-03-13 10:18:00
categories: dev
---

### ※이 글은 Java SE 8 기준으로 작성되었습니다.

### 이 장에서 다룰 Date와 Time의 API

이 장에서 다룰 날짜, 시간 관련 API는 java.util의 Date, Calendar 같은 클래스가 아닌  Java SE 8에서 추가된 java.time패키지에 관련된 내용이다.



### 클래스 종류



**TemporalAccssor 인터페이스와 하위 클래스들**

![1](http://localhost/content/images/2022/03/1.png)



- TemporalAccessor

시간을 표현하는 클래스를 만들기 위해서 사용하는 인터페이스이다.

※TemporalAccessor, Tempora는 실제로 사용하는 경우는 많이 없으므로  각각의 메서드에 대한 설명만 남김

```java
package java.time.temporal;

public interface TemporalAccessor {
    // 지정된 필드가 서포트되어있는가 체크함
    boolean isSupported(TemporalField field);

    // 지정된 필드의 유효한 값의 범위를 리턴
    default ValueRange range(TemporalField field) {
        if (field instanceof ChronoField) {
            if (isSupported(field)) {
                return field.range();
            }
            throw new UnsupportedTemporalTypeException("Unsupported field: " + field);
        }
        Objects.requireNonNull(field, "field");
        return field.rangeRefinedBy(this);
    }

    // 지정된 필드의 값을 리턴(int)
    default int get(TemporalField field) {
        ValueRange range = range(field);
        if (range.isIntValue() == false) {
            throw new UnsupportedTemporalTypeException("Invalid field " + field + " for get() method, use getLong() instead");
        }
        long value = getLong(field);
        if (range.isValidValue(value) == false) {
            throw new DateTimeException("Invalid value for " + field + " (valid values " + range + "): " + value);
        }
        return (int) value;
    }

    // 지정된 필드의 값을 리턴(long)
    long getLong(TemporalField field);

    // 날짜, 시간을 
    default <R> R query(TemporalQuery<R> query) {
        if (query == TemporalQueries.zoneId()
                || query == TemporalQueries.chronology()
                || query == TemporalQueries.precision()) {
            return null;
        }
        return query.queryFrom(this);
    }

}

```



- Temporal

  TemporalAccessor의 서브 인터페이스

  ※TemporalAccessor, Temporal는 실제로 사용하는 경우는 많이 없으므로 각각의 메서드에 대한 설명만 남김

  ```java
  package java.time.temporal;
  
  import java.time.DateTimeException;
  
  public interface Temporal extends TemporalAccessor {
  
      boolean isSupported(TemporalUnit unit);
  
  		// 
      default Temporal with(TemporalAdjuster adjuster) {
          return adjuster.adjustInto(this);
      }
  		
      Temporal with(TemporalField field, long newValue);
  
    	// 기간을 더하여 같은 오브젝트를 반환
      default Temporal plus(TemporalAmount amount) {
          return amount.addTo(this);
      }
  
    	// 기간을 더하여 새로운 오브젝트를 반환
      Temporal plus(long amountToAdd, TemporalUnit unit);
  
    	// 기간을 빼고 그 오브젝트를 반환
      default Temporal minus(TemporalAmount amount) {
          return amount.subtractFrom(this);
      }
  
    	// 기간을 빼고 새로운 오브젝트를 반환
      default Temporal minus(long amountToSubtract, TemporalUnit unit) {
          return (amountToSubtract == Long.MIN_VALUE ? plus(Long.MAX_VALUE, unit).plus(1, unit) : plus(-amountToSubtract, unit));
      }
  
    	// 지정한 기간까지의 시간양을 계산
      long until(Temporal endExclusive, TemporalUnit unit);
  
  }
  
  ```

  

- LocalDate

  날짜만 가지고 있음

  ```java
  public class Test{
      public static void main(String... args) {
          LocalDate.now(); //2022-02-20 #오늘 날짜를 반환
          LocalDate.of(2016, Month.APRIL, 1); //2016-04-01
          LocalDate.parse("2016-04-01"); //2016-04-01
        
          LocalDate d1 = LocalDate.of(2022, Month.APRIL, 1);
          LocalDate d2 = LocalDate.of(2022, Month.MAY, 1);
          d1.isBefore(d2); // true #source의 날짜가 작을 경우, true
          d1.isEqual(d2); // false #source의 날짜가 같을 경우, true
          d1.isAfter(d2); // false #source의 날짜가 클 경우, true
      }
  }
  
  
  ```

  

- LocalTime

  시간만 가지고 있음

  ```java
  public class Test{
      public static void main(String... args) {
          System.out.println(LocalTime.now()); //19:28:33.979 #현재 시간을 반환
          System.out.println(LocalTime.of(10, 30, 1, 1)); //10:30:01.000000001
          System.out.println(LocalTime.parse("19:27:49")); //19:27:49
  
          LocalTime t1 = LocalTime.of(10, 30, 1, 1);
          LocalTime t2 = LocalTime.of(15, 00, 59, 1);
          System.out.println(t1.isBefore(t2)); // true #source의 시간이 작을 경우, true
          System.out.println(t1.isAfter(t2)); // false #source의 시간이 클 경우, true
      }
  }
  ```



- LocalDateTime

  날짜의 시간, 둘 다 가지고 있음.

  

  

- OffsetTime

  LocalTime과 ZoneOffset를 가진 클래스.

  

- OffsetDateTime

  OffsetDateTime과 ZoneOffset를 가진 클래스.



**TemporalUnit 인터페이스와 ChronoUnit 열거형**

![2](http://localhost/content/images/2022/03/2.png)



- TemporalUnit

  날짜나 시간의 단위를 나타내는 인터페이스.

  

- ChronoUnit

  TemporalUnit의 하위 열거형

  ````java
  public class Test{
      public static void main(String... args) {
          // 시간 계산
          LocalDate start = LocalDate.now(); // 2022-02-20 #오늘 날짜를 반환
          LocalDate end = LocalDate.of(2022, Month.FEBRUARY, 30); // 2022-02-28
          ChronoUnit.DAYS.addTo(start, 5); // 2022-02-25 #start에 5일을 더함
          ChronoUnit.DAYS.between(start, end); // 8 # start - end 날짜를 뺌
  
          // 현재 타임존을 나타내는 인터페이스를 습득
          ZoneId.systemDefault(); // Asia/Tokyo 
      }
  }
  
  
  ````


  > * DAYS
  >
  > 1일마다의 단위
  >
  > * HOURS
  >
  > 한 시간마다의 단위
  >
  > * MINUTES
  >
  > 1분마다의 단위
  >
  > * MONTHS
  >
  > 한 달마다의 단위
  >
  > * SECONDS
  >
  > 1초마다의 단위
  >
  > * WEEKS
  >
  > 1주마다의 단위
  >
  > * YEARS
  >
  > 1년마다의 단위

 


**TemporalAmount 인터페이스와 하위 클래스들**

![3](http://localhost/content/images/2022/03/3.png)



- TemporalAmount

  날짜나 시간의 단위를 나타내는 인터페이스.

  

- Period

  TemporalAmount의 하위 클래스

  기간의 간격을 구할때 사용함.

  ```java
  public class Test{
      public static void main(String... args) {
          LocalDate start = LocalDate.now(); // 2022-03-13
          LocalDate end = LocalDate.of(2023, Month.APRIL, 30); //2023-04-30
          // 일자 간격을 구하는
          Period p1 = Period.between(start,end); //  일자간격
          System.out.println("start : " + start);
          System.out.println("end : " + end);
          System.out.println("간격 : " + p1.getYears() + "년" + p1.getMonths() + "개월" +p1.getDays() + "일"); // 간격 : 1년1개월7일
      }
  }
  ```

  

- Duration

  TemporalAmount의 하위 클래스

  시간의 간격을 구할 때 사용함 

  ```java
  public class Test{
      public static void main(String... args) {
          LocalTime start = LocalTime.of(10,12,30, 50);
          LocalTime end = LocalTime.of(20,30,50, 60);
  
          Duration p2 = Duration.between(start,end); //  시간 간격
          System.out.println("간격 : " + p2.getNano() + "나노초 " + p2.getSeconds() + "초"); // 간격 : 10나노초 37100초
      }
  }
  
  
  ```

  

  

  **그 외** 



- DateTimeFormatter 

  Date의 표시형식 설정(Formatter)하기 위한 클래스

  ```java
  public class Test{
      public static void main(String... args) {
          //DateTimeFommater 인스턴스화하는 세 가지 방법
          DateTimeFormatter formatter = DateTimeFormatter.ofLocalizedDateTime(FormatStyle.MEDIUM);
          DateTimeFormatter formatter1 = DateTimeFormatter.ISO_DATE;
          DateTimeFormatter formatter2 = DateTimeFormatter.ofPattern("YYYY-DD");
      }
  }
  
  
  ```

  

  

- Instant

  시간의 단일 지점을 표시하는 인터페이스. Instant는 그 순간이라는 의미가 있음

  예를들어 1970년 1월 1일 오전 0시0분0초부터의 경과한 초를 구함.

  ```java
  public class Test{
      public static void main(String... args) {
          // Instant 인터페이스
          Instant instant1 = Instant.now();
          Instant instant2 = ZonedDateTime.now().toInstant();
          Instant instant3 = Instant.ofEpochSecond(0);
          Instant instant4 = new Date().toInstant(); //추가됨
          Instant instant5 = Calendar.getInstance().toInstant(); // 추가됨
      }
  }
  
  
  ```

  

  

   