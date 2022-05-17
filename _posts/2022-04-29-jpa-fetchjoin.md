---
title:  "JPA - 페치 조인(fetch join)"
excerpt: "SQL에는 없는 fetch join"

categories:
  - JPA
tags:
  - [JPA, fetch join]

toc: true
toc_sticky: true
 
date: 2022-04-29
last_modified_at: 2022-04-29
---
# JPA - 페치 조인(fetch join)

# Fetch join

- SQL 조인 종류가 아니다
- JPQL에서 성능 최적화를 위해 제공하는 기능
- 연관된 Entity나 컬렉션을 SQL 한번에 함께 조회하는 기능
- join fetch 명령어 사용

### 테스트 Entity

Member Entity

```java
@Entity
public class Member extends BaseEntity {
    @Id
    @GeneratedValue
    private Long id;
    @Column(name = "USERNAME")
    private String username;

    @Column(name = "TEAM_ID")
    private Long teamId;

    @ManyToOne(fetch = FetchType.LAZY)  //프록시 객체 조회(지연로딩)
    @JoinColumn(name = "TEAM_ID")       //연관관계의 주인(진짜 매핑) : 연관관계 주인은 fk(왼래키)가 있는 객체, 다(many)가 있는곳(fk 있는곳)이 연관관계 주인
    private Team team;

		//setter, getter 생략
}
```

Team Entity

```java
@Entity
public class Team {
    @Id
    @GeneratedValue
    private Long id;
    private String name;

    @OneToMany(mappedBy = "team")   // member 객체의 team 변수명 과 맵핑되어 있음(개짜 매핑)
    private List<Member> members = new ArrayList<Member>();

    public void addMember(Member member) {
        members.add(member);
    }
}
```

**▣ Entity 페치 조인**

회원을 조회하면서 연관된 팀도 함께 조회(SQL 한번에)

**[JPQL]**

```java
**select m from Member m join fetch m.team**
```

**[SQL]**

```sql
SELECT M.*, T.* FROM MEMBER M
INNER JOIN TEAM T ON M.TEAM_ID = T.ID
```

- sql을 보면 회원 뿐만 아니라 팀(T.*)도 함게 조회해야한다.

## fetch join 전

```java
Team teamA = new Team();
teamA.setName("팀A");
em.persist(teamA);

Team teamB = new Team();
teamB.setName("팀B");
em.persist(teamB);

Member member1 = new Member();
member1.setUsername("회원1");
member1.setTeam(teamA);
em.persist(member1);

Member member2 = new Member();
member2.setUsername("회원2");
member2.setTeam(teamA);
em.persist(member2);

Member member3 = new Member();
member3.setUsername("회원3");
member3.setTeam(teamB);
em.persist(member3);

em.flush();
em.clear();

String query = "select m from Member m";
List<Member> resultList = em.createQuery(query, Member.class).getResultList();

for (Member member : resultList) {
	System.out.println("member = " + member.getUsername() + ", " + member.getTeam().getName());
}
```

```bash
Hibernate:
    /* select
        m
    from
        Member m */ select
            member0_.id as id1_0_,
            member0_.age as age2_0_,
            member0_.TEAM_ID as team_id4_0_,
            member0_.username as username3_0_
        from
            Member member0_
Hibernate:
    select
        team0_.TEAM_ID as team_id1_3_0_,
        team0_.name as name2_3_0_
    from
        Team team0_
    where
        team0_.TEAM_ID=?
member = 회원1, 팀A
member = 회원2, 팀A
Hibernate:
    select
        team0_.TEAM_ID as team_id1_3_0_,
        team0_.name as name2_3_0_
    from
        Team team0_
    where
        team0_.TEAM_ID=?
member = 회원3, 팀B
```

회원1을 조회할 때 실제 SELECT SQL문이 실행이 되었다.

회원2는 이미 1차 캐시에 저장되어 있기 때문에 SQL이 실행되는게 아닌, 1차캐시(영속성 컨텍스트)에서 조회가능하지만, 회원3같은 경우에는 팀A에 없기 때문에(영속성 컨텍스트 없기때문) SELECT 쿼리를 날려서 영속성 컨텍스트에 저장된다.

회원1, 팀A(SQL문)
회원2, 팀A(1차 캐시, 영속성 컨텍스트)
회원3, 팀A(SQL문)

**총 쿼리가 3번 실행된다.**

## fetch join 후

```java
// 이전 쿼리
// String query = "select m from Member m";

// 수정 쿼리
String query = "select m from Member m join fetch m.team";
```

```bash
// 실행문
Hibernate:
    /* select
        m
    from
        Member m
    join
        fetch m.team */ select
            member0_.id as id1_0_0_,
            team1_.TEAM_ID as team_id1_1_1_,
            member0_.age as age2_0_0_,
            member0_.TEAM_ID as team_id4_0_0_,
            member0_.username as username3_0_0_,
            team1_.name as name2_1_1_
        from
            Member member0_
        inner join
            Team team1_
                on member0_.TEAM_ID=team1_.TEAM_ID
member = 회원1, 팀A
member = 회원2, 팀A
member = 회원3, 팀B
```

Member의 Team에 Lazy를 적용해도 페치조인이 우선시 된다.

## 컬렉션 페치 조인

- 일대다 관계, 컬렉션 페치 조인

**[JPQL]**

```java
select t from Team t join fetch t.members
where t.name = '팀A'
```

**[SQL]**

```sql
SELECT T.* , M.*FROM TEAM T INNER JOIN MEMBER M ON T.ID = M.TEAM_ID
WHERE T.NAME = '팀A'
```
```bash
Team teamA = new Team();
teamA.setName("팀A");
em.persist(teamA);

Team teamB = new Team();
teamB.setName("팀B");
em.persist(teamB);

Member member1 = new Member();
member1.setUsername("회원1");
member1.setTeam(teamA);
em.persist(member1);

Member member2 = new Member();
member2.setUsername("회원2");
member2.setTeam(teamA);
em.persist(member2);

Member member3 = new Member();
member3.setUsername("회원3");
member3.setTeam(teamB);
em.persist(member3);

em.flush();
em.clear();

String query = "select t from Team t join fetch t.members";

List<Team> resultList = em.createQuery(query, Team.class).getResultList();

for (Team team : resultList) {
  System.out.println("team = " + team.getName() + "| members = " + team.getMembers().size());
  for(Member member : team.getMembers()){
  	System.out.println(" -> member = " + member);
  }
}
```

```bash
Hibernate:
    /* select
        t
    from
        Team t
    join
        fetch t.members */ select
            team0_.TEAM_ID as team_id1_1_0_,
            members1_.id as id1_0_1_,
            team0_.name as name2_1_0_,
            members1_.age as age2_0_1_,
            members1_.TEAM_ID as team_id4_0_1_,
            members1_.username as username3_0_1_,
            members1_.TEAM_ID as team_id4_0_0__,
            members1_.id as id1_0_0__
        from
            Team team0_
        inner join
            Member members1_
                on team0_.TEAM_ID=members1_.TEAM_ID
teamname = 팀A, team = Team@0x100
-> username = 회원1, member = Member@0x200 
-> username = 회원2, member = Member@0x300 
teamname = 팀A, team = Team@0x100
-> username = 회원1, member = Member@0x200 
-> username = 회원2, member = Member@0x300
```

![member_join_team]({{ site.url }}{{ site.baseurl }}/assets/images/meberjointeam.png)

1:N 조인이 되기 때문에 데이터가 뻥튀기 되어 조회된다.

만약 MEMBER가 1만건 있으면 1만건 출력한다.

## 페치조인과 DISTINCT

- DISTINCT는 중복 제거 명령
- JPQL의 DISTINCT는 2가지 기능을 제공
    - SQL에 DISTINCT를 추가
    - 애플리케이션에서 Entity 중복 제거

**[JPQL]**

```java
select distinct t FROM Team join fetch t.members
where t.name = '팀A'
```

![teamMember]({{ site.url }}{{ site.baseurl }}/assets/images/temamember.png)

```bash
Hibernate:
    /* select
        DISTINCT t
    from
        Team t
    join
        fetch t.members */ select
            distinct team0_.TEAM_ID as team_id1_1_0_,
            members1_.id as id1_0_1_,
            team0_.name as name2_1_0_,
            members1_.age as age2_0_1_,
            members1_.TEAM_ID as team_id4_0_1_,
            members1_.username as username3_0_1_,
            members1_.TEAM_ID as team_id4_0_0__,
            members1_.id as id1_0_0__
        from
            Team team0_
        inner join
            Member members1_
                on team0_.TEAM_ID=members1_.TEAM_ID
```

- SQL입장에서는 SQL에 DISTINCT를 추가하지만 데이터가 조금 다르므로 SQL결과에서 중복제거 실패
    - 모든 컬럼이 같아야 distinct로 중복 데이터가 제거된다.
- DISTINCT가 추가로 애플리케이션에서 중복 제거 시도
- **JPA에서는 같은 식별자를 가진 Team Entity를 제거**

```java
Team teamA = new Team();
teamA.setName("팀A");
em.persist(teamA);

Team teamB = new Team();
teamB.setName("팀B");
em.persist(teamB);

Member member1 = new Member();
member1.setUsername("회원1");
member1.setTeam(teamA);
em.persist(member1);

Member member2 = new Member();
member2.setUsername("회원2");
member2.setTeam(teamA);
em.persist(member2);

Member member3 = new Member();
member3.setUsername("회원3");
member3.setTeam(teamB);
em.persist(member3);

em.flush();
em.clear();

String query = "select DISTINCT t from Team t join fetch t.members";

List<Team> resultList = em.createQuery(query, Team.class).getResultList();

for (Team team : resultList) {
  System.out.println("team = " + team.getName() + ", members = " + team.getMembers().size());
  for(Member member : team.getMembers()){
  	System.out.println(" -> member = " + member);
  }
}
```

```bash
team = 팀A, members = 2
 -> member = Member{id=3, username='회원1', age=0}
 -> member = Member{id=4, username='회원2', age=0}
team = 팀B, members = 1
 -> member = Member{id=5, username='회원3', age=0}
```

- **일대다는 중복이 있지만 다대일은 중복이 없다.**
    
    TEAM -> MEMBER (중복생성)
    
    MEMBER -> TEAM (중복X)
    

## 페치 조인과 일반 조인의 차이

- 일반 조인 실행시 연관된 Entity를 함께 조회하지 않음.

**[JPQL]**

```java
select t from Team t join t.members where t.name = '팀A'
```

**[SQL]**

```sql
select T.* from TEAM T
inner join MEMBER M
on T.ID = M.TEAM_ID
where T.NAME = '팀A'
```

```java
Team teamA = new Team();
teamA.setName("팀A");
em.persist(teamA);

Team teamB = new Team();
teamB.setName("팀B");
em.persist(teamB);

Member member1 = new Member();
member1.setUsername("회원1");
member1.setTeam(teamA);
em.persist(member1);

Member member2 = new Member();
member2.setUsername("회원2");
member2.setTeam(teamA);
em.persist(member2);

Member member3 = new Member();
member3.setUsername("회원3");
member3.setTeam(teamB);
em.persist(member3);

em.flush();
em.clear();

// 일반조인
String query = "select t from Team t join t.members";

List<Team> resultList = em.createQuery(query, Team.class).getResultList();

for (Team team : resultList) {
  System.out.println("team = " + team.getName() + ", members = " + team.getMembers().size());
  for(Member member : team.getMembers()){
 	 System.out.println(" -> member = " + member);
  }
}
```

```bash
Hibernate:
    /* select
        t
    from
        Team t
    join
        t.members */ select
            team0_.TEAM_ID as team_id1_1_,
            team0_.name as name2_1_
        from
            Team team0_
        inner join
            Member members1_
                on team0_.TEAM_ID=members1_.TEAM_ID
Hibernate:
    select
        members0_.TEAM_ID as team_id4_0_0_,
        members0_.id as id1_0_0_,
        members0_.id as id1_0_1_,
        members0_.age as age2_0_1_,
        members0_.TEAM_ID as team_id4_0_1_,
        members0_.username as username3_0_1_
    from
        Member members0_
    where
        members0_.TEAM_ID=?
team = 팀A, members = 2
 -> member = Member{id=3, username='회원1', age=0}
 -> member = Member{id=4, username='회원2', age=0}
team = 팀A, members = 2
 -> member = Member{id=3, username='회원1', age=0}
 -> member = Member{id=4, username='회원2', age=0}
Hibernate:
    select
        members0_.TEAM_ID as team_id4_0_0_,
        members0_.id as id1_0_0_,
        members0_.id as id1_0_1_,
        members0_.age as age2_0_1_,
        members0_.TEAM_ID as team_id4_0_1_,
        members0_.username as username3_0_1_
    from
        Member members0_
    where
        members0_.TEAM_ID=?
team = 팀B| members = 1
 -> member = Member{id=5, username='회원3', age=0}
Hibernate:
    /* select
        t
    from
        Team t
    join
        t.members */ select
            team0_.TEAM_ID as team_id1_1_,
            team0_.name as name2_1_
        from
            Team team0_
        inner join
            Member members1_
                on team0_.TEAM_ID=members1_.TEAM_ID
```

### 일반 조인

- select절에서 Team만 가지고 온다.
    - 일반조인할 때 연관된 Entity를 함께 조회하지 않기 떄문.
- 지연로딩으로 인해 각 객체를 불러올때 조회한다.(team.getName(),team.getMembers().size())

### **페치조인 , 일반조인 차이점**

- 일반조인
    - JPQL은 결과를 반환할 때 연관관계 고려 X
    - SELECT절에 지정한 Entity만 조회할 뿐
- 페치조인
    - 페치조인을 사용할때만 연관된 Entity도 함께 조회(즉시로딩)
    - 페치조인은 객체 그래프를 SQL 한번에 조회하는 개념

## **페치 조인의 특징과 한계**

- 페치 조인 대상에는 별칭을 줄 수 없다.
    - 하이버네이트는 가능하지만 가급적 사용을 지양한다.
- 둘 이상의 컬렉션은 페치조인을 할 수 없다.
- 컬렉션을 페치조인하면 페이징 API를 사용할 수 없다.
- 일대일, 다대일같은 단일 값 연관 필드들은 페치조인해도 페이징 가능
- 하이버네이트는 경고 로그를 남기고 메모리에서 페이징(매우 위험하다)

```java
String query = "select t from Team t join fetch  t.members m";

List<Team> resultList = em.createQuery(query, Team.class)
  .setFirstResult(0)
  .setMaxResults(1)
  .getResultList();
```

```bash
// 실행문
WARN: HHH000104: firstResult/maxResults specified with collection fetch; applying in memory!
Hibernate:
    /* select
        t
    from
        Team t
    join
        fetch  t.members m */ select
            team0_.TEAM_ID as team_id1_1_0_,
            members1_.id as id1_0_1_,
            team0_.name as name2_1_0_,
            members1_.age as age2_0_1_,
            members1_.TEAM_ID as team_id4_0_1_,
            members1_.username as username3_0_1_,
            members1_.TEAM_ID as team_id4_0_0__,
            members1_.id as id1_0_0__
        from
            Team team0_
        inner join
            Member members1_
                on team0_.TEAM_ID=members1_.TEAM_ID
team = 팀A| members = 2
 -> member = Member{id=3, username='회원1', age=0}
 -> member = Member{id=4, username='회원2', age=0}
```

**'WARN: HHH000104: firstResult/maxResults specified with collection fetch; applying in memory!'** 

또한 페이징 쿼리(ex, limit 등)가 없으며 데이터를 다 불러온다.

- 연관된 Entity들을 SQL 한번으로 조회 - 성능 최적화
- Entity에 직접 적용하는 글로벌 로딩 전략보다 우선시됨.
    - @OneToMany(fetch = FetchType.LAZY)//글로벌 로딩 전략
- 실무에서 글로벌 로딩 전략은 모두 지연로딩
- 최적화가 필요한 곳은 페치조인 적용

## 정리

- 모든 것을 페치 조인으로 해결할 수는 없음
- 페치조인은 객체 그래프를 유지할 때 사용하면 효과적
- **여러 테이블을 Join해서 Entity가 가진 모양이 아닌, 전혀 다른 결과를 내야 하면, 페치 조인보다는 일반 조인을 사용하고 필요한 데이터들만 조회해서 DTO로 변환하는 것이 효과적**