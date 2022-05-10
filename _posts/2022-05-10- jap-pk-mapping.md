---
title:  "JPA - 기본 키 매핑"
excerpt: "기본키 매핑 및 전략"

categories:
  - JPA
tags:
  - [JPA]

toc: true
toc_sticky: true
 
date: 2022-05-10
last_modified_at: 2022-05-10
---
# **기본 키 매핑 어노테이션**

---

- @Id
- @**GeneratedValue**

```java
@Id @GeneratedValue(strategy = GenerationType.AUTO) 
private Long id;
```

# **기본 키 매핑 방법**

---

- 직접 할당: **@Id만 사용**
- 자동 생성(**@GeneratedValue**)
    - **IDENTITY**: 데이터베이스에 위임, MYSQL
    - **SEQUENCE**: 데이터베이스 시퀀스 오브젝트 사용, ORACLE
        - @SequenceGenerator 필요
    - **TABLE**: 키 생성용 테이블 사용, 모든 DB에서 사용
        - @TableGenerator 필요
    - **AUTO**: 방언에 따라 자동 지정, 기본값

# IDENTITY 전략 및 특징

---

- **기본 키 생성을 데이터베이스에 위임**
- **주로 MySQL, PostgreSQL, SQL Server, DB2에서 사용**
    - **(예: MySQL의 AUTO_ INCREMENT)**
- **JPA는 보통 트랜잭션 커밋 시점에 INSERT SQL 실행**
- **AUTO_ INCREMENT는 데이터베이스에 INSERT SQL을 실행한 이후에 ID 값을 알 수 있음**
- **IDENTITY 전략은 em.persist() 시점에 즉시 INSERT SQL 실행하고 DB에서 식별자를 조회**

## IDENTITY 전략 - 매핑

```java
@Entity
public class RMember {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)

		//...
}
```

```java
RMember member = new RMember();
member.setUsername("A");

System.out.println("===================");
em.persist(member);
System.out.println("===================");
```

```bash
===================
Hibernate: 
    /* insert review.entity.RMember
        */ insert 
        into
            RMember
            (city, username, zipcode) 
        values
            (?, ?, ?)
===================
```

# **SEQUENCE 전략  및 특징**

---

- **데이터베이스 시퀀스는 유일한 값을 순서대로 생성하는 특별한 데이터베이스 오브젝트(예: 오라클 SEQUENCE)**
- **오라클, PostgreSQL, DB2, H2 데이터베이스에서 사용**

## **SEQUENCE 전략 - 매핑**

```java
@Entity
@SequenceGenerator(
        name = "MEMBER_SEQ_GENERATOR",//시퀀스 제너레이터 이름
        sequenceName = "MEMBER_SEQ", //매핑할 데이터베이스 시퀀스 이름
        initialValue = 1, allocationSize = 1)//메모리를 통해 할당할 범위 사이즈
public class RMember {
    @Id
    @GeneratedValue(strategy = GenerationType.SEQUENCE
	        ,generator = "MEMBER_SEQ_GENERATOR")//식별자 생성기를 설정해놓은 MEMBER_SEQ_GENERATOR 지정
    private Long id;
		//...
}
```

데이터베이스 시퀀스는 유일한 값을 순서대로 생성하는 특별한 데이터베이스 오브젝트이다. SEQUENCE 전략은 이 시퀀스를 사용하여 기본키를 생성한다.

# **SEQUENCE - @SequenceGenerator**

---

- DB에 생성한 시퀀스를 바탕으로 식별자를 생성하는 시퀀스 생성기를 설정하는 애노테이션
- 클래스 단위 또는 식별자 필드에서 @GeneratedValue 애노테이션 설정과 함께 사용 가능

| 속성 | 설명 | 기본값 |
| --- | --- | --- |
| name | 식별자 생성기 이름 | 필수 |
| sequenceName | 데이터베이스에 등록되어있는 시퀀스 이름 | hibernate_sequence |
| initialValue | DDL 생성 시에만 사용됨, 시퀀스DDL을 생성할 때 처음 1 시작하는 수를 지정한다. | 1 |
| allocationSize | 시퀀스 한 번 호출에 증가하는 수(성능 최적화에 사용됨 데이터베이스 시퀀스 값이 하나씩 증가하도록 설정되어 있으면 이값을 반드시 1로 설정해야한다.) | 50 |
| catalog,schema | 데이터베이스 catalog, schema 이름 |  |

## **allocationSize 속성**

- allocationSize 값은 DB에 매번 시퀀스를 호출하지 않기 위해서 최적화를 위해 존재하는 속성
- 하이버네이트의 경우 기본값은 50인데, 값을 바꾸지 않는다면 최초에 DB에 시퀀스를 호출한 이후 50까지는 메모리에서 현재 시퀀스 값을 저장한 이후 가상으로 증가시키며 관리하고 51이 되는 시점에 DB의 시퀀스를 한번 더 호출한 이후 그 값으로부터 50만큼인 100까지 가상으로 시퀀스 식별자를 관리
- 서버가 중단 되면 +50 후 다시 증가한다.

## **주의사항**

주의할점이 있다면 DB의 시퀀스 증가값이 1인 경우입니다. DB의 시퀀스 증가값이 1인 상태는 반드시 allocationSize 또한 1로 맞춰 주어야 합니다. 

이유는 allocationSize 가 다음과 같은 알고리즘으로 동작하기 때문

1. 최초 persist() 실행시에 설정에 따른 DB 시퀀스를 두 번 호출하여 첫번째 시퀀스값을 가상으로 관리할 시작값, 두번째 시퀀스 값을 가상으로 관리할 범위의 끝(MAX)값으로 지정한다.

2. 이후에는 persist()를 실행해도 db에 시퀀스를 호출하지 않고 메모리에서 가상으로 관리하며 할당한다. persist() 실행시마다 메모리에서 관리하는 가상의 값을 1씩 증가시키며 엔티티에 할당한다.

3. 어느 시점에 다다르면 엔티티에 식별자를 할당할 값이 관리할 범위의 끝(MAX)이 되고, 이후 다시 한번 persist()를 실행하는 시점에 DB에 시퀀스를 호출한다.

4. 다시 호출한 시퀀스값을 가상으로 관리할 끝(MAX)값으로 바꾸고 시작값 또한 변경하는데 끝(MAX)값 - (allocationSize - 1) 공식을 사용하 시작값을 정한다.

```java
RMember member = new RMember();
member.setUsername("A");

RMember member2 = new RMember();
member2.setUsername("B");

RMember member3 = new RMember();
member3.setUsername("C");

System.out.println("===================");

em.persist(member);
em.persist(member2);
em.persist(member3);

System.out.println("member = " + member.getId());
System.out.println("member2 = " + member2.getId());
System.out.println("member2 = " + member3.getId());

System.out.println("===================");
```

```java
===================
Hibernate: 
    call next value for MEMBER_SEQ
Hibernate: 
    call next value for MEMBER_SEQ
member = 1
member2 = 2
member2 = 3
===================
```

서버를 종료 후 다시 실행 하면 현재 값이 +50 증가 후 다시 시작된다.

![H2에서 시퀀스 상태]({{ site.url }}{{ site.baseurl }}/assets/images/seq_status.png)

H2에서 시퀀스 상태

# TABLE 전략

---

- **키 생성 전용 테이블을 하나 만들어서 데이터베이스 시퀀스를 흉내내는 전략**
- **장점: 모든 데이터베이스에 적용 가능**
- **단점: 테이블을 한번 더 조회해야하기 때문에 성능상 좋지 않다.**

## **TABLE 전략 - 매핑**

```java
@Entity
@TableGenerator(
	name = "MEMBER_SEQ_GENERATOR",
	table = "MY_SEQUENCES",
	pkColumnValue = "MEMBER_SEQ", 
	allocationSize = 1)
public class RMember {
    @Id
    @GeneratedValue(strategy = GenerationType.TABLE,
            generator = "MEMBER_SEQ_GENERATOR")
    private Long id;
    //...
}
```

```java
RMember member = new RMember();
member.setUsername("A");
em.persist(member);
```

```bash
create table MY_SEQUENCES (
       sequence_name varchar(255) not null,
        next_val bigint,
        primary key (sequence_name)
    )
Hibernate: 
    
    insert into MY_SEQUENCES(sequence_name, next_val) values ('MEMBER_SEQ',0)
```

![MY_SQUENCES 테이블 조회]({{ site.url }}{{ site.baseurl }}/assets/images/seq_table_strategy.png)

MY_SQUENCES 테이블 조회

위 그림 처럼 MY_SQUENCES 테이블에 사용할 시퀀스를 테이블별로 로우를 등록하여 사용한다.

## **@TableGenerator - 속성**

| 속성 | 설명 | 기본값 |
| --- | --- | --- |
| name | 식별자 생성기 이름 | 필수 |
| table | 키생성 테이블명 | hibernate_sequences |
| pkColumnName | 시퀀스 컬럼명 | sequence_name |
| valueColumnName | 시퀀스 값 컬럼명 | next_val |
| pkColumnValue | 키로 사용할 값 이름 | 엔티티 이름 |
| initialValue | 초기 값, 마지막으로 생성된 값이 기준이다. | 0 |
| allocationSize | 시퀀스 한 번 호출에 증가하는 수(성능 최적화에 사용됨) | 50 |
| catalog, schema | 데이터베이스 catalog, schema 이름 |  |
| uniqueConstraints(DDL) | 유니크 제약 조건을 지정할 수 있다. |  |

# **권장하는 식별자 전략**

---

- **기본 키 제약 조건** :  not null, 유일 값, **변하면 안됨**
- 미래까지 이 조건을 만족하는 자연키를 찾기 어렵기 때문에 대리키(대체키)를 사용해함
    - 주민등록번호는 적절하지 않음
- **권장 : Long형 + 대체키 + 키 생성전략 사용**

출처

인프런 김영한님 **[자바 ORM 표준 JPA 프로그래밍 - 기본편](https://www.inflearn.com/course/ORM-JPA-Basic)**

[https://dololak.tistory.com/479](https://dololak.tistory.com/479)