---
title:  "CentOS 7 Postgresql 설치"
excerpt: "Postgresql 설치"

categories:
  - Database
tags:
  - [Database, Postgresql]

toc: true
toc_sticky: true
 
date: 2022-05-01
last_modified_at: 2022-05-01
---
## Install the repository RPM

```bash
[root@localhost ~]# yum install -y 
```

[https://download.postgresql.org/pub/repos/yum/reporpms/EL-7-x86_64/pgdg-redhat-repo-latest.noarch.rpm](https://download.postgresql.org/pub/repos/yum/reporpms/EL-7-x86_64/pgdg-redhat-repo-latest.noarch.rpm)

## PostgreSQL을 설치

```bash
[root@localhost ~]# yum install -y postgresql12-server postgresql12-contrib
```

## 설치된 패키지 확인

```bash
[root@localhost ~]# rpm -qa | grep postgresql
```

## 기본 Database 생성

 - initdb 명령어를 통해 기본 데이터베이스를 설치합니다. 기본 데이터베이스는 postgres 라는 이름으로 생성됩니다.

```bash
[root@localhost ~]# /usr/pgsql-12/bin/postgresql-12-setup initdb
```

## 서비스 등록 및 실행

```bash
[root@localhost ~]# systemctl enable postgresql-12
[root@localhost ~]# systemctl start postgresql-12
```

## 외부접속 허용하기

```bash
[root@localhost ~]# cd /var/lib/pgsql/12/data/
[root@localhost ~]# vi pg_hba.conf

맨 하단에

host    all     all     0.0.0.0/0       password 추가
```

맨 하단에

```bash
[root@localhost ~]# vi postgresql.conf
```

? 명령어를 사용해서 listen 검색 후 listen_addresses = '*' 로 수정한다. (주석 제거)

접속 안되면 서버 방화벽, 포트포워딩 확인해서 열어 줘야함

## PostGIS 설치

```bash
[root@localhost ~]# yum install epel-release
[root@localhost ~]# yum install postgis31_12.x86_64    -> 31 => postgis 3.1버전 , 12 가 postgresql 12버전
[root@localhost ~]# systemctl restart postgresql-12.service
```

해당 db 가서 CREATE EXTENSION postgis; 쿼리 실행