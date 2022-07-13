---
title:  "Java final"
excerpt: "final 키워드..."

categories:
  - java
tags:
  - [java, final]

toc: true
toc_sticky: true
 
date: 2022-07-13
last_modified_at: 2022-07-13
---
# final

final 키워드는 변수(variable), 메서드(method), 또는 클래스(class)에 사용될 수 있다.

final 키워드는 어떤 곳에 사용되냐에 따라 다른 의미를 가진다. 하지만 final 키워드를 붙이면 무언가를 제한한다는 의미를 가지는 것은 공통적이 성격이있다.

> the final keyword is used in several contexts to define an entity that can only be assigned once.
> 

# 변수(variable)

- **변수에 final을 붙이면 이 변수는 수정할 수 없다는 의미**를 가진다.
- 수정될 수 없기 때문에 초기화 값은 필수적
- 객체안의 변수라면 생성자, static 블럭을 통한 초기화까지는 허용.

**수정 할 수 없다는 범위는 그 변수의 값에 한정한다.** 다른 객체를 참조할때  참조하는 객체의 내부의 값은 변경할 수 있다라는 의미이다.  

아래 예시는 기본형 변수는 값이 변경하지 못하고 참조형 변수는 그 객체 내부의 값은 변경할 수 있지만 인스턴스를 재생성은 불가능하다.

```java
public class Service {

  public void variableFinal() {

    final int value = 2;
    final Person person = new Person("손흥민", 30);

    System.out.println("value = " + value);
    System.out.println("person_1 = " + person);

    // value = 2; // 컴파일 에러
    person.setAge(29);
    person.setName("케인");

    System.out.println("person_2 = " + person);
    // person = new Person("zidol", 11); // 컴파일 에러
  }
}

class Person {

  private String name;

  private int age;

  //.. get, set, toString 생략
}
```

```
value = 2
person_1 = Person{name='손흥민', age=30}
person_2 = Person{name='케인', age=29}
```

이미지로 나타내면 아래와 같습니다. 메서드에 있는 변수들은 stack 메모리에 저장된다. 이 변수들은 각각 값을 가지는데 기본형 변수(primitive)는 내부에 값을 가지고 참조형 변수는 객체의 주소를 가진다. 따라서 참조형 변수는 객체에 있는 값을 알 수 있다.

final 키워드를 변수에 사용하면 이 영역에만 변경할 수 없다는 영향을 미칩니다. 기본형 변수라면 값을 변경하지 못하고 참조형 변수라면 가리키는 객체를 변경하지 못하는 것입니다. **후자의 경우 현재 가리키고 있는 객체의 내부의 값은 final의 영향 밖에 있기 때문에 변경이 가능합니다.**

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/fd1bff89-ba4e-4ddc-b0a6-73638d4cd6f5/img.png](https://zidols.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Ffd1bff89-ba4e-4ddc-b0a6-73638d4cd6f5%2Fimg.png?table=block&id=83d7c735-9270-421f-aeef-193e33401d46&spaceId=370dbc5e-872d-4d9e-9f3a-f7113cda9427&width=1400&userId=&cache=v2)

위 예제는 final을 메서드 내부 변수에 선언했기 때문에 stack 메모리를 기점으로 하지만 만약 객체 내부 변수를 final로 둔다면 이미지는 person 메모리 내부에서 제한하는 것으로 달라집니다. 그리고 배열 역시 객체이기 때문에 내부의 값을 변경하는것은 final이 제한하지 못합니다.

**변수를 final로 선언하면 초기화 전에 사용한다면 컴파일 에러가 발생합니다.** 각 변수 타입별 일반적인 초기화 방법은 아래와 같습니다.

- 메서드 내부 변수
    - 선언시 초기화
    - 사용하기 전 초기화

```java
public void variableFinal() {

  final int value = 2;
  final int value_2;

  System.out.println("value = " + value);

  value_2 = 3;
  System.out.println("person_2 = " + value_2);
}
```

- 객체 멤버 변수
    - 선언시 초기화
    - 생성자를 이용한 초기화
    - 초기화 block을 이용한 초기화

```java
public void variableFinal() {
  Person person = new Person("손흥민");
  System.out.println("person = " + person);
}

class Person {

  private final String name;

  private final int age;
  {
    age = 30;
  }

  private final String email = "aaa@aaa.com";

  public Person(String name) {
    this.name = name;
  }

  @Override
  public String toString() {
    return "Person{" +
        "name='" + name + '\'' +
        ", age=" + age +
        ", email='" + email + '\'' +
        '}';
  }
}

```

- static 변수
    - 선언시 초기화
    - static 초기화 block을 이용한 초기화

```java
class Person {

  private static final String DEFAULT_NAME = "염광호";

  private static final int DEFAULT_AGE;
  static {
    DEFAULT_AGE = 10;
  }

  ... person 객체 내부 후략
```

## 메서드(method)

**메서드에 final을 붙이면 override를 제한**하게 됩니다. 클래스를 상속하게되면 우리는 해당 클래스의 protected, public의 접근 제어자(access-modifier)를 가진 메서드를 상속해서 재구현을 할 수 있습니다. 즉, 동일한 메서드지만 다르게 동작하게 만들 수 있다는 것입니다. 아래 예제를 보겠습니다. 아래를 보시면 `Person` 클래스에 `speech`라는 메서드가 있습니다. 본인을 소개하는 메서드입니다. 그리고 이를 상속받은 `Korean`이라는 클래스가 있습니다. 해당 클래스는 `speech`에 나이까지 함께 소개하고 있습니다. 사실 `Person` 클래스를 만들때 자기소개하는 `speech` 메서드는 모든 나라언어에서 공통으로 사용하고 싶을 수 있습니다. `Korean` 클래스 처럼 각 나라별로 바꿔버리는것이 문제가 될 수 있습니다. 이렇게 **상속 받은 클래스에서 해당 메서드를 수정해서 사용하지 못하도록 할 수 있는것이 메서드에 final을 붙이는 것**입니다.

```java
class Person {

  private final String name;

  private final int age;

  public Person(String name, int age) {
    this.name = name;
    this.age = age;
  }

  public void speech() {
    System.out.println("나는 " + name + " 입니다.");
  }
}

class Korean extends Person {

  public Korean(String name, int age) {
    super(name, age);
  }

  @Override
  public void speech() {
    System.out.println("나는 " + name + " 이며, " + age + " 입니다.");
  }
}
```

## 클래스(Class)

**final 키워드를 클래스에 붙이면 상속 불가능 클래스**가 됩니다. 즉, 다른 클래스에서 상속하여 재정의를 할 수 없는 것입니다. 대표적인 클래스로 Integer와 같은 랩퍼(Wrapper) 클래스가 있습니다. 클래스 설계시 재정의 여부를 생각해서 재정의 불가능하게 사용하고 싶다면 final로 등록하는게 추후 유지보수차원에서 좋습니다.

```
public final class Integer extends Number implements Comparable<Integer> {

}
```

![https://s3-us-west-2.amazonaws.com/secure.notion-static.com/67bce6d8-00da-472d-a024-0c644b65626e/img.png](https://zidols.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F67bce6d8-00da-472d-a024-0c644b65626e%2Fimg.png?table=block&id=9e50a958-29fd-4158-9f3a-1a2d1b6f6de2&spaceId=370dbc5e-872d-4d9e-9f3a-f7113cda9427&width=1400&userId=&cache=v2)

# 요약

Java에서의 final은 Immutable/Read-only 속성을 선언하는 지시어입니다.

**클래스, 함수, 변수가 변하지 못하도록 의도하고 싶다면 final로 선언하자.**

출처  
[https://sabarada.tistory.com/148](https://sabarada.tistory.com/148)  
[https://blog.lulab.net/programming-java/java-final-when-should-i-use-it/](https://blog.lulab.net/programming-java/java-final-when-should-i-use-it/)