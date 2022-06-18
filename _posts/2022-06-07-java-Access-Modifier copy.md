---
title:  "Java 접근제어자 (Access Modifier)"
excerpt: "접근제어자"

categories:
  - java
tags:
  - [java, Access Modifier]

toc: true
toc_sticky: true
 
date: 2022-06-07
last_modified_at: 2022-06-07
---
# **접근제어자 (Access Modifier)**

## **접근 제어자**

객체 지향에서 정보 은닉(data hiding)이란 사용자가 굳이 알 필요가 없는 정보는 사용자로부터 숨겨야 한다는 개념입니다. 그렇게 함으로써 사용자는 언제나 최소한의 정보만으로 프로그램을 손쉽게 사용할 수 있게 된다.

자바에서는 이러한 정보 은닉을 위해 접근 제어자(access modifier)라는 기능을 제공하고 있습니다.접근 제어자를 사용하면 클래스 외부에서의 직접적인 접근을 허용하지 않는 멤버를 설정하여 정보 은닉을 구체화할 수 있다.

자바에서는 다음과 같은 네 가지의 접근 제어자를 제공한다.

1. private
2. default
3. protected
4. public

### **private**

접근제어자가 private으로 설정되었다면 private 이 붙은 변수, 메소드는 해당 클래스에서만 접근이 가능하다.

```java
public class Sample {
    private String secret;
    private String getSecret() {
        return this.secret;
    }
}
```

위 코드의 secret 변수와 getSecret 메소드는 오직 Sample 클래스에서만 접근이 가능하고 다른 클래스에서는 접근이 불가능하다.

![Untitled](https://zidols.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F7a8f13a3-9a2c-45a8-8ed4-222606163106%2FUntitled.png?table=block&id=b1f2018b-c8ca-43ab-b884-1425807f6a7a&spaceId=370dbc5e-872d-4d9e-9f3a-f7113cda9427&width=2000&userId=&cache=v2)

### **default**

접근 제어자를 별도로 설정하지 않는다면 접근 제어자가 없는 변수, 메소드는 default 접근 제어자가 되어 해당 패키지 내에서만 접근이 가능하다.

> ※ 우리가 지금까지 진행했던 대부분의 예제는 특별한 경우가 아니라면 모두 default 접근 제어자를 사용했다.
> 

```java
package house;  // 패키지가 동일

public class ZidolAhn {
    String lastname = "ahn";  // lastname은 default 접근제어자로 설정된다.
}
```

```java
package house;  // 패키지가 동일

public class Home {
    String lastname = "park";

    public static void main(String[] args) {
        ZidolAhn ahn = new ZidolAhn();
				System.out.println(ahn.lastname); // ZidolAhn 클래스의 lastname 변수 사용가능
    }
}
```

HouseKim과 HousePark의 패키지는 **house**로 동일하다. 따라서 HousePark 클래스에서 HouseKim의 lastname 변수에 접근이 가능하다.

![Untitled](https://zidols.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F5b36694c-f0cc-4573-8204-2a088199c5e7%2FUntitled.png?table=block&id=2a67ff4b-9c53-4980-a5b9-f955d4e65e84&spaceId=370dbc5e-872d-4d9e-9f3a-f7113cda9427&width=1230&userId=&cache=v2)

### **protected**

접근제어자가 protected로 설정되었다면 protected가 붙은 변수, 메소드는 동일 패키지의 클래스 또는 해당 클래스를 상속받은 다른 패키지의 클래스에서만 접근이 가능하다.

클래스의 protected 멤버에 접근할 수 있는 영역은 다음과 같다.

1. 이 멤버를 선언한 클래스의 멤버
2. 이 멤버를 선언한 클래스가 속한 패키지의 멤버
3. 이 멤버를 선언한 클래스를 상속받은 자식 클래스(child class)의 멤버

```java
package home;  // 패키지가 서로 다르다.

public class ZidolAhn {
    protected String lastname = "ahn";
}
```

```java
package home.person;  // 패키지가 서로 다르다.

import home.ZidolAhn;

public class Home extends ZidolAhn {  // HousePark을 상속했다.
    public static void main(String[] args) {
        Home home = new Home();
        System.out.println(home.lastname);  // 상속한 클래스의 protected 변수는 접근이 가능하다.
    }
}
```

![Untitled](https://zidols.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fbcc42170-7fe6-48c6-a8ea-b0e1133a1928%2FUntitled.png?table=block&id=61269597-56c1-4050-ab36-919f471b0a31&spaceId=370dbc5e-872d-4d9e-9f3a-f7113cda9427&width=1220&userId=&cache=v2)

ZidolAhn 클래스를 상속한 Home 클래스의 패키지는 `home.person`으로 HousePark의 패키지인 `home`와 다르지만 HousePark의 lastname 변수가 protected이기 때문에 `home.lastname`과 같은 접근이 가능하다. 만약 lastname의 접근제어자가 protected 가 아닌 default 접근제어자였다면 `home.lastname` 문장은 컴파일 오류가 발생할 것이다.

### **public**

접근제어자가 public으로 설정되었다면 public 접근제어자가 붙은 변수, 메소드는 어떤 클래스에서라도 접근이 가능하다.

```java
package house;

public class ZidolAhn {
    protected String lastname = "ahn";
    public String info = "this is public message.";
}
```

위 예제의 ZidolAhn의 info 변수는 public 접근 제어자가 붙어 있으므로 어떤 클래스라도 접근이 가능하다.

![Untitled](https://zidols.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F350763ff-3233-4078-a9cb-d1b0a4ab9332%2FUntitled.png?table=block&id=c21a0242-5324-4047-96c3-ff199975e4dd&spaceId=370dbc5e-872d-4d9e-9f3a-f7113cda9427&width=1220&userId=&cache=v2)

## **클래스, 메소드의 접근 제어자**

이상과 같이 총 4개의 접근제어자에 대해서 알아보았다. 위의 예제는 변수만을 대상으로 설명했지만 클래스나 메소드도 마찬가지의 접근제어자 규칙을 따른다.

접근제어자를 모두 public으로 설정해도 프로그램은 잘 동작할 것이다. 하지만 접근제어자를 이용하면 프로그래머의 코딩 실수를 방지할 수 있고 기타 위험요소를 제거할 수 있는 등의 장점이 있다

## 정리

`private -> default -> protected -> public` 순으로 보다 많은 접근을 허용한다.

| 접근제어자 | 같은 클래스의 멤버 | 같은 패키지의 멤버 |  자식 클래스의 멤버 |  그 외의 영역 |
| --- | --- | --- | --- | --- |
|  public | o | o | o | o |
| protected | o | o  | o  | x |
| default | o | o | x | x |
| private | o | x | x | x |

출처  
[http://www.tcpschool.com/java/java_modifier_accessModifier](http://www.tcpschool.com/java/java_modifier_accessModifier)  
[https://wikidocs.net/232](https://wikidocs.net/232)  