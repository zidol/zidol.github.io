---
title:  "디자인 패턴"
excerpt: "각종 디자인 패턴..."

categories:
  - CS
tags:
  - [CS, 디자인 패턴, Design Pattern]

toc: true
toc_sticky: true
 
date: 2022-05-14
last_modified_at: 2022-05-14
---

# 디자인 패턴이란

---

- 과거 소프트웨어 개발 과정에서 발갼한 설계 노하우를 패턴으로 정리
- 범용적인 코드 스타일로 효율적 의사소통  가능
- 이미 검증된 구조이므로 설계를 빠르게 가능
- 목적에 따라 생성패턴, 구조패턴, 행동패턴으로 나누어짐

# 디자인 패턴의 종류

---

1. 생성(Creational) 패턴
    
    객체 생성하는 패턴
    
    - 추상팩토리 (Abstract Factory)
    - 빌더(Builder)
    - **팩토리 메서드(Factory Method)**
    - 프로토타입(Prototype)
    - **싱글턴(Singleton)**
2. 구조(Structural)패턴
    - **어댑터(Adapter)**
    - 브리지(Bridge)
    - 컴퍼지트(Composite)
    - 데커레이터(Decorator)
    - 플라이웨이트(Flyweight)
    - 프록시(Proxy)
3. 행동(Behavior)패턴

    메소드 관련 패턴

- 책임연쇄(Chain of Responsibility)
- 커맨드(Command)
- 인터프리터(Interpreter)
- 이터레이터(Iterator)
- 미디에이터(Mediator)
- 비지터(Visitor)

- 메멘토(Memento)
- 옵서버(Observer)
- 스테이트(State)
- 스트래티지(Strategy)
- **템플릿 메서드(Template Method)**

# 대표 적인 디자인 패턴

---

## 1. 싱글톤 패턴

 1) 인스턴스를 오직 1개만 생성하는 패턴

- 스프링 컨테이너에서 싱글톤패턴을 주로 사용
- DB커넥션
- 쓰레드풀

2) 싱글톤 패턴 인스턴스 생성방식

1. 이른 초기화**(Thread-Safe)**

```java
public class SingletonService {

    private static final SingletonService instance = new SingletonService();

    public static SingletonService getInstance() {
        return instance;
    }

    private  SingletonService() {

    }

    public void logic() {
        System.out.println("싱글톤 객체 로직 호출");
    }
}
```

이른 초기화는 클래스가 호출될 때 인스턴스를 생성하는 방법이다.  다만 인스턴스를 사용하지 않아도 생성하기 때문에 효율성이 떨어집니다.(사용하지 않아도 자리를 잡음) 프로그램 실행 시, 전역에서의 싱글톤 클래스의 생성은 알 수 없으므로 해당 단계에서 해당 싱글톤 클래스와 다른 클래스 또는 함수에서 싱글톤 클래스를 참조하고자 하면 문제가 생길 수 있다.

1. 게이른 초기화
    
    ```java
    public class SignltonService {
    
    	private static SingletonSerivce instance;
    	
    	private SingletonService () {}
    
    	public static SingletonService getInstance() {
    			if(instance == null) {
    					instance = new SingletonService();
    			}
    			return instance;
    	}
    }
    ```
    
    늦은 초기화는 인스턴스를 실제로 사용할 시점에 생성하는 방법이다. 인스턴스를 실제로 생성하지 않으면 생성하지 않기에 이른 초기화보다 효율성이 좋긴 하지만 두 스레드가 동시에 싱글톤 인스턴스에 접근하고 생성이 안된 것을 확인하여 생성한다면 중복으로 생성할 수 있다는 문제가 있을 수 있다.
    
2. Holder
    
    ```java
    public class SingletonService {
    
        //private construct
        private SingletonService() {}
    
        private static class InnerInstanceClazz {
            private static final SingletonService instance 
    																							= new SingletonService();
        }
    
        public static SingletonService getInstance() {
            return InnerInstanceClazz.instance;
        }
    }
    ```
    
    JVM의 클래스 로더 메커니즘과 클래스의 로드 시점을 이용하여 내부 클래스를 통해 생성 시킴으로써 쓰레드 간의 동기화 문제를 해결한다.
    
    > 출처
    [https://elfinlas.github.io/2019/09/23/java-singleton/](https://elfinlas.github.io/2019/09/23/java-singleton/)
    [https://velog.io/@taeheon95/Singleton-Pattern](https://velog.io/@taeheon95/Singleton-Pattern)
    > 

## 2. 팩토리 메소드 패턴

- 객체를 직접 생성하지 않고 객체를 생성하는 Factory 객체를 사용하는 패턴으로서 객체 간의 결합도를 느슨하게 하여 서로간의 종속성을 낮춘다.

![https://gmlwjd9405.github.io/images/design-pattern-factory-method/factory-method-pattern.png](https://gmlwjd9405.github.io/images/design-pattern-factory-method/factory-method-pattern.png)

- 역할이 수행하는 작업
    - Product
        - 팩토리 메서드로 생성될 객체의 공통 인터페이스
    - ConcreteProduct
        - 구체적으로 객체가 생성되는 클래스
    - Creator
        - 팩토리 메서드를 갖는 클래스
    - ConcreteCreator
        - 팩토리 메서드를 구현하는 클래스로 ConcreteProduct 객체를 생성
- 팩토리 메서드 패턴의 개념과 적용 방법
1. 객체 생성을 전담하는 별도의 Factory 클래스 이용
    1. 스트래티지 패턴과 싱글턴 패턴을 이용한다.
2. 상속 이용: 하위 클래스에서 적합한 클래스의 객체를 생성

## 예제 Code

팩토리 패턴에 사용되는 슈퍼 클래스는 인터페이스나 추상 클래스, 혹은 그냥 평범한 자바 클래스여도 상관없음

### Super Class

```java
package factory;

//Super Class
public abstract class Computer {

    public abstract String getRAM();
    public abstract String getHDD();
    public abstract String getCPU();

    @Override
    public String toString(){
        return "RAM= "+this.getRAM()+", HDD="+this.getHDD()+", CPU="+this.getCPU();
    }
}
```

### Sub Class -1

```java
package factory;

public class Pc extends Computer {

    private String ram;
    private String hdd;
    private String cpu;

    public Pc(String ram, String hdd, String cpu){
        this.ram = ram;
        this.hdd = hdd;
        this.cpu = cpu;
    }
    @Override
    public String getRAM() {
        return this.ram;
    }

    @Override
    public String getHDD() {
        return this.hdd;
    }

    @Override
    public String getCPU() {
        return this.cpu;
    }

}
```

### Sub Class - 2

```java
package factory;

public class Server extends Computer {

    private String ram;
    private String hdd;
    private String cpu;

    public Server(String ram, String hdd, String cpu){
        this.ram = ram;
        this.hdd = hdd;
        this.cpu = cpu;
    }
    @Override
    public String getRAM() {
        return this.ram;
    }

    @Override
    public String getHDD() {
        return this.hdd;
    }

    @Override
    public String getCPU() {
        return this.cpu;
    }
}

```

*PC 클래스와 Server 클래스 모두 Computer 클래스를 상속*

### Factory Class

```java
package factory;

public class ComputerFactory {
    public static Computer getComputer(String type, String ram
																			 , String hdd, String cpu){
        if("PC".equalsIgnoreCase(type))
            return new Pc(ram, hdd, cpu);
        else if("Server".equalsIgnoreCase(type))
            return new Server(ram, hdd, cpu);

        return null;
    }
}

```

ComputerFactory 클래스의 getComputer 메소드를 살펴보면 **static** 메소드로 구현되었다는 점을 살펴볼 수 있고, 메소드 내부 코드를 보면 type의 값이 "PC"일 경우 PC 클래스의 인스턴스를, "Server"일 경우 Server 클래스의 인스턴스를 리턴하는 것을 볼 수 있다.

**따라서 팩토리 메소드 패턴을 사용하게 된다면 인스턴스를 필요로 하는 Application에서 Computer의 Sub 클래스에 대한 정보는 모른 채 인스턴스를 생성할 수 있게 된다.**

이렇게 구현한다면 앞으로 Computer 클래스에 더 많은 Sub 클래스가 추가된다 할지라도 getComputer()를 통해 인스턴스를 제공받던 Application의 코드는 수정할 필요가 없게 된다.

```java
package factory;

public class TestFactory {
    public static void main(String[] args) {
        Computer pc = ComputerFactory.getComputer("pc","2 GB","500 GB","2.4 GHz");
        Computer server = ComputerFactory.getComputer("server","16 GB","1 TB","2.9 GHz");
        System.out.println("Factory PC Config::"+pc);
        System.out.println("Factory Server Config::"+server);
    }
}
```

```bash
Factory PC Config::RAM= 2 GB, HDD=500 GB, CPU=2.4 GHz
Factory Server Config::RAM= 16 GB, HDD=1 TB, CPU=2.9 GHz
```

### **팩토리 패턴의 장점**

1. 팩토리 패턴은 클라이언트 코드로부터 서브 클래스의 인스턴스화를 제거하여 **서로 간의 종속성을 낮추고, 결합도를 느슨하게 하며(Loosely Coupled), 확장을 쉽게 합니다.**예를 들어, 위 예제에서 작성한 클래스 중 PC class에 대해 수정 혹은 삭제가 일어나더라도 클라이언트는 알 수 없기 때문에 코드를 변경할 필요도 없음
2. 팩토리 패턴은 클라이언트와 구현 객체들 사이에 추상화를 제공

> 출처 
[https://gmlwjd9405.github.io/2018/08/07/factory-method-pattern.html](https://gmlwjd9405.github.io/2018/08/07/factory-method-pattern.html)
[https://readystory.tistory.com/117#recentComments](https://readystory.tistory.com/117#recentComments)
> 

## 3. 어댑터 패턴

- 어댑터를 사용하여 호환되지 않는 인터페이스를 호환되도록 하는 패턴

 

![어댑터 패턴 클래스 다이어그램]({{ site.url }}{{ site.baseurl }}/assets/images/adapterPatternUml.png)

어댑터 패턴 클래스 다이어그램

> 출처
[https://jusungpark.tistory.com/22](https://jusungpark.tistory.com/22)
> 

## 4. 템플릿 메소드 패턴

어떤 작업을 처리하는 일부분을 서브 클래스로 캡슐화해 전체 일을 수행하는 구조는 바꾸지 않으면서 특정 단계에서 수행하는 내역을 바꾸는 패턴

- 즉, 전체적으로는 동일하면서 부분적으로는 다른 구문으로 구성된 메서드의 코드 중복을 최소화 할 때 유용하다.
- 다른 관점에서 보면 동일한 기능을 상위 클래스에서 정의하면서 확장/변화가 필요한 부분만 서브 클래스에서 구현할 수 있도록 한다.
- 예를 들어, 전체적인 알고리즘은 상위 클래스에서 구현하면서 다른 부분은 하위 클래스에서 구현할 수 있도록 함으로써 전체적인 알고리즘 코드를 재사용하는 데 유용하도록 한다.
‘행위(Behavioral) 패턴’의 하나 (아래 참고)
[https://gmlwjd9405.github.io/2018/07/13/template-method-pattern.html](https://gmlwjd9405.github.io/2018/07/13/template-method-pattern.html)
- 상속을 통해 부모 클래스의 기능을 확장할때 사용하는 방법
    - 부모 클래스에서 변하지 않는 기능을 구현해두고 자식 클래스에서 확장할 기능을 구현
    - 예시) 자바의 Abstract Map : HashMap, TreeMap의 공통 구현

### **장점**

1. 중복코드를 줄일 수 있다.
2. 자식 클래스의 역할을 줄여 핵심 로직의 관리가 용이하다.
3. 좀더 코드를 객체지향적으로 구성할 수 있다.

### **단점**

1. 추상 메소드가 많아지면서 클래스 관리가 복잡해진다.
2.  클래스간의 관계와 코드가 꼬여버릴 염려가 있다.