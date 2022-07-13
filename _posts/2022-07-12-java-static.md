---
title:  "Java static"
excerpt: "static 키워드..."

categories:
  - java
tags:
  - [java, static]

toc: true
toc_sticky: true
 
date: 2022-07-12
last_modified_at: 2022-07-12
---
# Static

Java에서 Static 키워드를 사용한다는 것은 메모리에 한번 할당되어 프로그램이 종료될 때 해제되는 것을 의마한다. 일반적으로 우리가 만든 Class는 Static 영역에 생성되고, new 연산을 통해 생성한 객체를 Heap영역에 생성된다. 객체 생성시에 할당된 Heap 영역의 메모리는 Garbage Collector를 통해 수시로 관리를 받는다. 하지만 Static 키워드를 통해 Static 영역에 할당된 메모리는 모든 객체가 공유하는 메모리라는 장점을 지니지만, Garbage Collcector의 관리 영역 밖에 존재하므로 Static을 자주 사용하면 프로그램의 종료시까지 메모리가 할당된 채 존재하므로 자주 사용하게 되면 시스템의 포퍼먼스에 악역향을 주게된다.

![[https://coding-factory.tistory.com/524](https://coding-factory.tistory.com/524)](https://zidols.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F0b706ea0-a98f-4199-bc56-57d2ac569456%2FUntitled.png?table=block&id=52ad8e19-936b-41d9-b12e-d16011e13742&spaceId=370dbc5e-872d-4d9e-9f3a-f7113cda9427&width=2000&userId=&cache=v2)

[https://coding-factory.tistory.com/524](https://coding-factory.tistory.com/524)

# Static 변수 특징

Static 변수는 클래스 변수입니다. 객체를 생성하지 않고도 Static 자원에 접근이 가능합니다.

```java
class MyCalculator {
    public static String appName = "MyCalculator";

    public static int add(int x, int y) {
        return x + y;
    }

    public int min(int x, int y) {
        return x - y;
    }
}
public class Test {

    public static void main(String[] args) {
        MyCalculator.add(1, 2);   //  static 메소드 이므로 객체 생성 없이 사용 가능
        MyCalculator.min(1, 2);   //  static 메소드가 아니므로 객체 생성후에 사용가능

        MyCalculator cal = new MyCalculator();
        cal.add(1, 2);   // o 가능은 하지만 권장하지 않는 방법
        cal.min(1, 2);   // o
    }
}
```

## Static 변수(정적 변수)

- 메모리에 고정적으로 할당되어, 프로그램이 종료될 때 해제되는 변수

Java에서 Static 변수는 메모리에 한번 할당되어 프로그램이 종료될 때 해제되는 변수로,
메모리에 한번 할당되므로 여러 객체가 해당 메모리를 공유하게 된다.

```java
class HouseAhn {
    static String lastname = "Ahn";
}

public class Sample {
    public static void main(String[] args) {
        HouseAhn lee1 = new HouseAhn();
        HouseAhn lee2 = new HouseAhn();
    }
}
```

위와 같이 lastname 변수에 static 키워드를 붙이면 자바는 메모리 할당을 딱 한번만 하게 되어 메모리 사용에 이점이 있다.

> ※ 만약 HouseAhn 클래스의 lastname 값이 변경되지 않기를 바란다면 static 키워드 앞에 final이라는 키워드를 붙이면 된다. final 키워드는 한번 설정되면 그 값을 변경할수 없다. 변경하려고 하면 오류가 발생한다.
> 

static을 사용하는 또 한가지 이유로 **공유** 개념을 들 수 있다. static 으로 설정하면 같은 곳의 메모리 주소만을 바라보기 때문에 static 변수의 값을 공유하게 되는 것이다.

# Static Method

```java
class Counter  {
    static int count = 0;
    Counter() {
        count++;
        System.out.println(count);
    }

    public static int getCount() {
        return count;
    }
}

public class Sample {
    public static void main(String[] args) {
        Counter c1 = new Counter();
        Counter c2 = new Counter();

        System.out.println(Counter.getCount());  // 스태틱 메서드는 클래스를 이용하여 호출
    }
}
```

Counter 클래스에 `getCount()` 라는 static 메소드가 추가되었다. 메소드 앞에 static 키워드를 붙이면 `Counter.getCount()` 와 같이 객체 생성없이 클래스를 통해 메서드를 직접 호출할 수 있다.

> ※ 스태틱 메소드 안에서는 객체변수 접근이 불가능 하다. 위 예에서는 count 변수가 static 변수이기 때문에 스태틱 메소드(static method)에서 접근이 가능하다.
> 

보통 스태틱 메소드는 유틸리티성 메소드를 작성할 때 많이 사용된다. 예를 들어 "오늘의 날짜 구하기", "숫자에 콤마 추가하기" 등의 메소드를 작성할 때에는 클래스 메소드를 사용하는 것이 유리하다.

주로 util성 메소드를 Util 클래스로 생성하여 사용한다.

```java

import java.text.SimpleDateFormat;
import java.util.Date;

class Util {
    public static String getCurrentDate(String fmt) {
        SimpleDateFormat sdf = new SimpleDateFormat(fmt);
        return sdf.format(new Date());
    }
}

public class Sample {
    public static void main(String[] args) {
        System.out.println(Util.getCurrentDate("yyyyMMdd"));  // 오늘 날짜 출력
    }
}
```

## 싱글톤 패턴 (singleton pattern)

싱글톤은 단 하나의 객체만을 생성하게 강제하는 패턴이다. 즉 클래스를 통해 생성할 수 있는 객체는 Only One, 즉, 한 개만 되도록 만드는 것이 싱글톤이다.

```java
class Singleton {
    private Singleton() {
    }
}

public class Sample {
    public static void main(String[] args) {
        Singleton singleton = new Singleton();  // 컴파일 오류가 발생한다.
    }
}

```

위와 같은 코드를 작성하면 컴파일 에러가 발생한다. 왜냐하면 Singleton 클래스의 생성자에 private 키워드로 다른 클래스에서 Singleton 클래스의 생성자로의 접근을 막았기 때문이다. 이렇게 생성자를 private 으로 만들어 버리면 다른 클래스에서 Singleton 클래스를 `new` 를 이용하여 생성할 수 없게 된다.

`new`를 이용하여 무수히 많은 객체를 생성한다면 싱글톤의 정의에 어긋나다. 그래서 일단 `new`로 객체를 생성할 수 없도록 막은 것이다.

그렇다면 Singletone 클래스의 객체는 어떻게 생성할 수 있을까? 다음처럼 코드를 작성 해 보자.

```java
class Singleton {
    private Singleton() {
    }

    public static Singleton getInstance() {
        return new Singleton();  // 같은 클래스이므로 생성자 호출이 가능하다.
    }
}

public class Sample {
    public static void main(String[] args) {
        Singleton singleton = Singleton.getInstance();
    }
}

```

위와 같이 코드를 변경하면 이제 getInstance라는 static 메소드를 이용하여 Singleton 클래스의 객체를 생성할수 있다. 하지만 getInstance를 호출 할 때마다 새로운 객체가 생성된다. 그렇다면 싱글톤이 아니다. 어떻게 해야 할까?

다음처럼 코드를 작성해 보자.

```java
class Singleton {
    private static Singleton instnace;
    private Singleton() {
    }

    public static Singleton getInstance() {
        if(instnace == null) {
            instnace = new Singleton();
        }
        return instnace;
    }
}

public class Sample {
    public static void main(String[] args) {
        Singleton singleton1 = Singleton.getInstance();
        Singleton singleton2 = Singleton.getInstance();
        System.out.println(singleton1 == singleton2);  // true 출력
    }
}

```

Singleton 클래스에 one 이라는 static 변수를 두고 getInstance 메소드에서 instance 값이 null 인 경우에만 객체를 생성하도록 하여 instance 객체가 딱 한번만 만들어지도록 했다.

getInstance 메소드의 동작원리를 살펴보자.

최초 getInstance가 호출 되면 one이 null이므로 `new`에 의해서 객체가 생성이 된다. 이렇게 한번 생성이 되면 one은 static 변수이기 때문에 그 이후로는 null이 아니게 된다. 그런 후에 다시 getInstance 메소드가 호출되면 이제 instance는 null이 아니므로 이미 만들어진 싱글톤 객체인 instance을 항상 리턴한다.

main 메소드에서 getInstance를 두번 호출하여 각각 얻은 객체가 같은 객체인지 조사 해 보았다. 역시 예상대로 "true"가 출력되어 같은 객체임을 확인 할 수 있다.

출처  
[https://wikidocs.net/228](https://wikidocs.net/228)  
[https://mi-nya.tistory.com/251](https://mi-nya.tistory.com/251)