---
title:  "라액티브 시스템"
excerpt: "kevin"

categories:
  - Spring reactive
tags:
  - [reactive system, reactive promgramming]

toc: true
toc_sticky: true
 
date: 2024-11-03
last_modified_at: 2024-11-03
---

## 리액티브 시스템(Reactive System)이란?

### 리액티브 시스템의 특징

- 높은 응답성
- 탄력적이고 유연
- 메시지 기반으로 동작

!https://www.reactivemanifesto.org/images/reactive-traits.svg

## 리액티브 프로그래밍(Reactive Programming)이란?

### 리액티브 프로그래밍의 특징

- 데이터 소스에 변경이 있을때마다 데이터를 전파
- 선언형 프로그래밍의 패러다임 : 실행할 동작을 구체적으로 명시하지 않고 목표만 정의함
- 함수형 프로그래밍 기법 사용

### 명령형 프로그래밍의 예시

```java
List<Integer> numbers = Arrays.asList(1, 3, 21, 10, 8, 11);

int sum = 0;
for(int number : numbers) {     
	if(number > 6 && (number % 2 != 0)){         
			sum += number;     
	}
}
```

### 선언형 프로그래밍의 예시

```java
List<Integer> numbers = Arrays.asList(1, 3, 21, 10, 8, 11);
int sum = numbers.stream()        
		.filter(number -> number > 6 && (number % 2 != 0))        
		.mapToInt(number -> number)        
		.sum();

```

### 리액티브 스트림즈(Reative Streams)란?

- 리액티브 프로그래밍을 표준화 한 명세

: [https://github.com/reactive-streams/reactive-streams-jvm/blob/v1.0.3/README.md#specification](https://github.com/reactive-streams/reactive-streams-jvm/blob/v1.0.3/README.md)

- Reactive Streams를 구현한 구현체
    - RxJava
    - Java 9 Flow API
    - Akka Streams
    - Reactor
    - 그 외  RxJs, RxScala, RxAndroid 등의 리액티브 확장