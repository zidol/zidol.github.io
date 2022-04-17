---
title:  "CentOS - nohup"
excerpt: "Centos 환경에서 세션이 끊겨도 서비스 유지하기.."

categories:
  - Blog
tags:
  - [CentOS7, nohup]

toc: true
toc_sticky: true
 
date: 2022-04-19
last_modified_at: 2022-04-19
---

# 1. nohup 이란?

- no-hang-up signaling..즉 신호가 끊기지 않는다 는 뜻.
- 리눅스에서 프로세스를 실행한 터미널의 세션 연결이 끊어지더라도 지속적으로 동작 할 수 있게 해주는 명령어

# 2. &란?

프로세스를 실행할 때 백그라운드에서 동작하도록 만드는 명령어

# 3. 주의사항

nohup으로 실행할 쉘스크립트파일(*.sh)은 현재 퍼미션이 755이상 상태여야 함

# 4. 실행방법

1) 톰캣이나 geoserver에 있는 스크립트 실행시

```bash
[root@localhost ~]# nohup ./startup.sh & 
```

2) Spring boot jar파일 실행시

```bash
[root@localhost ~]# nohup java -jar nakdongRiver-0.0.1-SNAPSHOT.jar &
```

실행 파일 같은 경로에 nohup.out 이란 파일이 생기며 로그를 볼수 있다.

터미널 상에서 로그를 보려면 다음과 같이하면 된다.

```bash
[root@localhost ~]# tail -f nohup.out
```

nohup.out 파일을 생성하고 싶지 않을땐 다음과 같이 하면된다.

```bash
[root@localhost ~]# nohup java -jar nakdongRiver-0.0.1-SNAPSHOT.jar > /dev/null &
```

실행한 서비스는 PID 를 찾아서 kill 명령어를 사용하여 종료 시킨다.

```bash
[root@localhost ~]# ps -ef | grep java

tomcat    6731     1  0 Nov23 ?        00:08:29 /usr/lib/jvm/java-1.8.0-openjdk-1.8.0.302.b08-0.el7_9.x86_64/bin/java -Xbootclasspath/a:/home/tomcat/geoserver/geoserver-2.19.2-bin/webapps/geoser                                     
ver/WEB-INF/lib/marlin-0.9.3.jar -Dsun.java2d.renderer=org.marlin.pisces.MarlinRenderingEngine -DGEOSERVER_DATA_DIR=/home/tomcat/geoserver/geoserver-2.19.2-bin/bin/../data_dir -Djava.awt.headles                                    
 s=true -DSTOP.PORT=8079 -DSTOP.KEY=geoserver -jar start.jar

[root@localhost ~]# kill -9 6731
```

### 참고

[https://gracefulprograming.tistory.com/128](https://gracefulprograming.tistory.com/128) [Peter의 우아한 프로그래밍]

[https://marlboroyw.tistory.com/373](https://marlboroyw.tistory.com/373) [ywlee861009]