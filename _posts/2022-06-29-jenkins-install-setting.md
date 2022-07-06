---
title:  "젠킨스 세팅"
excerpt: "CI 툴 젠킨스.."

categories:
  - JENKINS
tags:
  - [JENKINS, CI]

toc: true
toc_sticky: true
 
date: 2022-06-29
last_modified_at: 2022-06-29
---
# Global Tool Configuration

![Untitled](https://zidols.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fb974e259-d33c-40d3-9537-f6a41f4ac3cb%2FUntitled.png?table=block&id=e6bfcf19-3b72-438b-8799-9f4105e959b5&spaceId=370dbc5e-872d-4d9e-9f3a-f7113cda9427&width=2000&userId=&cache=v2)

젠킨스 관리에서는 다양한 메뉴들이 존재한다. 

Global Tool Configuration 메뉴를 클릭한다.

![Untitled](https://zidols.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fb9ea3afe-4251-4b19-8134-d2525d56f242%2FUntitled.png?table=block&id=76c1428d-59a4-4359-87f2-28a4e2b0028c&spaceId=370dbc5e-872d-4d9e-9f3a-f7113cda9427&width=2000&userId=&cache=v2)

위 화면을 보면 메이븐 세팅 부터 JDK, Git 설정 그리고 GIT과 ANT 그리고 MAVEN 세팅 도커까지 다양한 설치 위치들을 세팅해 줄 수 있다. 

기존에 설치 된 것이 있으면 어느 위치에 설치가 되어있는지를 잘 확인해야 하고, 만약 설치가 안되어 있으면 설치해주는 작업을 각각 진행해주어야 한다. Git 설정이 잘 되어야 서버상에서 소스가 잘 내려 받아진다.

 java 와 maven 설치가 잘 되어 있어야 빌드가 잘 완료되고 저장이 완료된다.

## JDK

![Untitled](https://zidols.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fbf516c8b-948f-44d4-a10a-196d92aeddfe%2FUntitled.png?table=block&id=a3f937d1-4111-4097-ad75-6f1eee642d48&spaceId=370dbc5e-872d-4d9e-9f3a-f7113cda9427&width=2000&userId=&cache=v2)

자바의 패스 정보와 mvn의 패스 정보, 그리고 git 정보가 정상적으로 잘 들어가야 빌드가 잘 된다.
Which 명령어를 통해서 설치된 내용과 실행파일의 위치를 파악한다.

![Untitled](https://zidols.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fdaf41c77-9471-4de2-93a0-6557172becc6%2FUntitled.png?table=block&id=800a1103-58ed-43d9-b92d-04ca15871120&spaceId=370dbc5e-872d-4d9e-9f3a-f7113cda9427&width=2000&userId=&cache=v2)

본인은 m1 칩 맥북이라 zulu jdk를 사용해서 JAVA_HOME 경로를 위와 같이 설정 하였다.

### MAVEN

![Untitled](https://zidols.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F6b8b60c6-033a-40c0-83ae-f0a3f4e1966a%2FUntitled.png?table=block&id=6f4ebc81-f66a-4b9f-ae80-f203bd2e8acd&spaceId=370dbc5e-872d-4d9e-9f3a-f7113cda9427&width=2000&userId=&cache=v2)

```bash
brew install maven
```

macOS 기준으로 위와 같이 brew를 사용해서 메이븐을 설치 해준다. 이미 설치 한적이 있기 때문에 위와 같이 업데이트를 하고 이미 설치 되어 있다고 한다.

### GIT

![Untitled](https://zidols.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fad2ec7f2-b49c-41bf-a433-3d9e52dc652f%2FUntitled.png?table=block&id=b2e106e4-bd82-4087-ac6a-80cb145e0e46&spaceId=370dbc5e-872d-4d9e-9f3a-f7113cda9427&width=2000&userId=&cache=v2)

모든 설치가 완료가 되면 각각 path to git executable 정보에 실제 설치된 패스 정보를 입력한다.

Save Apply 버튼을 눌러주면 설정 완료!!

# 젠킨스 프로젝트 세팅

![Untitled](https://zidols.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F5d19696e-5b5c-4835-9049-b068ea87435f%2FUntitled.png?table=block&id=356bbe85-64d6-4210-98cc-c173c7b0521a&spaceId=370dbc5e-872d-4d9e-9f3a-f7113cda9427&width=2000&userId=&cache=v2)

 새로운 프로젝트를 만들어야 한다. 왼쪽 상단의 새로운 Item을 누른다. 젠킨스에서는 이렇게 프로젝트를 아이템이라고 부른다.

![Untitled](https://zidols.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Ff0c469e1-34a2-486d-8a14-d37c27d518d0%2FUntitled.png?table=block&id=06e8f833-c734-48e9-878f-1af56036d2af&spaceId=370dbc5e-872d-4d9e-9f3a-f7113cda9427&width=2000&userId=&cache=v2)

프로젝트 명을 입력 후 FreestyleProject 를 클릭한 뒤에 okay 버튼을 누른다.

![Untitled](https://zidols.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fa2e36984-9048-4f38-ab99-c7cf661d924a%2FUntitled.png?table=block&id=c5b6d926-6be0-4579-aa21-952d591a7713&spaceId=370dbc5e-872d-4d9e-9f3a-f7113cda9427&width=2000&userId=&cache=v2)

프로젝트가 생성이 완료 되면 이렇게 프로젝트와 프로젝트 명칭이 뜨고 마지막으로 빌드한 시간 및 기타 정보들이 수록 되어 있다. 

구성을 클릭하여 프로젝트 구성 세팅을 한다.

### 소스 코드 관리

![Untitled](https://zidols.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F982eee22-6b2c-480f-a3b5-52465c24d1bb%2FUntitled.png?table=block&id=105b4684-a9ee-4cd4-9486-4f6fdadbe1b1&spaceId=370dbc5e-872d-4d9e-9f3a-f7113cda9427&width=2000&userId=&cache=v2)

구성을 클릭한 뒤에 소스 코드 관리에 git을 클릭한다.  깃 정보에 올라간 정보를 가지고 소스를 가져와서 자동으로 빌드를 해주기 위해서 github, bitbucket등의 서비스에 올린 repositroy 정보를 등록한다.

![Untitled](https://zidols.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F3d5a898d-3220-4094-a765-abafd419ed22%2FUntitled.png?table=block&id=c2db079d-75be-413a-8c35-6fa3e154e601&spaceId=370dbc5e-872d-4d9e-9f3a-f7113cda9427&width=2000&userId=&cache=v2)

빗버킷의 reposytory에 가서 우측 clone 버튼을 눌러 reposytory url을 복사해 넣어준다.

![Untitled](https://zidols.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F0a65f97d-fffa-4684-95e1-3062f94d2d52%2FUntitled.png?table=block&id=d8d4d5fd-f6e5-4d9e-86ac-856033aaf2f5&spaceId=370dbc5e-872d-4d9e-9f3a-f7113cda9427&width=2000&userId=&cache=v2)

credential은 빗버킷 기준으로 Personal settings에 들어가서 **app password**를 등록하여 입력 해준다.

패스워드는 따로 저장 해두도록 하자!!

### 빌드 구성

![Untitled](https://zidols.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fc476a04b-fde3-4c7e-9628-10b737ffb9dc%2FUntitled.png?table=block&id=c32cfe74-6d0b-4501-8fa1-f8a00c684098&spaceId=370dbc5e-872d-4d9e-9f3a-f7113cda9427&width=2000&userId=&cache=v2)

하단에 Build 에 Add Build Step 을 눌러서 Invoke top-level maven targets 를 클릭한다.

 메이븐 빌드를 하기 위해서 해당 내용들을 세팅해 준다.

![Untitled](https://zidols.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F213d5b37-181c-4c54-a74e-365250911e10%2FUntitled.png?table=block&id=a3285de9-1b62-4b56-b8cf-c90a347f8aee&spaceId=370dbc5e-872d-4d9e-9f3a-f7113cda9427&width=2000&userId=&cache=v2)

메이븐 버젼은 maven 을 입력하며  Goals 에 package 를 넣어준다. POM은 pom.xml를 읽지 못하면 실제 프로젝트 pom.xml 위치를 넣어주면 된다. Git에 올라간 경로에 따라서 root 경로가 될 수도 있고, 프로젝트 폴더 밑으로 해당 파일이 들어갈 수도 있기때문에 POM 의 위치를 세팅해 주면 된다.

![Untitled](https://zidols.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F336b4b0d-d330-4ecd-ba19-206011e5ce37%2FUntitled.png?table=block&id=58104df8-fca1-4251-bc15-0dd49a199b70&spaceId=370dbc5e-872d-4d9e-9f3a-f7113cda9427&width=2000&userId=&cache=v2)

![Untitled](https://zidols.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fb00ca1c0-63af-43e6-9ceb-72a699d07eb6%2FUntitled.png?table=block&id=25849fca-fbfe-46d9-bdbb-c39229339895&spaceId=370dbc5e-872d-4d9e-9f3a-f7113cda9427&width=2000&userId=&cache=v2)

프로젝트로 돌아와서 세팅이 완료되기 전에 **지금 빌드** 클릭하고, 하단에 빌드 되고 있는 숫자 버튼을 누룬다.

빌드 숫자를 누르면 오른쪽 상단 처럼 빌드중이라는 표기가 나온다. 그리고 Console Output 이라는 것을 클릭해서 현재 빌드가 되는 상황을볼수 있다.  여기 정보를 토대로 Building in workspace 라는 정보를 가지고 권한을 한번 업데이트 해주어야 합니다. 초기에 폴더 정보에 대한 권한이 없을 때 git 으로 받아오는 폴더 권한이 없어서 계속 무한 로딩 걸리는 경우가 있다.

![Untitled](https://zidols.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Ff8781ef0-2284-4061-920f-259e82cb727b%2FUntitled.png?table=block&id=22398bba-e7e4-47dc-99c5-a347db88ef97&spaceId=370dbc5e-872d-4d9e-9f3a-f7113cda9427&width=2000&userId=&cache=v2)

이때 터미널을 열어서 해당 폴더로 접속한다. 그리고 chmod 명령어로 만든 프로젝트 폴더의 권한을 열어준다.

![Untitled](https://zidols.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F1e1613cb-4c9f-4d56-a28d-1e37b515431b%2FUntitled.png?table=block&id=b681cc6a-1aee-4935-a609-c8d2fea34c29&spaceId=370dbc5e-872d-4d9e-9f3a-f7113cda9427&width=2000&userId=&cache=v2)

그리고 젠킨스의 관리페이지로 다시 돌아와서 Reload Configuration from Disk를 클릭해 준다. 권한이 세팅이 되었다고 해도 실제로 인식하는에는 시점 차이가 있기 때문에 이 부분을 꼭 확인 버튼을 눌러서 새롭게 화면을 고쳐 준다.

![Untitled](https://zidols.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F8e409d6f-f986-42d6-934d-f794482b5473%2FUntitled.png?table=block&id=af2dab40-a117-4536-b6fb-9ddf61d9557a&spaceId=370dbc5e-872d-4d9e-9f3a-f7113cda9427&width=2000&userId=&cache=v2)

 다시 젠킨스를 클릭해서 name 을 클릭해서 해당 프로젝트로 들어갑니다. 앞에 날씨 표시는 프로젝트가 정상적으로 빌드되고 있는지 아닌지 여부가 나온다. 우측에 플래이 버튼처럼 생긴 것이 빌드를 실행하는 버튼이다.

![Untitled](https://zidols.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fc1558545-7f59-4a74-b4f8-7279c2b0ecc3%2FUntitled.png?table=block&id=ea995a8e-ed90-42ce-a089-5e47cfbf3cce&spaceId=370dbc5e-872d-4d9e-9f3a-f7113cda9427&width=2000&userId=&cache=v2)

![Untitled](https://zidols.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F639517a2-9074-4a84-8399-0edc3a2a97d6%2FUntitled.png?table=block&id=68d853b8-7ce3-409c-b3ff-cab5c735f170&spaceId=370dbc5e-872d-4d9e-9f3a-f7113cda9427&width=2000&userId=&cache=v2)

권한세팅이 잘 끝나면 git fetch 라인이 정상적으로 넘어간다.

 처음 초기 세팅에는 이 부분이 시간이 오래걸리는데요. 소스 전체를 다운 받기 때문이다.

빌드가 정상적으로 끝이나게 되면 이런 화면이 나온다. 세팅한 WAR 또는 JAR 정보 그리고 빌드된 파일에 대한 정보와 그리고 SUCCESS 가 뜨면 빌드가 완료된다.

배포는 다음 포스팅에..

참조  
fastcampus - AWS & Docker 클라우드 서버구축 배진호강사님 강의
