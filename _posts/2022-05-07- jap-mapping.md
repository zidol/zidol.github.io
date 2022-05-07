---
title:  "JPA - 엔티티 매핑"
excerpt: "객체와 테이블 매핑"

categories:
  - JPA
tags:
  - [JPA]

toc: true
toc_sticky: true
 
date: 2022-05-07
last_modified_at: 2022-05-07
---
# **객체와 테이블 매핑**

## **@Entity**

- @Entity가 붙은 클래스는 JPA가 관리, 엔티티라 한다.
- JPA를 사용해서 테이블과 매핑할 클래스는 **@Entity** 필수로 붙여줘야한다.

### 주의사항

- **기본 생성자 필수**(파라미터가 없는 public 또는 protected 생성자)
- final 클래스, enum, interface, inner 클래스 사용 하지 않는다.
- 저장할 필드에 final 사용 하지 않는다.

### 속성

- name
    - jap에서 사용할 엔티티 이름을 저장한다.
    - 기본값: 클래스 이름을 그대로 사용(ex : Member)
    - 같은 클래스 이름이 없으면 가급적 기본값 사용한다.

## @Table

- @Table은 엔티티와 매핑할 테이블을 지정한다.

| 속성 | 기능 | 기본값 |
| --- | --- | --- |
| name | 매핑할 테이블 이름 | 엔티티 이름을 사용 |
| catalog | 데이터베이스 catalog 매핑 |  |
| schema | 데이터베이스 schema 매핑 |  |
| uniqueConstraints(DDL) | DDL 생성 시에 유니크 제약 조건 생성(키 이름을 지정 할수 있음) |  |

```java
@Entity
@Table(name = "ARTICLES")
public class Article {
    // ...
}

@Table(uniqueConstraints = {
@UniqueConstraint(name = "UniqueNumberAndStatus", columnNames = {"personNumber", "isActive"}),
@UniqueConstraint(name = "UniqueSecurityAndDepartment", columnNames = {"securityNumber", "departmentCode"})})
```

아래 코드 처럼 데이터베이스에 order라는 예약어가 있기때문에 @Table(name = "orders") 를 선언하여

테이블명을 지정 해준다. 

```java
package review.entity;

import javax.persistence.*;
import java.time.LocalDateTime;
import java.util.ArrayList;
import java.util.List;

@Entity
@Table(name = "orders")
public class Order {

    @Id @GeneratedValue
    @Column(name = "ORDER_ID")
    private Long id;

    @ManyToOne
    @JoinColumn(name = "MEMBER_ID")
    private Member member;

    @OneToMany(mappedBy = "order")
    private List<OrderItem> orderItems = new ArrayList<>();

    private LocalDateTime orderDate;

    @Enumerated(EnumType.STRING)
    private OrderStatus status;

}
```

---

# **필드와 컬럼 매핑**

## 예시 요구사항

1. 회원은 일반회원과 관리자로 구분해야 한다.
2. 회원 가입일과 수정일이 있어야 한다.
3. 회원을 설명할수 있는 필드가있어야한다. 이필드는 길이 제한이 없다.

위 요구사항 추가시 아래 코드와 같이 많은 어노테이션이 추가 된다.

```java
import javax.persistence.*;
import java.time.LocalDate;
import java.time.LocalDateTime;
import java.util.Date;
@Entity
public class Member {

 @Id
 private Long id;

 @Column(name = "name")
 private String username;
 
 private Integer age;
 
 @Enumerated(EnumType.STRING)
 private RoleType roleType;
 
 @Temporal(TemporalType.TIMESTAMP)
 private Date createdDate;
 
 @Temporal(TemporalType.TIMESTAMP)
 private Date lastModifiedDate;
 
 @Lob
 private String description;
 
	// getter, setter
```

## 매핑 어노테이션

| 어노테이션 | 설명 |
| --- | --- |
| @Column | 컬럼 매핑 |
| @Temporal | 날짜 타입 매핑 |
| @Enumerated | enum 타입 매핑 |
| @Lob | BLOB, CLOB 매핑 |
| @Transient | 특정 필드를 컬럼에 매핑하지 않음(매핑무시) |

## @Column

| 속성 | 설명 | 기본값 |
| --- | --- | --- |
| name | 필드와 매핑할 테이블의 컬럼 이름 | 객체의 필드 이름 |
| insertable, updatable | 등록, 변경가능 여부 결정 | TRUE |
| nullable(DDL) | null 값의 허영 여부를 설정, false로 설정하면 DDL생성 시 not null 제약 조건이 붙음 |  |
| unique(DDL) | @Table의 uniqueConstraints와 같지만 한 컬럼에 간단히 유니크 제약 조건을 걸 때 사용(@Table에서 주로 사용) |  |
| columnDefinition(DDL) | 데이터베이스 컬럼 정보를 직접 줄 수 있다. ex) varchar(100) default ‘EMPTY’ | 필드의 자바 타입과 방언의 정보를 사용 |
| length(DDL) | 문자 길이 제약조건, String 타입에만 사용한다. | 255 |
| precsion, scale(DDL) | 1. BigDecimal 타입에서 사용(BigInteger도 사용할 수 있음). 2. precision은 소수점을 포함한 전체 자 릿수를, scale은 소수의 자릿수 다. 3. 참고로 double, float 타입에는 적용되지 않는다. 아주 큰 숫자나 정 밀한 소수를 다루어야 할 때만 사용한다. | precision=19, scale=2 |

## **@Enumerated**

### 자바 enum  타입을 매핑할 때 사용
****

| 속성 | 설명 | 기본값 |
| --- | --- | --- |
| value | - EnumType.ORDINAL : enum 순서를 DB에 저장
- EnumType.STRING: enum 이름을 DB에 저장 | EnumType.ORDINAL |

***주의사항**

- **ORDINAL 사용하지 않는다.**
    - 데이터 베이스에 순서가 저장 되기때문에 enum에 데이터 추가시 order값이 바뀌게 되어 이전에 등록한 값과 달라져서 운영에 문제가 됨
    
    A : USER, B: ADMIN일때 C가 추가 되기 전 아래 처럼 GUEST 추가 시 테이블 조회 결과 처럼 USER와 같은 순서를 같게 되어 장애를 일으킨다.
    
    ```java
    public enum RoleType {
        USER, ADMIN
    }
    //하나 더 추가 시
    public enum RoleType {
        GUEST, USER, ADMIN
    }
    ```
    
    ![Untitled](https://zidols.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F5172a50a-189e-47ea-b5e1-9de9874ec49a%2FUntitled.png?table=block&id=430f4cd7-c652-4fcd-beb0-3f381bd3955f&spaceId=370dbc5e-872d-4d9e-9f3a-f7113cda9427&width=2000&userId=&cache=v2)
    

## **@Temporal**

### **날짜 타입(java.util.Date, java.util.Calendar)을 매핑할 때 사용**

> **자바8 이상에서 LocalDate, LocalDateTime을 사용할 때는 생략 가능(최신 하이버네이트 지원)**
> 

value 속성

- **TemporalType.DATE**: 날짜, 데이터베이스 date 타입과 매핑
(예: 2013–10–11)
- **TemporalType.TIME**: 시간, 데이터베이스 time 타입과 매핑
(예: 11:11:11)
- **TemporalType.TIMESTAMP**: 날짜와 시간, 데이터베이 스
    
    timestamp 타입과 매핑(예: 2013–10–11 11:11:11)
    

## **@Lob**

### **데이터베이스 BLOB, CLOB 타입과 매핑**

- **@Lob에는 지정할 수 있는 속성이 없다.**
- **매핑하는 필드 타입이 문자면 CLOB 매핑, 나머지는 BLOB 매핑**
    - **CLOB: String, char[], java.sql.CLOB**
    - **BLOB: byte[], java.sql. BLOB**

## **@Transient**

- **필드 매핑을 하지 않음**
- **데이터베이스에 저장 하지  않고 조회 하지 않음**
- **주로 메모리상에서만 임시로 어떤 값을 보관하고 싶을 때 사용**

```java
@Transient
private Integer temp;
```

---

출처

인프런 김영한님 **[자바 ORM 표준 JPA 프로그래밍 - 기본편](https://www.inflearn.com/course/ORM-JPA-Basic)**
