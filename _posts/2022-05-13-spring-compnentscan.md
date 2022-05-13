---
title:  "Spring - Component Scan"
excerpt: "Component Scan"

categories:
  - Spring
tags:
  - [Spring, Component Scan]

toc: true
toc_sticky: true
 
date: 2022-05-13
last_modified_at: 2022-05-13
---
# **컴포넌트 스캔과 의존관계 자동 주입 시작하기**

지금까지 스프링 빈을 등록할 때는 자바 코드의 @Bean이나 XML의 <bean> 등을 통해서 설정 정보에 직접 등록할 스프링 빈을 나열했다.  

스프링 빈이 수십, 수백개가 되면 일일이 등록하기도 귀찮고, 설정 정보도 커지고, 누락하는 문제도 발생한다.  

스프링은 설정 정보가 없어도 자동으로 스프링 빈을 등록하는 컴포넌트 스캔 이라는 기능을 제공한다. 또한  의존관계도 자동으로 주입하는 @Autowired 라는 기능도 제공한다.  

```java
import hello.core.member.MemberRepository;
import hello.core.member.MemoryMemberRepository;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.ComponentScan;
import org.springframework.context.annotation.Configuration;
import org.springframework.context.annotation.FilterType;

@Configuration
@ComponentScan(
// 탐색할 패키지의 시작 위치 지정 , default = @ComponentScan이 붙은 클래스의 패키지 부터
//        basePackages = "hello.core.member",
//AppConfig.java 및 test 하면서 생성한  것들 제외 시키기 위해.
        excludeFilters = @ComponentScan.Filter(type = FilterType.ANNOTATION
, classes = Configuration.class))
public class AutoAppConfig {

}
```

- 컴포넌트 스캔을 사용하려면 먼저 @ComponentScan 을 설정 정보에 붙여주면 된다.
- 다른 설정 클래스에 @Configuration 어노테이션이 있는 클래스를 제외하기 위해 @ComponentScan에 excludeFilters 설정을 하였다.

**컴포넌트 스캔**은 이름 그대로 **@Component** 애노테이션이 붙은 클래스를 스캔해서 스프링 빈으로 등록한다. @Component 를 붙여줘야 한다.

> 참고: **@Configuration** 이 컴포넌트 스캔의 대상이 된 이유도 **@Configuration** 소스코드를 열어보면 **@Component** 애노테이션이 붙어있기 때문이다.
> 

## **MemoryMemberRepository @Component 추가**

```java
@Component
public class MemoryMemberRepository implements MemberRepository {
	//....
}
```

## **MemberServiceImpl @Component, @Autowired 추가**

```java
@Component
  public class MemberServiceImpl implements MemberService {
      private final MemberRepository memberRepository;
      @Autowired
      public MemberServiceImpl(MemberRepository memberRepository) {
          this.memberRepository = memberRepository;
      }
}
```

- 이전에 AppConfig에서는 @Bean 으로 직접 설정 정보를 작성했고, 의존관계도 직접 명시했다. 이제는
이런 설정 정보 자체가 없기 때문에, 의존관계 주입도 이 클래스 안에서 해결해야 한다.
- @Autowired 는 의존관계를 자동으로 주입해준다.
- @Autowired 를 사용하면 생성자에서 여러 의존관계도 한번에 주입받을 수 있다.

## **AutoAppConfig Test**

```java
package hello.core.scan;

import hello.core.AutoAppConfig;
import hello.core.member.MemberRepository;
import hello.core.member.MemberService;
import hello.core.order.OrderServiceImpl;
import org.assertj.core.api.Assertions;
import org.assertj.core.api.ObjectAssert;
import org.junit.jupiter.api.Test;
import org.springframework.context.annotation.AnnotationConfigApplicationContext;

import static org.assertj.core.api.Assertions.*;

public class AutoAppConfigTest {

    @Test
    void basicScan() {

        AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext(AutoAppConfig.class);

        MemberService memberService = ac.getBean(MemberService.class);

        assertThat(memberService).isInstanceOf(MemberService.class);

        OrderServiceImpl bean = ac.getBean(OrderServiceImpl.class);
        MemberRepository memberRepository = bean.getMemberRepository();
        System.out.println("memberRepository = " + memberRepository);
    }
}
```

아래 로그를 잘 보면 컴포넌트 스캔이 잘 동작하는 것을 확인할 수 있다.

```bash
15:47:21.035 [Test worker] DEBUG org.springframework.context.annotation.ClassPathBeanDefinitionScanner - Identified candidate component class: file [/Users/zidol/Desktop/back-end/SpringCore/build/classes/java/main/hello/core/member/MemberServiceImpl.class]
15:47:21.035 [Test worker] DEBUG org.springframework.context.annotation.ClassPathBeanDefinitionScanner - Identified candidate component class: file [/Users/zidol/Desktop/back-end/SpringCore/build/classes/java/main/hello/core/member/MemoryMemberRepository.class]
15:47:21.035 [Test worker] DEBUG org.springframework.context.annotation.ClassPathBeanDefinitionScanner - Identified candidate component class: file [/Users/zidol/Desktop/back-end/SpringCore/build/classes/java/main/hello/core/order/OrderServiceImpl.class]
15:47:21.037 [Test worker] DEBUG org.springframework.context.annotation.ClassPathBeanDefinitionScanner - Identified candidate component class: file [/Users/zidol/Desktop/back-end/SpringCore/build/classes/java/main/hello/core/web/LogDemoController.class]
15:47:21.037 [Test worker] DEBUG org.springframework.context.annotation.ClassPathBeanDefinitionScanner - Identified candidate component class: file [/Users/zidol/Desktop/back-end/SpringCore/build/classes/java/main/hello/core/web/LogDemoService.class]
```

### 1. **@ComponentScan**

![Untitled](https://zidols.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F7e89d683-cd11-4031-b5ad-34628633d126%2FUntitled.png?table=block&id=79d35b4a-809a-499e-84c0-7663f2669149&spaceId=370dbc5e-872d-4d9e-9f3a-f7113cda9427&width=2000&userId=&cache=v2)

- @ComponentScan 은 @Component 가 붙은 모든 클래스를 스프링 빈으로 등록한다.
- 이때 스프링 빈의 기본 이름은 클래스명을 사용하되 맨 앞글자만 소문자를 사용한다.
    - **빈 이름 기본 전략:** MemberServiceImpl 클래스 memberServiceImpl
    - **빈 이름 직접 지정:** 만약 스프링 빈의 이름을 직접 지정하고 싶으면@Component("memberService2") 이런식으로 이름을 부여하면 된다.

### **2. @Autowired 의존관계 자동 주입**

![Untitled](https://zidols.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F85bd4a81-d48b-4150-9fb0-c14a1ed4d39c%2FUntitled.png?table=block&id=52c3e05f-2cb8-4a30-8241-eeab2a4f156e&spaceId=370dbc5e-872d-4d9e-9f3a-f7113cda9427&width=2000&userId=&cache=v2)

- 생성자에 @Autowired 를 지정하면, 스프링 컨테이너가 자동으로 해당 스프링 빈을 찾아서 주입한다.
- 이때 기본 조회 전략은 타입이 같은 빈을 찾아서 주입한다.
    - getBean(MemberRepository.class) 와 동일하다고 이해하면 된다.
- 생성자에 파라미터가 많아도 다 찾아서 자동으로 주입한다.

---

# **탐색 위치와 기본 스캔 대상**

## **탐색할 패키지의 시작 위치 지정**

모든 자바 클래스를 다 컴포넌트 스캔하면 시간이 오래 걸린다. 그래서 꼭 필요한 위치부터 탐색하도록 시작
위치를 지정 하도록 한다.

```java
@ComponentScan(
          basePackages = "hello.core",
}
```

- basePackages : 탐색할 패키지의 시작 위치를 지정한다. 이 패키지를 포함해서 하위 패키지를 모두
탐색한다.
    - basePackages = {"hello.core", "hello.service"} 이렇게 여러 시작 위치를 지정할 수도
    있다.
- basePackageClasses : 지정한 클래스의 패키지를 탐색 시작 위치로 지정한다.
- 만약 지정하지 않으면 @ComponentScan 이 붙은 설정 정보 클래스의 패키지가 시작 위치가 된다.

## **권장하는 방법**

- 설정 정보 클래스의 위치를 프로젝트 최상단에 두는 것이다.
- 최근 스프링 부트도 이 방법을 기본으로 제공한다.

예를 들어서 프로젝트가 다음과 같이 구조가 되어 있으면

- com.hello
- com.hello.serivce
- com.hello.repository

com.hello 프로젝트 시작 루트, 여기에 AppConfig 같은 메인 설정 정보를 두고, @ComponentScan 애노테이션을 붙이고, basePackages 지정은 생략한다.

## SpringBoot가 좋은 이유!

스프링 부트 프로젝트를 만들면 @SpringBootApplication가 있는 클래스가 루트 경로에 생성이 된다. @SpringBootApplication을 열어보면 설정안에 바로 @ComponentScan 이 선언되어 있다.

## **컴포넌트 스캔 기본 대상**

컴포넌트 스캔은 @Component 뿐만 아니라 다음과 내용도 추가로 대상에 포함한다.

- @Component : 컴포넌트 스캔에서 사용
- @Controlller : 스프링 MVC 컨트롤러에서 사용
- @Service : 스프링 비즈니스 로직에서 사용
- @Repository : 스프링 데이터 접근 계층에서 사용
- @Configuration : 스프링 설정 정보에서 사용

해당 클래스의 코드를 보면 아래와 같이 선언되어 있다.

```java
@Component
public @interface Controller {
}

@Component
public @interface Service {
}

@Component
public @interface Configuration {
}
```

컴포넌트 스캔의 용도 뿐만 아니라 다음 애노테이션이 있으면 스프링은 부가 기능을 수행한다.

- @Controller : 스프링 MVC 컨트롤러로 인식
- @Repository : 스프링 데이터 접근 계층으로 인식하고, 데이터 계층의 예외를 스프링 예외로 변환해준다.
- @Configuration : 앞서 보았듯이 스프링 설정 정보로 인식하고, 스프링 빈이 싱글톤을 유지하도록 추가 처리를 한다.
- @Service :  @Service 는 특별한 처리를 하지 않는다. 대신 개발자들이 핵심 비즈니스 로직이 여기에 있다고 인식하는데 도움이 된다.

> 참고: useDefaultFilters 옵션은 기본으로 켜져있는데, 이 옵션을 끄면 기본 스캔 대상들이 제외된다.
> 

## **필터**

- **includeFilters : 컴포넌트 스캔 대상을 추가로 지정한다.**
- **excludeFilters : 컴포넌트 스캔에서 제외할 대상을 지정한다.**

### **컴포넌트 스캔 대상에 추가할 애노테이션**

```java
package hello.core.scan.filter;

import java.lang.annotation.*;

@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface MyIncludeComponent {

}
```

### **컴포넌트 스캔 대상에서 제외할 애노테이션**

```java
package hello.core.scan.filter;

import java.lang.annotation.*;

@Target(ElementType.TYPE)
@Retention(RetentionPolicy.RUNTIME)
@Documented
public @interface MyExcludeComponent {

}
```

### 컴포넌트 스캔 대상 포함, 제외 할 클래스선언

```java
package hello.core.scan.filter;

@MyIncludeComponent
public class BeanA {
}

@MyExcludeComponent
public class BeanB {
}
```

### 설정 정보와 테스트 코드

```java
public class ComponentFilterAppConfigTest {

    @Test
    void filterScan() {
        AnnotationConfigApplicationContext ac = new AnnotationConfigApplicationContext(ComponentFilterAppConfig.class);

        BeanA beanA = ac.getBean("beanA", BeanA.class);
        assertThat(beanA).isNotNull();

        assertThrows(
                NoSuchBeanDefinitionException.class,
                () -> ac.getBean("beanB", BeanB.class));

    }

    @Configuration
    @ComponentScan(
            includeFilters = @Filter(type = FilterType.ANNOTATION, classes = MyIncludeComponent.class),
            excludeFilters = @Filter(type = FilterType.ANNOTATION, classes = MyExcludeComponent.class)
    )
    static class ComponentFilterAppConfig {

    }
}
```

- includeFilters 에 MyIncludeComponent 애노테이션을 추가해서 BeanA가 스프링 빈에 등록된다.
- excludeFilters 에 MyExcludeComponent 애노테이션을 추가해서 BeanB는 스프링 빈에 등록되지 않는다.

### **FilterType 옵션**

FilterType은 5가지 옵션이 있다.

- ANNOTATION: 기본값, 애노테이션을 인식해서 동작한다.
ex) org.example.SomeAnnotation
- ASSIGNABLE_TYPE: 지정한 타입과 자식 타입을 인식해서 동작한다.
ex) org.example.SomeClass
- ASPECTJ: AspectJ 패턴 사용
ex) org.example..*Service+
- REGEX: 정규 표현식
ex) org\.example\.Default.*
- CUSTOM: TypeFilter 이라는 인터페이스를 구현해서 처리
ex) org.example.MyTypeFilter

---

# **중복 등록과 충돌**

컴포넌트 스캔에서 같은 빈 이름을 등록 시 다음 두가지 상황이 있다.

1. 자동빈등록 vs 자동빈등록
2. 수동빈등록 vs 자동빈등록

## **자동 빈 등록 vs 자동 빈 등록**

- 컴포넌트 스캔에 의해 자동으로 스프링 빈이 등록되는데, 그 이름이 같은 경우 스프링은 오류를 발생시킨다.
    - ConflictingBeanDefinitionException 예외 발생

## **수동 빈 등록 vs 자동 빈 등록**

```java
@Configuration
@ComponentScan(
//        basePackages = "hello.core.member",// 탐색할 패키지의 시작 위치 지정 , default = @ComponentScan이 붙은 클래스의 패키지 부터
        excludeFilters = @ComponentScan.Filter(type = FilterType.ANNOTATION, classes = Configuration.class)//AppConfig.java 및 test 하면서 생성한  것들 제외 시키기 위해.
)
public class AutoAppConfig {

    //spring bootg는 application.properties에 선언 해줘야 수동이 우선권 가짐
    // spring.main.allow-bean-definition-overriding=true //default : false
    @Bean(name = "memoryMemberRepository")//수동이 우선권을 가짐
    MemberRepository memberRepository() {
        return new MemoryMemberRepository();
    }
}
```

이 경우 수동 빈 등록이 우선권을 가진다.
(수동 빈이 자동 빈을 오버라이딩 해버린다.)

**수동 빈 등록시 남는 로그**

```bash
21:10:49.859 [Test worker] DEBUG org.springframework.beans.factory.support.DefaultListableBeanFactory - 
Overriding bean definition for bean 'memoryMemberRepository' with a different definition: replacing ...
```

개발자가 의도한 경우라면 다행이지만 그렇지 않는경우에는 정말 잡아내기 어려운 버그가 만들어진다.

그래서 스프링 부트에서는 수동 빈 등록과 자동 빈 등록이 충돌나면 오류가 발생하도록 기본 값을 바꾸었다.

## **수동 빈 등록, 자동 빈 등록 오류시 스프링 부트 에러**

```bash
***************************
APPLICATION FAILED TO START
***************************

Description:

The bean 'memoryMemberRepository', defined in class path resource [hello/core/AutoAppConfig.class], could not be registered. A bean with that name has already been defined in file [/Users/zidol/Desktop/back-end/SpringCore/build/classes/java/main/hello/core/member/MemoryMemberRepository.class] and overriding is disabled.

Action:

Consider renaming one of the beans or enabling overriding by setting spring.main.allow-bean-definition-overriding=true
```

다음과 같이 스프링 부트를 실행 하면 위와 같이 오류가 발생하고 [application.properties](http://application.properties) 에 spring.main.allow-bean-definition-overriding=true 로 세팅하라고 메시지를 준다. 이와 같이 설정하면 마찬가지로 **수동 빈 등록이 우선권**을 갖는다.

출처  
인프런 김영한님 **[자바 ORM 표준 JPA 프로그래밍 - 기본편](https://www.inflearn.com/course/ORM-JPA-Basic)**  