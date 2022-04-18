---
title:  "CentOS 7 자바 설정"
excerpt: "Centos 환경에서 자바 설치 후 설정 방법"

categories:
  - Linux
tags:
  - [CentOS7, Java, setting]

toc: true
toc_sticky: true
 
date: 2021-11-21T08:06:00-05:00
last_modified_at: 2021-11-21T08:06:00-05:00
---

## yum 설치 가능 리스트 확인

```bash
[root@localhost ~]# yum list java*jdk-devel
```

## 자바 설치

```bash
[root@localhost ~]# yum install java-1.8.0-openjdk-devel.x86_64
```

## CentOs7 jdk 설치 결과 확인

```bash
[root@localhost ~]# javac -version
javac 1.8.0_161
[root@localhost ~]# rpm -qa java*jdk-devel
java-1.8.0-openjdk-devel-1.8.0.161-0.b14.el7_4.x86_64
[root@localhost ~]#
```

## CentOs7 jdk 환경변수 설정

1) 현재 JAVA_HOME이 설정 되어 있는지 확인

```bash
[root@localhost ~]# echo $JAVA_HOME
[root@localhost ~]#
```

JAVA_HOME이 설정이 안되어 있는 것을 확인 됨

2) javac 위치 확인

```bash
[root@localhost ~]# which javac
/usr/bin/javac
[root@localhost ~]# readlink -f /usr/bin/javac
/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.161-0.b14.el7_4.x86_64/bin/javac
[root@localhost ~]#
```

- which javac라는 명령어는 javac라는 명령어의 위치를 알려달라는 말
- /usr/bin/javac 는 심볼릭 링크 이므로 원본 파일의 위치를 찾기 위해 readlink -f /usr/bin/javac 명령어를 사용함
- readlink -f는 심볼릭 링크에서 원본파일을 추출하는 명령어
- /usr/lib/jvm/java-1.8.0-openjdk-1.8.0.161-0.b14.el7_4.x86_64/bin/javac 가 쉘에서 동작하고 있는 javac명령어의 원본파일
- /usr/lib/jvm/java-1.8.0-openjdk-1.8.0.161-0.b14.el7_4.x86_64 가 JAVA_HOME이 될 경로가 됨

## $JAVA_HOME 설정

1) 실제 javac명령어의 경로를 찾았으니 그 경로를 이용하여 JAVA_HOME 환경변수로 등록하도록 하자.
환경변수를 설정할수 있는 profile 이라는 파일을 vi 편집기로 열자

```bash
[root@localhost ~]# vi /etc/profile
```

해당 파일의 하단에 아래 내용을 추가한뒤 저장하자.

```bash
export JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.161-0.b14.el7_4.x86_64
```

파일을 저장한뒤 아래 명령어를 이용하여 수정한 파일을 적용하자.
ssh를 재접속 해도 되지만 아래 방법이 더 편하다.

```bash
[root@localhost ~]# source /etc/profile
```

## JAVA_HOME 재대로 적용되었는지 확인

```bash
[root@localhost ~]# source /etc/profile
[root@localhost ~]# echo $JAVA_HOME
/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.161-0.b14.el7_4.x86_64
[root@localhost ~]# $JAVA_HOME/bin/javac -version
javac 1.8.0_161
```