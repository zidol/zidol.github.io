---
title:  "Blocking I/O VS Non-Blocking I/O"
excerpt: "kevin"

categories:
  - Spring reactive
tags:
  - [reactive system, reactive promgramming, Blocking I/O, Non-Blocking I/O]

toc: true
toc_sticky: true
 
date: 2024-11-30
last_modified_at: 2024-11-30
---

## Blocking I/O 방식

- **작업 쓰레드가 종료될때까지 요청을 한 쓰레드는 차단된다**
- **이러한 단점을 보완하기 위해 멀티 쓰레딩 기법을 통해 추가 쓰레드를 할당 할 수 있다.**
- **CPU 대비 많은 수의 쓰레드를 사용하는 애플리케이션은 비효율적이다.**
    - **컨텍스트 스위칭(Context Switching)으로 인한 쓰레드 전환 비용 발생**
    - **메모리 사용에 있어서 오버 헤드 발생**
    - **쓰레드 풀에서의 응답 지연 문제 발생**

![Blocking I/O 예시]({{ site.url }}{{ site.baseurl }}/assets/images/blocking_ex.png)

Blocking I/O 예시

## Blocking I/O 방식의 문제점

### - 컨텍스트 스위칭(Context Switching) 비용 발생

![image.png]({{ site.url }}{{ site.baseurl }}/assets/images/context_switching.png)

- PCB에 저장하고 PCB에서 로드하는데 추가 시간이 들어가기때문에 실행 시점과 유휴 시점이 딱 들어맞지 않는다
- PCB에 저장하고 PCB에서 로드하는 시간동안에 CPU는 대기한다. 일을 하지 못함.
- 컨텍스트 스위칭이 잦을수록 CPU의 전체 대기 시간이 길어지므로 성능이 저하된다.

### **메모리 사용에 있어서 오버헤드 발생**

- **default stack size for the 64-bit JVM = 1,024 KB**
- **64,000 명이 동시 접속 시, 64GB의 메모리가 추가로 필요함**

### **쓰레드 풀에서의 응답 지연 문제 발생**

- 쓰레드 풀에 유휴 쓰레드가 없을 경우의 응답 지연 시간이 길어짐
- **반납된 쓰레드가 사용 가능하도록 전환되는 과정에서 응답 지연 발생**

## Non-Blocking I/O 방식

- **작업 쓰레드의 종료와 상관없이 요청을 한 쓰레드는 차단되지 않는다.**
- **적은 수의 쓰레드를 사용하기 때문에 쓰레드 전환 비용이 적게 발생한다.**
    - **CPU 대기 시간 및 사용량에 있어서도 효율적이다.**
    - **CPU를 많이 사용하는 작업이 포함 되어 있을 경우에는 성능 향상에 악영향을 준다.**
    - **사용자 요청에서 응답 까지의 과정에 Blocking I/O 요소가 포함되어 있을 경우 Non-Blocking의 이점을 발휘하기 힘들다**

![Non-Blicking I/O 예시]({{ site.url }}{{ site.baseurl }}/assets/images/nonblocking_ex.png)

Non-Blicking I/O 예시