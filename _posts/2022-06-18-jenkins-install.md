---
title:  "젠킨스 설치"
excerpt: "CI 툴 젠킨스.."

categories:
  - JENKINS
tags:
  - [JENKINS, CI]

toc: true
toc_sticky: true
 
date: 2022-06-20
last_modified_at: 2022-06-20
---
# Jenkins 설치

# 젠킨스란?

- 지속적으로 통합 서비스를 제공하는 툴
- Continuous Integration

젠킨스가 하는 역할은 깃헙혹은 빗버킷에 올라가있는 소스를 빌드해주고, 해당 소스를 컴파일 해주어서, 오류를 감지하고, 또 테스트 자동화와 맞물려 사용이 가능하며, 개발 서버나 운영 서버에 배포까지 해주는 절차가 가능하다.

## 젠킨스의 장점

1. 표준 컴파일 환경 - 빌드 테스트
    - 자바 뿐만 아니라 다양한 환경에서의 빌드 테스트 환경을 맞출수 있다.
    - 다영한 언어를 지원하고, 설치해서 해당 컴파일 환경들을 만들수가 있다.
2. 정적 코드 분석을 통해 코딩 규약 준수 여부 체크
3. 다양한 테스트 환경에 대하여 배포작업을 연결 할수 있다.
4. 빌드 타임을 비롯해서, 실행 시간의 변화등 성능 변화 또한 감지 할수 있다.

## 젠킨스 설치 방법

1. 서버에 직접 설치(Docker 활용)
2. 로컬 호스트에 설치

지라, 지라 컨플루언스, 빗버킷과 마찬가지로, 설치 방법은 다양하며 AWS나 자체 운영 서버에 설치 가능하다.

### 젠킨스 로컬 호스트 설치

[https://www.jenkins.io/](https://www.jenkins.io/) 접속하여 Download 클릭한다.

![Untitled](https://zidols.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F32e7f73a-bcf8-4b36-aa27-bb0006e24027%2FUntitled.png?table=block&id=72d771d5-cf50-40e2-b32f-f71f27c1c893&spaceId=370dbc5e-872d-4d9e-9f3a-f7113cda9427&width=2000&userId=&cache=v2)

![Untitled](https://zidols.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F9034807e-08bb-4f69-889b-f4ef7efd1129%2FUntitled.png?table=block&id=a358de8b-d493-4a57-aa91-798d66f53f1b&spaceId=370dbc5e-872d-4d9e-9f3a-f7113cda9427&width=2000&userId=&cache=v2)

가장 안정적인 Stable(LTS) 버전을 받으며 각 사용하는 OS에 맞게 설치한다. 본인은  MacOS 버전을 기준으로 한다.

![Untitled](https://zidols.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F345e73fc-a5ae-44e0-b902-122a13f9ce3f%2FUntitled.png?table=block&id=2020a0e6-8661-47ab-a431-2eff5bc979c3&spaceId=370dbc5e-872d-4d9e-9f3a-f7113cda9427&width=2000&userId=&cache=v2)

MacOS인 경우 brew install을 통해서 터미널을 통해서 설치 한다.

```bash
brew install jenkins-lts
```

![Untitled](https://zidols.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fe9bf2e4d-9671-48c5-9776-7d67e00d5bfe%2FUntitled.png?table=block&id=d7df7cdb-3527-480b-97f5-df52e346a8b2&spaceId=370dbc5e-872d-4d9e-9f3a-f7113cda9427&width=2000&userId=&cache=v2)

이미 설치되어 위와 같은 결과가 나온다.

Mac 에서는 위의 화면처럼 젠킨스를 설치할 수 있는 방법이 몇가지 있다. 

1. Brew 인스톨을 통해서 설치하는 방법.
2. 맥의 로컬에서는 Jenkins 를 brew를 통해 설치하고 실행할 경우 빌드 시 권한 문제가 발생된다. 따라서 패키지를 다운 받아서 설치 해서, 시행착오를 줄일 수 있다.

### 젠킨스 설치

![Untitled](https://zidols.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Ff79288ad-d54b-4d35-ad92-8e8d35c467d1%2FUntitled.png?table=block&id=7a5ec219-a2ec-43c8-aa1a-03273661c18e&spaceId=370dbc5e-872d-4d9e-9f3a-f7113cda9427&width=2000&userId=&cache=v2)

설치가 완료 되면 이렇게 젠킨스의 키를 입력하라는 화면이 나온다.

 빨간색 으로 표기된 부분이 이런 키가 들어있는 폴더와 파일 위치다.

```bash
vi /Users/zidol/.jenkins/secrets/initalAdminpassword
```

위 명령어를 통해 password를 붙여넣어준다.

![Untitled](https://zidols.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F52a88254-2f69-488b-bb81-6c85186c024e%2FUntitled.png?table=block&id=fd65219e-adab-4f27-afac-507ddee13b06&spaceId=370dbc5e-872d-4d9e-9f3a-f7113cda9427&width=2000&userId=&cache=v2)

![Untitled](https://zidols.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F7f30236c-e30c-46dd-9c7a-e32d30baf284%2FUntitled.png?table=block&id=c2800d27-0f44-4ffc-b961-055ba7fed5cb&spaceId=370dbc5e-872d-4d9e-9f3a-f7113cda9427&width=2000&userId=&cache=v2)

![Untitled](https://zidols.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F18aa5ab3-b9ba-42be-94ba-78f5b268edea%2FUntitled.png?table=block&id=4527ba08-2b11-422c-8ca6-ea6cbe70e555&spaceId=370dbc5e-872d-4d9e-9f3a-f7113cda9427&width=2000&userId=&cache=v2)

위 세 단계를 거치면 아래와 같은 대시보드가 나온다.(한개 프로젝트 미리 만들어진 상황)

![Untitled](https://zidols.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fca2d5529-ee0e-40d5-a3e1-c078d705be6c%2FUntitled.png?table=block&id=0a5b3e1a-9f0f-44da-9f7d-70282dd1cdbe&spaceId=370dbc5e-872d-4d9e-9f3a-f7113cda9427&width=2000&userId=&cache=v2)

젠킨스 설정은 다음 포스팅에…

참조  
fastcampus - AWS & Docker 클라우드 서버구축 배진호강사님 강의