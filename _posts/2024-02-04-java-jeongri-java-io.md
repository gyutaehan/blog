---
layout: post
comments: true
title: Java 정리, Java I/O
description: 
- Java 자격증 Java I/O
date: 2024-02-04 10:00:00
categories: dev
author: Gyutae Han
---

### ※이 글은 Java SE 8 기준으로 작성되었습니다.

### Java I/O

**java.io.File 클래스**

1. 파일과 디렉토리를 참조가능함.
2. 파일데이터를 수정하는 것은 불가능()
3. Unix계의 파일시스템 고유의 기능을 사용하는것이 안됨.(최종 갱신일같은 기본적인 파일 속성은 습득은 되지만, 파일 소유자나 시큐리티속성의 상세등은 습득, 설정하는 것도 불가능함.)
4. 이벤트를 감시가 안됨

```java
import java.io.File;
import java.util.Date;

public class Main {
    public static void main(String[] args) {

        File f1 = new File("in/"); // 디렉토리 참조 가능
        File f2 = new File("in", "a.txt"); // 파일 참초
        File f3 = new File(f1, "b.txt"); // 디렉토리로 파일 참조

        File[] files = f1.listFiles();

        if (files != null) {
            for (File f : files) {

                if (f.isFile()) {
                    // 파일 정보 출력
                    System.out.println("File: " + f.getName()); 
                    System.out.println("File 수정일: " + new Date(f.lastModified()));
                } else if (f.isDirectory()) {
                    // 디렉토리 정보 출력
                    System.out.println("Directory: " + f.getName());

                }
            }
        }
        System.out.println("f1 absolute path: " + f1.getAbsolutePath());
        System.out.println("f2 absolute path: " + f2.getAbsolutePath());
        System.out.println("f3 absolute path: " + f3.getAbsolutePath());
    }
}
```

```
output:
File: a.txt
File 수정일: Wed Jan 31 00:26:25 JST 2024
File: b.txt
File 수정일: Wed Jan 31 00:26:32 JST 2024
Directory: source
f1 absolute path: C:\proj\intellij\javagold\in
f2 absolute path: C:\proj\intellij\javagold\in\a.txt
f3 absolute path: C:\proj\intellij\javagold\in\b.txt
```

**입출력스트림**

**기본 데이터형에서 사용되는 스트림**

|          | 입력           | 출력           |
| -------- | -------------- | -------------- |
| 바이너리 | InputStream    | OutputStream   |
| 텍스트   | BufferedReader | BufferedWriter |

**파일 입출력에서 사용되는 스트림**

|          | 입력            | 출력             |
| -------- | --------------- | ---------------- |
| 바이너리 | FileInputStream | FileOutputStream |
| 텍스트   | FileReader      | FileWriter       |

**편리한 기능을 추가한 출력 스트림**

다양한 타입 값(프리미티브)을 편리하게 출력함. 자동 플러시 기능도 있다.

|          | 출력        |
| -------- | ----------- |
| 바이너리 | PrintStream |
| 텍스트   | PrintWriter |



**Console 표준출력 클래스**

표준입출력을 사용하려면 Console클래스를 사용하면 된다. 

IDE를 사용하는등, 콘솔을 이용하지 못하는경우는 console이 null이 된다.

```java
import java.io.Console;
import java.io.PrintWriter;

public class Main {
    public static void main(String[] args) {

        Console console = System.console();
        PrintWriter pw = console.writer();

        while (true) {
            String str = console.readLine();
            if (str.equals("end")) {
                break;
            }
            pw.append("input data: " + str);
            pw.flush();
        }
    }
}
```

```cmd
PS C:\proj\intellij\javagold\src> java Main
okay
input data: okay
input data: end
PS C:\proj\intellij\javagold\src>
```



**java.nio.file.Path 인터페이스**

​	java.io.FIle클래스를 보완하여 나온 인터페이스

   유닉스 고유기능 이용가능, 디렉토리내에서 발생하는 이벤트 감시가 가능

   기본적인 파일 속성 이외에도 상세한 속성을 습득할 수 있는 네이티브 코드 이용이 가능

   고유의 예외정보를 습득할 수 있음



```java
public class Main {
    public static void main(String[] args) {

        // 1. file클래스로 path인터페이스를 습득
        File file = new File("in/soruce/x.txt");
        Path fileToPath = file.toPath();

        // 2. fileSystem인터페이스로 시스템정보를 받은사음 Path인터페이스를 습득
        FileSystem fileSystem = FileSystems.getDefault();
        Path fileSystemToPath = fileSystem.getPath("in/source/x.txt");

        // 3.Paths클래스로 path인터페이스를 습득
        Path pathsToPath = Paths.get("in/source/x.txt");


        System.out.println(fileToPath.getFileSystem());
        System.out.println(fileSystemToPath.getFileSystem());
        System.out.println(pathsToPath.getFileSystem());

    }
}
```



**java.nio.file.Files 클래스** 

파일이나 디렉토리를 조작(이동이나 복사 같은)을 도와주는 유틸리티 클래스



| 함수명             | 설명                                                         |
| ------------------ | ------------------------------------------------------------ |
| Files.copy         | 복사                                                         |
| Files.move         | 이동                                                         |
| Files.walkFileTree | nio.2에서 추가된 기능. 재귀적으로 하위 디렉토리 처리기능<br />FileVisitor를 이용하여 커스텀이 가능하게 정교한 처리가 가능하다. |
| Files.lines        | nio.2에서 추가된 기능. <br />지정한 텍스트 파일의 내용을 전부 stream으로 받는 함수 |
| Files.list         | nio.2에서 추가된 기능. <br />지정한 경로에 있는 모든것들을 Path의 요소로서 stream으로 받는 함수(지정한 디렉토리만) |
| Files.walk         | nio.2에서 추가된 기능. <br />재귀적으로 모두 검색하여, stream으로 받음. list의 업데이트판 |

```java
public class Main {
    public static void main(String[] args) throws IOException {

        Path filePath = Paths.get("in/source/x.txt");
        Path directoryPath = Paths.get("in");

        
        Stream<String> s = Files.lines(filePath);
        s.forEach(System.out::println);

        Stream<Path> s2 = Files.list(directoryPath);
        s2.forEach(System.out::println);

        Stream<Path> s3 = Files.walk(directoryPath);
        s3.forEach(System.out::println);

    }
}
```

```cmd
//// Files.lines -> 파일 내용 전부 스트림(string)으로 받음
a
b
c

//// Files.list -> 지정한 디텍토리만 전부 스트림(Path)로 받음
in\a.txt
in\b.txt
in\source

//// Files.list -> 본인포함 모든! 하위 디텍토리를 전부 스트림(Path)로 받음
in
in\a.txt
in\b.txt
in\source
in\source\x.txt
```



**Decorator 디자인 패턴**

입출력스트림을 다루는 여러가지 클래스를 조합하여 사용하기 위한 패턴



**연습문제**

```java
public class Main {
    public static void main(String[] args) {
        File file = new File("sample.txt"); // abcdefgh
        try(BufferedReader in = new BufferedReader(new FileReader(file))) {
            for(int i = 0; i < 3; i ++) {
                in.skip(i);
                System.out.print((char) in.read()); // a, c, f 출력 ※１
            }
            in.mark(6); // 현위치(g)에 마크 (상한치는 6) 상한치는 마크된 이후의 길이보다 커야함 ※２
            System.out.print(in.readLine()); //gh 출력 ※２
            in.reset(); // 마크한 곳으로 되돌림. 만약 마크함수가 없다면 예외발생
            System.out.print((char)in.read()); //g 출력 ※２

        } catch(Exception e) {
        e.printStackTrace();
        }
    }
}
```

**※１**  

a b c d e f g h 처음 skip(0)이 불리면 아무 스킵을 안하기 때문에 a가 출력이 되고, 그 후에 b가 출력이 될 차례인데 skip(1)이 불리면서 하나가 스킵이 되어 c가 출력이 됨. 마찬가지로 skip(2)가 불리면 f가 출력

**※２** 

g가 불릴 차례에서 마크가 되었기 때문에 g에 마크가 붙음. 남은 문자열은 gh(2)이므로 길이는 3이상이 되어야함. 작으면 에러발생함. 출력은 gh이 되고, 그 후에 reset으로 다시 되돌린 후 또 g가 출력이 됨.