---
title:  "JPA - 상속 관계 매핑"
excerpt: "상속 관계 매핑..."

categories:
  - JPA
tags:
  - [JPA, 상속, Inheritance]

toc: true
toc_sticky: true
 
date: 2022-05-16
last_modified_at: 2022-05-16
---

# **상속관계 매핑**

- 관계형 데이터베이스는 상속 관계가 아니다.
- 슈퍼타입 서브타입 관계라는 모델링 기법이 객체 상속과 유사하다.
- 상속관계 매핑: 객체의 상속과 구조와 DB의 슈퍼타입 서브타입 관계를 매핑

![Untitled](https://zidols.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F6acb7d99-5daf-47c6-8bb1-58a75be0c1dd%2FUntitled.png?table=block&id=ec57e364-efb3-4a77-aa63-b0574415762c&spaceId=370dbc5e-872d-4d9e-9f3a-f7113cda9427&width=2000&userId=&cache=v2)

슈퍼타입 서브타입 논리 모델을 실제 물리 모델로 구현하는 방법

- 각각테이블로변환 → 조인전략
- 통합 테이블로 변환 →  단일 테이블 전략
- 서브타입 테이블로 변환 → 구현 클래스마다 테이블 전략

# **주요 어노테이션**

## **@Inheritance**

**@Inheritance(strategy=InheritanceType.XXX) 형태로 3가지 유형이 있다.**

- **JOINED**: 조인 전략
- **SINGLE_TABLE**: 단일 테이블 전략
- **TABLE_PER_CLASS**: 구현 클래스마다 테이블 전략

## **@DiscriminatorColumn(name=“DTYPE”)**

- 테이블에 **DTYPE이라는 컬럼을 만들어 구분자를 넣어줌.**
- default 값 :  DTYPE

```java
import javax.persistence.*;

@Entity
@Inheritance(strategy = InheritanceType.JOINED)
//@Inheritance(strategy = InheritanceType.**SINGLE_TABLE**)
//@Inheritance(strategy = InheritanceType.**TABLE_PER_CLASS**)
@DiscriminatorColumn
public abstract class RItem {

    @Id
    @GeneratedValue
    @Column(name = "ITEM_ID")
    private Long id;

    private String name;
    private int price;

	// getter, setter ...
}
```

## **@DiscriminatorValue(“column_value”)**

- DTYPE 컬럼에 들어가는 값을 지정해줌

```java
@Entity
@DiscriminatorValue("M")//dtype 컬럼 디폴트값
public class Rmovie extends RItem{
    private String director;
    private String actor;
//getter.. setter
}
```

# **조인 전략**

![Untitled](https://zidols.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F5a6fc4d4-dfc1-4131-a177-633839f88ad3%2FUntitled.png?table=block&id=caa0df8e-4c9e-47d5-a2c9-e4ea6ba16bad&spaceId=370dbc5e-872d-4d9e-9f3a-f7113cda9427&width=1920&userId=&cache=v2)

## 장점

- 테이블 정규화
- **외래 키 참조 무결성 제약조건 활용가능**
- **저장공간 효율화**

## 단점

- 조회시 조인을 많이사용 하여 성능 저하가 우려된다.
- 조회 쿼리가 복잡함
- 데이터 저장시 INSERT SQL 2번 호출

```java
Rmovie movie = new Rmovie();
movie.setDirector("bbb");
movie.setActor("aaa");
movie.setName("베트맨");
movie.setPrice(10000);

em.persist(movie);
```

위 코드와 같이 Rmovie 엔티티를 등록하면 아래와 같이 등록 된다.

![Untitled](https://zidols.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F3bfad709-ba8f-4c4b-8cf4-94b84e2121b0%2FUntitled.png?table=block&id=4ec92f3e-b684-4017-a2ee-668dfacca69b&spaceId=370dbc5e-872d-4d9e-9f3a-f7113cda9427&width=2000&userId=&cache=v2)

![조인 전략 시 테이블 ](https://zidols.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fd22fbb33-4bd4-4835-b394-6cbb4a6abd44%2FUntitled.png?table=block&id=83537928-f6a2-4c66-b177-ade2b719493a&spaceId=370dbc5e-872d-4d9e-9f3a-f7113cda9427&width=410&userId=&cache=v2)

조인 전략 시 테이블 

# **단일 테이블 전략**

![Untitled](https://zidols.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F64e333a1-746f-454d-bc89-460dedaa08e0%2FUntitled.png?table=block&id=fc58c14b-02a2-4cc7-a242-a5646a717272&spaceId=370dbc5e-872d-4d9e-9f3a-f7113cda9427&width=1680&userId=&cache=v2)

## 장점

- 조인이 필요 없으므로 일반적으로 조회 성능이 빠름
- 조회 쿼리가 단순함

## 단점

- 자식 엔티티가 매핑한 컬럼은 모두 널 허용 설정을 해줘야 한다.
- 단일 테이블에 모든것을 저장하므로 테이블의 컬럼이 커질수있다.
- 상황에 따라서 조회 성능이 오히려 느려질 수 있다.

![단일 테이블 전략](https://zidols.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F3550c6c7-4459-42a1-9bb2-357b698da878%2FUntitled.png?table=block&id=546207c1-7afb-49bf-a9c4-63546d70e2c5&spaceId=370dbc5e-872d-4d9e-9f3a-f7113cda9427&width=1390&userId=&cache=v2)

단일 테이블 전략

# **구현 클래스마다 테이블 전략**

![Untitled](https://zidols.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fd04713fc-32cb-440c-a2de-a3aa35a2271b%2FUntitled.png?table=block&id=4198456a-8025-4ef2-9625-d72454994c04&spaceId=370dbc5e-872d-4d9e-9f3a-f7113cda9427&width=1880&userId=&cache=v2)

**이 전략은 데이터베이스 설계자와 ORM 전문가 둘 다 추천하지 않는 전략이다.**

## 장점

- 서브 타입을 명확하게 구분해서 처리할 때 효과적
- not null 제약조건 사용 가능

## 단점

- 여러 자식 테이블을 함께 조회할 때 성능이 느리다.(UNION SQL 필요)
- 자식 테이블을 통합해서 쿼리하기 어렵다.

![구현 클래스마다 테이블 전략(Ritem 테이블이 없음)](https://zidols.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F895e8054-af95-4ba1-b006-639a9d7794f8%2FUntitled.png?table=block&id=a2addbaf-f28c-4086-a822-f1ecaea375ca&spaceId=370dbc5e-872d-4d9e-9f3a-f7113cda9427&width=850&userId=&cache=v2)

구현 클래스마다 테이블 전략(Ritem 테이블이 없음)

# **@MappedSuperclass**

**공통 매핑 정보가 필요할 때 사용(id, name)**

![Untitled](https://zidols.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F97386a5b-cd33-4301-8a5d-3bf40fe8b036%2FUntitled.png?table=block&id=de36a74d-1deb-4f0a-bed2-87999f5aa881&spaceId=370dbc5e-872d-4d9e-9f3a-f7113cda9427&width=1570&userId=&cache=v2)

- 상속관계 매핑이 아닌다.
- 엔티티 가 아니며  테이블과 매핑하지 않는다.
- 부모 클래스를 상속 받는 **자식 클래스에 매핑 정보만 제공 한다.**
- 조회, 검색 불가(**em.find(BaseEntity) 불가**)
- 직접 생성해서 사용할 일이 없으므로 **추상 클래스 권장**
- 테이블과 관계 없고, 단순히 엔티티가 공통으로 사용하는 매핑 정보를 모으는 역할
- 주로 등록일, 수정일, 등록자, 수정자 같은 전체 엔티티에서 공통으로 적용하는 정보를 모을 때 사용

> 참고: @Entity 클래스는 엔티티나 @MappedSuperclass로 지정한 클래스만 상속 가능
> 

RBaseEntity.class

```java
package review.entity;

import javax.persistence.MappedSuperclass;
import java.time.LocalDateTime;

@MappedSuperclass
public class RBaseEntity {

    private String createdBy;

    private LocalDateTime createdDate;

    private String lastModifiedBy;

    private LocalDateTime lastModifiedDate;

    //getter... , setter...
}
```

RBaseEntity 상속 받은 RMember Entity

```java
@Entity
public class RMember extends RBaseEntity {

    @Id @GeneratedValue
    private Long id;
    private String username;
    private String city;
    private String zipcode;

    @OneToMany(mappedBy = "member")
    private List<ROrder> orders = new ArrayList<>();
//getter... , setter...
}
```

```bash
Hibernate: 
    
    create table RMember (
       id bigint not null,
        createdBy varchar(255),
        createdDate timestamp,
        lastModifiedBy varchar(255),
        lastModifiedDate timestamp,
        city varchar(255),
        username varchar(255),
        zipcode varchar(255),
        primary key (id)
    )
```

![RBaseEntity의 컬럼정보가 생성되어 있다.](https://zidols.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fd1b18543-72eb-4a7d-b460-1077a79f2dce%2FUntitled.png?table=block&id=1ca54039-a818-4feb-9788-627da85b70cf&spaceId=370dbc5e-872d-4d9e-9f3a-f7113cda9427&width=1320&userId=&cache=v2)

RBaseEntity의 컬럼정보가 생성되어 있다.  

**출처**  
인프런 김영한님 **[자바 ORM 표준 JPA 프로그래밍 - 기본편](https://www.inflearn.com/course/ORM-JPA-Basic)**