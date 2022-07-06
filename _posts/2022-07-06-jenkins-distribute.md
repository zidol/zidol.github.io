---
title:  "젠킨스 배포"
excerpt: "CI 툴 젠킨스.."

categories:
  - JENKINS
tags:
  - [JENKINS, CD]

toc: true
toc_sticky: true
 
date: 2022-07-06
last_modified_at: 2022-07-06
---
# 배포전 사전 작업

## 플러그인 설치

![Untitled](https://zidols.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F52059786-eadf-4783-9dd1-12a0f4d4dab6%2FUntitled.png?table=block&id=1a901f4a-121a-4676-a71c-6fdb368be1e5&spaceId=370dbc5e-872d-4d9e-9f3a-f7113cda9427&width=2000&userId=&cache=v2)

![Untitled](https://zidols.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F1eeb8401-f634-4d22-8417-97955e910f80%2FUntitled.png?table=block&id=b868e878-bf04-4f77-87ff-91da866187eb&spaceId=370dbc5e-872d-4d9e-9f3a-f7113cda9427&width=2000&userId=&cache=v2)

먼저 배포를 하기 위해서는 플러그인을 설치해 주어야 한다. 젠킨스 메인 화면에서 젠킨스 관리 탭을 클릭해서 설치 가능을 클릭해 보시면 **Publish Over SSH** 라는 항목이 있다.해당 항목을 클릭해서 설치를 진행해봅니다.
실제로 서버에 접속할 수 있는 권한을 젠킨스가 대신 대행해서 수행해 준다.

![Untitled](https://zidols.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F605990a4-0dc2-4688-8a70-46f1af278c7e%2FUntitled.png?table=block&id=f8afd1b6-4df5-47a6-92c3-27654ea5aa7b&spaceId=370dbc5e-872d-4d9e-9f3a-f7113cda9427&width=2000&userId=&cache=v2)

본인 먼저 설치를 하여서 설치된 목록에 있지만 설치할 플러그인을 선택 후 Download now and install after restart 버튼을 누른다. 위 화면 처럼 설치화면이 나오고 완료되면 자동으로 젠킨스가 재시작 된다.

## 시스템 설정

![Untitled](https://zidols.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fe78678e2-9623-45c9-a76a-5d5f2c3f54c4%2FUntitled.png?table=block&id=d3b3855b-37aa-4969-a89a-44db816009fd&spaceId=370dbc5e-872d-4d9e-9f3a-f7113cda9427&width=2000&userId=&cache=v2)

SSH 정보를 세팅하기 전에 시스템 설정으로 들어간다.

![Untitled](https://zidols.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F63aada34-5837-42a2-a2dd-4c009de1ae14%2FUntitled.png?table=block&id=7d98eff8-e325-41f7-b594-9e29dbb11cf0&spaceId=370dbc5e-872d-4d9e-9f3a-f7113cda9427&width=2000&userId=&cache=v2)

시스템 설정에서 하단 부분으로 내려가면 publish over ssh 에 정보들을 세팅해주는 부분이 나온다. 이 정보를 세팅하기 위해서 터미널에서 ssh 를 생성해주어야 한다.

```bash
ssh-keygen –t rsa 
```

![Untitled](https://zidols.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F51c45adc-e226-4043-a3c7-24490ddc51d4%2FUntitled.png?table=block&id=fe8c6af6-80c4-454d-a097-8d6254a9940f&spaceId=370dbc5e-872d-4d9e-9f3a-f7113cda9427&width=2000&userId=&cache=v2)

터미널에 접속해서 ssh-keygen –t rsa 를 입력하고 엔터를 두번 치시면 키가 생성 되었다고 표기가 나온다.

```bash
chmod 700 /Users/zidol/.ssh                                      

chmod 600 /Users/zidol/.ssh/id_rsa                

chmod 644 /Users/zidol/.ssh/id_rsa.pub  
```

![Untitled](https://zidols.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F320b7b3d-2e63-4561-854f-e3c221686dc2%2FUntitled.png?table=block&id=709dc9e7-b4ff-40c4-844a-3790b51264dd&spaceId=370dbc5e-872d-4d9e-9f3a-f7113cda9427&width=2000&userId=&cache=v2)

![Untitled](https://zidols.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Ffe2946c2-8bd6-4ca0-8162-765b2e6a5af9%2FUntitled.png?table=block&id=6201942f-7e4b-4b86-b9ae-5fae274e7ad6&spaceId=370dbc5e-872d-4d9e-9f3a-f7113cda9427&width=2000&userId=&cache=v2)

cat 로 위 생성된 pub 파일의 내용을 읽어서 복사해 둡니다. 그리고 .ssh 폴더와 그 하위 폴더의 권한 정보를 세팅해준다.

![Untitled](https://zidols.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F3e8e1fca-552d-466a-a9c6-d09a460bcd3b%2FUntitled.png?table=block&id=554d556f-48c5-4d90-a586-dd67908f4eb9&spaceId=370dbc5e-872d-4d9e-9f3a-f7113cda9427&width=2000&userId=&cache=v2)

복사했던 키 정보를 이 Passphrase 에 입력 한다.  그리고 키의 위치 정보를 입력하며  SSH Servers 의 정보는  AWS 에 TOMCAT 정보를 세팅해주었던 그 해당 인스턴스의 경로를 입력해주면 된다.

![Untitled](https://zidols.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F5cb7a004-0bd4-4640-98e8-6ea684727fea%2FUntitled.png?table=block&id=e1547990-c8fd-470a-bfaa-4918c4cb8e2e&spaceId=370dbc5e-872d-4d9e-9f3a-f7113cda9427&width=2000&userId=&cache=v2)

이름은 tomcat 그리고 hostname 은 aws 에 생성했던 ip 정보를 기입해 줍니다. 그리고 username 은 ec2-user 계정으로 세팅이 되어있다.

 ec2-user 계정으로 해두고 실제로 tomcat 의 경로를 적어 준다. 그리고 AWS 에 접속했던 키 정보를 Use password authentication 을 클릭해서 Path to key 에 해당 키의 경로를 적어 준다. 하단에 test configuration 을 클릭해서 성공적으로 접속되는지 확인을 하면 끝나며 저장 버튼을 눌러줍니다.

## 프로젝트 구성 수정

![Untitled](https://zidols.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Feef5e33b-4b7e-4140-8c02-968c90d69db6%2FUntitled.png?table=block&id=c29d9706-ecc0-48f0-a467-6bab69389e05&spaceId=370dbc5e-872d-4d9e-9f3a-f7113cda9427&width=2000&userId=&cache=v2)

다시 구성 클릭

![Untitled](https://zidols.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F55d190ea-94d2-4c43-88ee-1b86eb52b994%2FUntitled.png?table=block&id=b5f97caa-5b3a-41e5-a9d4-4ce93e1ee322&spaceId=370dbc5e-872d-4d9e-9f3a-f7113cda9427&width=2000&userId=&cache=v2)

프로젝트의 설정으로 다시 화면을 전환한다. 프로젝트 설정의 제일 마지막 부분에 빌드 후 조치 추가를 클릭해보면이전에 없던 기능 Send build artifacts over ssh 라는 항목이 생겨 있다.

![Untitled](https://zidols.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fea9ead11-b4ed-4caa-b6d1-39cc44b2bc56%2FUntitled.png?table=block&id=ce00be6c-4f9b-4ea7-b969-0be299b80193&spaceId=370dbc5e-872d-4d9e-9f3a-f7113cda9427&width=2000&userId=&cache=v2)

- 시스템 설정에서 생성하고 세팅했던 tomcat 정보가 자동으로 불러와 진다. 여기서 어떤 파일을 옮길 것인지에 대한 세팅을 하면 끝난다.
- 소스 파일은 jar 파일 그리고 remove prefix 는 실제로 파일이 올라가면 해당 파일의 경로 위치로 올라가기 때문에 그 해당 앞 파일의 경로를 빼주기 위해서 작성된 경로 주소다.
- 본인은 인강과 다르게  /opt/tomcat/webapps 밑에 실제로 ROOT.jar 파일이 올라가게 할 것이다. 실제로 저 remove prefix 를 쓰지 않는 경우 opt 밑에 톰켓 및에 webapps 밑에 리뷰 밑에 타겟에 파일이 들어가게된다.
    - 본인은 jar로 빌드 하였기 때문에 내장된 톰캣을 사용한다.
- SSH server 세팅에 opt 와 tomcat 정보를 넣었기 때문에 리모드 디렉토리에는 webapps만 넣으면 파일이 올라가게 된다. 밑에 마지막 실행 명령어는 톰 켓 서버를 재시작 하라는 명령어다.

![Untitled](https://zidols.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F247dad6c-36e2-403d-9de6-ada253f5e4a5%2FUntitled.png?table=block&id=20e8c4bc-e552-486d-814d-7669615bb278&spaceId=370dbc5e-872d-4d9e-9f3a-f7113cda9427&width=2000&userId=&cache=v2)

기존에 소스의 정보를 토대로 빌드를 해서 jar 파일이 생성되면 프로젝트 명에 뒤에 – 버젼 정보 형태로
파일이 생기게 되는데도 이것을 실제로 이름을 변경해서 올리려면 매우 번거롭기 때문에 jar 파일을 생성시에 한번에 ROOT.jar파일로 바꾸어 주는 부분을 소스로 추가 하였다. Pom.xml 파일에 build 쪽에 finalName 이라는 소스만 추가해 주면 된다.

# 배포

![Untitled](https://zidols.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F88489e94-0e46-40b9-ab86-acf2078e9e3f%2FUntitled.png?table=block&id=83687755-b968-4c2f-92cb-2066689cfe04&spaceId=370dbc5e-872d-4d9e-9f3a-f7113cda9427&width=1460&userId=&cache=v2)

처음에 위와 같은 jar 파일이 생성되던 것이 이제 젠킨스에서 바로 빌드할때 ROOT.jar 파일로 생성되는 것을 볼 수 있다.

![Untitled](https://zidols.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F595cdcb7-4915-43a9-b088-493442bafb9f%2FUntitled.png?table=block&id=cc4259f5-5fcc-44c9-a081-b6e09d676c73&spaceId=370dbc5e-872d-4d9e-9f3a-f7113cda9427&width=2000&userId=&cache=v2)

참조    
fastcampus - AWS & Docker 클라우드 서버구축 배진호강사님 강의