---
title:  "JPA - 연관관계"
excerpt: "jpa 연관관계 및 양방향 연관관계"

categories:
  - JPA
tags:
  - [JPA]

toc: true
toc_sticky: true
 
date: 2022-05-01
last_modified_at: 2022-05-01
---
# 연관관계 매핑

## **연관관계가 필요한 이유**

### **객체를 테이블에 맞추어 모델링**

**연관관계가 없는 객체**

![Untitled](https://zidols.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F400af073-0336-47fe-b43d-ab7e762707e8%2FUntitled.png?table=block&id=fe191f91-dfce-4779-9953-286b0b7ec83f&spaceId=370dbc5e-872d-4d9e-9f3a-f7113cda9427&width=2000&userId=&cache=v2)

**참조 대신에 외래 키를 그대로 사용
Mybatis 사용시 vo를 이런식으로 설계했었다.**

```java
@Entity
public class RMember {
    @Id
    @GeneratedValue
    private Long id;

    @Column(name = "USERNAME")
    private String name;

    @Column(name = "TEAM_ID")
    private Long teamId;

    // getter, setter
}
```

```java
@Entity
public class RTeam {

    @Id @GeneratedValue
    private Long id;

    private String name;

   //getter, setter
}
```

**외래 키 식별자를 직접 다룬다.**

```java
//팀 저장
RTeam team = new RTeam();
team.setName("TeamA");
em.persist(team);

//회원 저장
RMember member = new RMember();
member.setName("member1");
member.setTeamId(team.getId());
em.persist(member);

//조회
RMember findMember = em.find(RMember.class, member.getId());

//연관관계 없음
RTeam findTeam = em.find(RTeam.class, team.getId());
```

**식별자로 다시 조회, 객체 지향적인 방법은 아니다.
물론 테이블의 내용은 정상적으로 들어간다.**

![Untitled](https://zidols.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F576c8ccc-f230-44d5-a013-e2240819b3a8%2FUntitled.png?table=block&id=ffb7af83-7651-4dd6-a626-4b0876e4d58a&spaceId=370dbc5e-872d-4d9e-9f3a-f7113cda9427&width=860&userId=&cache=v2)

## 테이블 중심적 설계시 문제점

**객체를 테이블에 맞추어 데이터 중심으로 모델링하면, 협력 관계를 만들 수 없다.**

- **테이블은 외래 키로 조인을 사용해서 연관된 테이블을 찾는다.**
- 객체는 참조를 사용해서 연관된 객체를 찾는다.
- 테이블과 객체 사이에는 이러한 큰 간격이 있다.

---

# **단방향 연관관계**

## 객체 지향 모델링

### 객체 연관관계 사용

![Untitled](https://zidols.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fc313fa88-9a6f-47b9-ba33-d52f1900c05a%2FUntitled.png?table=block&id=e7b9ca0e-82f5-4fef-b3e0-954d1bf0e5e2&spaceId=370dbc5e-872d-4d9e-9f3a-f7113cda9427&width=2000&userId=&cache=v2)

## **객체의 참조와 테이블의 외래 키를 매핑**

```java

@Entity
public class RMember {
    @Id
    @GeneratedValue
    private Long id;

    @Column(name = "USERNAME")
    private String name;

//    @Column(name = "TEAM_ID")
//    private Long teamId;

    @ManyToOne
    @JoinColumn(name = "TEAM_ID")
    private RTeam rTeam;

	//getter, setter
    
}
```

## **ORM 매핑**

![Untitled](https://zidols.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F0512d3b2-b6b9-464d-a7d7-1bb7fddbfbbb%2FUntitled.png?table=block&id=26db1f14-ebbc-48c5-aae9-1e3678c5aa2a&spaceId=370dbc5e-872d-4d9e-9f3a-f7113cda9427&width=2000&userId=&cache=v2)

## 연관 관계 저장

```java
//팀 저장
RTeam team = new RTeam();
team.setName("TeamA");
em.persist(team);

//회원 저장
RMember member = new RMember();
member.setName("member1");
member.setrTeam(team);// 단방향 연관관계 설정, 참조 저장
em.persist(member);
```

영속성 컨텍스트를 초기화 해주면 1차 캐시에서 가져 오지 않고 DB를 직접 조회 한다.

```java
//영속성 컨텍스 초기화
em.flush();
em.clear();
```

## 참조로 연관관계 조회 - 객체 그래프 탐색

```java
//조회
RMember findMember = em.find(RMember.class, member.getId());

//연관관계 없음
RTeam findTeam = findMember.getrTeam();

System.out.println("findTeam.getName() = " + findTeam.getName());
```

@ManyToOne은 `FetchType` 기본값이 EAGER이기 때문에 조인 쿼리로 한번에 가져온다.

```bash
Hibernate: 
    /* insert review.entity.RTeam
        */ insert 
        into
            RTeam
            (name, id) 
        values
            (?, ?)
Hibernate: 
    /* insert review.entity.RMember
        */ insert 
        into
            RMember
            (USERNAME, TEAM_ID, id) 
        values
            (?, ?, ?)
Hibernate: 
    select
        rmember0_.id as id1_11_0_,
        rmember0_.USERNAME as username2_11_0_,
        rmember0_.TEAM_ID as team_id3_11_0_,
        rteam1_.id as id1_12_1_,
        rteam1_.name as name2_12_1_ 
    from
        RMember rmember0_ 
    left outer join
        RTeam rteam1_ 
            on rmember0_.TEAM_ID=rteam1_.id 
    where
        rmember0_.id=?
findTeam.getName() = TeamA
```

## **연관관계 수정(fk 수정)**

```java
//새로운 팀
RTeam teamB = new RTeam();
teamB.setName("teamB");
em.persist(teamB);

findMember.setrTeam(teamB);
System.out.println("team = " + findMember.getrTeam().getName());
```

조회 한 member 객체에 새로운 팀을 set 하면 변경감지로 팀이 수정된다.

```java
Hibernate: 
    call next value for hibernate_sequence
team = teamB
Hibernate: 
    /* insert review.entity.RTeam
        */ insert 
        into
            RTeam
            (name, id) 
        values
            (?, ?)
Hibernate: 
    /* update
        review.entity.RMember */ update
            RMember 
        set
            USERNAME=?,
            TEAM_ID=? 
        where
            id=?
```

---

# **양방향 연관관계와 연관관계의 주인**

## **양방향 매핑**

![Untitled](https://zidols.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fff12d298-e3f2-4af3-b0c9-39a964224d13%2FUntitled.png?table=block&id=8776bd23-a93b-4d83-b474-45bc0e37a37c&spaceId=370dbc5e-872d-4d9e-9f3a-f7113cda9427&width=2000&userId=&cache=v2)

### RMember Entity

Member 엔티티는 단방향과 동일하다.

```java

@Entity
public class RMember {
    @Id
    @GeneratedValue
    private Long id;

    @Column(name = "USERNAME")
    private String username;

    @ManyToOne
    @JoinColumn(name = "TEAM_ID")
    private RTeam rTeam;
//getter, setter
}
```

### RTeam Entity

Team 엔티티는 컬렉션을 추가한다.

```java

@Entity
public class RTeam {

    @Id @GeneratedValue
    private Long id;

    private String name;

    @OneToMany(mappedBy = "rTeam")
    private List<RMember> members = new ArrayList<>();
}
```

## **반대 방향으로 객체 그래프 탐색**

```java
//조회
RTeam findTeam = em.find(RTeam.class, team.getId());
List<RMember> members = findTeam.getMembers();// 역방향 조회
```

## **연관관계의 주인과 mappedBy**

@OneToMany(mappedBy = "rTeam" 에서 rTeam은 RMember 엔티티의 RTeam 필드 명을 써야하며 rTeam에 의해서 맵핑 되었다는 의미이다.(조회만 가능)

### **객체와 테이블이 관계를 맺는 차이**

- 객체 연관관계는 2개
    - 회원 → 팀 연관관계 1개(단방향)
    - 팀 → 회원 연관관계 1개(단방행)
- 테이블 연관관계는 1개
    - 회원 ↔ 팀의 연관관계 1개(양방향)
    
    ![Untitled](https://zidols.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F04aa628a-58aa-4319-8ef9-eece9714db11%2FUntitled.png?table=block&id=20d5bc7b-5ef7-4904-995c-ca0794257e11&spaceId=370dbc5e-872d-4d9e-9f3a-f7113cda9427&width=2000&userId=&cache=v2)
    

## 객체의 양방향 관계

- 객체의 양방향 관계는 양방향 관계가 아니라 서로 다른 단방향 관계 2개다.
- 객체를 양방향으로 참조 하려면 단방향 연관관계를 2개 만들어야 한다.

![Untitled](https://zidols.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fc54194cf-7585-4834-a210-d31243e13afa%2FUntitled.png?table=block&id=8c79c7bc-fe06-4d5a-86a5-02ba9257f20c&spaceId=370dbc5e-872d-4d9e-9f3a-f7113cda9427&width=580&userId=&cache=v2)

## 테이블의 양방향 연관관계

- **테이블은 외래 키 하나로 두 테이블의 연관관계를 관리**
- **MEMBER.TEAM_ID 외래 키 하나로 양방향 연관관계 가짐**
    - **양쪽으로 조인하여 연관관계 정보를 조회 가능**

```sql
SELECT *
  FROM RMEMBER M
  JOIN RTEAM T ON M.TEAM_ID = T.TEAM_ID;

SELECT *
  FROM TEAM T
  JOIN MEMBER M ON T.TEAM_ID = M.TEAM_Id
```

## 외래키 관리

양방향 관계에서는 둘 중 하나로 외래키를 관리 해야한다.

![Untitled](https://zidols.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Ff5c28f69-ac09-420a-bd8b-2ca507e50f5f%2FUntitled.png?table=block&id=ce856a3e-cd70-402c-a95d-f7a7fa5e3fb5&spaceId=370dbc5e-872d-4d9e-9f3a-f7113cda9427&width=2000&userId=&cache=v2)

## **연관관계의 주인(Owner)**

### **양방향 매핑 규칙**

- 객체의 두 관계중 하나를 연관관계의 주인으로 지정
- **연관관계의 주인만이 외래 키를 관리(등록, 수정)**
- **주인이 아닌쪽은 읽기만 가능**
- 주인은 mappedBy 속성 사용하면 안됨
- 주인이 아니면 mappedBy 속성으로 주인 지정

## 누구로 주인으로 해야할까?

- 외래 키가 있는 곳을 주인으로 해야함
- 예제에서는 Member.team 이 연관관계의 주인

![Untitled](https://zidols.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2Fff7a4793-6471-414b-8e2b-b9b509bc1629%2FUntitled.png?table=block&id=42f6ab79-b81f-4690-ad51-a646cc44a36c&spaceId=370dbc5e-872d-4d9e-9f3a-f7113cda9427&width=2000&userId=&cache=v2)

## **양방향 매핑시 가장 많이 하는 실수**

```java
//팀 저장
RTeam team = new RTeam();
team.setName("TeamA");
em.persist(team);

//회원 저장
RMember member = new RMember();
member.setUsername("member1");
em.persist(member);
//역방향(주인이 아닌 방향)만 연관관계 설정
team.getMembers().add(member);
```

아래와 같이 역방향으로 데이터를 넣어주면 DB에는 null이 들어가 있다. **따라서, 양방향 매핑시 연관관계의 주인에  값을 입력해야 한다.** (순수한 객체 관계를 고려하면 항상 양쪽다 값을 입력해야한다.)

![Untitled](https://zidols.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F70001f3b-b7ab-4527-aa7b-99eb65c649e8%2FUntitled.png?table=block&id=ed4ca218-7aea-4576-9621-14dd7f0526da&spaceId=370dbc5e-872d-4d9e-9f3a-f7113cda9427&width=2000&userId=&cache=v2)

```java
//팀 저장
RTeam team = new RTeam();
team.setName("TeamA");
em.persist(team);

//회원 저장
RMember member = new RMember();
member.setUsername("member1");

team.getMembers().add(member);
//연관관계의 주인에 값 설정
member.setTeam(team);

em.persist(member);
```

![Untitled](https://zidols.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F2ca046c4-143f-4996-a82b-4bb6167f8364%2FUntitled.png?table=block&id=98beba9a-e97d-43c5-ac37-482dd71ea83d&spaceId=370dbc5e-872d-4d9e-9f3a-f7113cda9427&width=750&userId=&cache=v2)

하지만 객체까지 고려한다면, 양쪽 다 관계를 맺어야한다. 즉, 객체의 양방향 연관관계는 양쪽 모두 관계를 맺어주어야 순수한 객체 상태에서도 정상적으로 동작한다.

이렇듯 양방향 연관관계는 결국 양쪽 모두를 신경써야한다. 만약, setTeam과 getMembers().add를 각각 호출하면 실수가 발생할 수 있다. 따라서 양쪽 모두의 관계를 맺어주는 것을 하나의 코드처럼 사용하는 것이 안전하다.

메소드의 명칭도 setter의 명칭을 사용 하지 않고 changeTeam와 같이 의미를 알 수 있는 명명법을 사용한다.

RMember.class

```java
public void changeTeam(RTeam rTeam) {
        this.rTeam = rTeam;
        rTeam.getMembers().add(this);//나 자신
}
```

```java

//팀 저장
RTeam team = new RTeam();
team.setName("TeamA");
em.persist(team);

//회원 저장
RMember member = new RMember();
member.setUsername("member1");
member.changeTeam(team);  //연관관계 메소드 member 중심으로 값 세팅
em.persist(member);

//영속성 컨텍스 초기화
//em.flush();
//em.clear();

RTeam findTeam = em.find(RTeam.class, team.getId());//1차 캐시
List<RMember> members = findTeam.getMembers();

System.out.println("================");
for (RMember m : members) {
    System.out.println("m = " + m.getUsername());
}
System.out.println("================");
```

```bash
Hibernate: 
    call next value for hibernate_sequence
Hibernate: 
    call next value for hibernate_sequence
================
m = member1
================
Hibernate: 
    /* insert review.entity.RTeam
        */ insert 
        into
            RTeam
            (name, id) 
        values
            (?, ?)
Hibernate: 
    /* insert review.entity.RMember
        */ insert 
        into
            RMember
            (TEAM_ID, USERNAME, id) 
        values
            (?, ?, ?)
```

이렇듯  아래와 같이  team을 조회한 후 member를 조회하면 DB에서 조회 하지 않고 1차 캐시에서 조회 하기때문에 쿼리를 질의 하지 않는다.

```java
member.changeTeam(team);  
member.changeTeam(team2);  
```

위와 같이 연속적으로 changeTeam을 호출한 이후 team에서 멤버를 조회하면 member1가 여전히 조회된다. team2로 변경할 때 team1과의 관계를 제거하지 않았기 때문이다.

```java
public void changeTeam(RTeam rTeam) {
    if (this.rTeam != null) { // 기존에 이미 팀이 존재한다면
        this.rTeam.getMembers().remove(this); // 관계를 끊는다.
    }
    this.rTeam = rTeam;
    rTeam.getMembers().add(this);//나 자신
}
```

따라서 위와 같이 기존 팀과의 관계를 제거하는 코드를 추가해야 정상적으로 동작한다.

출처

인프런 김영한님 **[자바 ORM 표준 JPA 프로그래밍 - 기본편](https://www.inflearn.com/course/ORM-JPA-Basic)**
[https://joanne.tistory.com/220](https://joanne.tistory.com/220)