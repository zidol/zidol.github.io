---
title:  "Database 정규화(Normalization)"
excerpt: "데이터베이스 정규화 하기.."

categories:
  - Database
tags:
  - [Database, Postgresql]

toc: true
toc_sticky: true
 
date: 2022-04-19
last_modified_at: 2022-04-19
---

# **1. 정규화(Normalization)**

---

## **정규화(Normalization)이란?**

데이터의 **중복**을 줄이고 **무결성**을 향상시키는 등 여러 목적을 달성하기 위해서 **재디자인**하는 것이다.

- 속성들끼리의 종속 관계를 분석하여 **여러개의 릴레이션으로 분해**하는 과정 → 데이터베이스의 설계를 재구성하는 테크닉a
- 일반적으로 테이블을 여러개로 분해하면 속도는 상대적으로 느려질 수 있지만, 분해하지 않으면 이상 문제들이 발생한다.

즉, 정규화는 **릴레이션 내에서 중복을 제거하는 과정**이다.

## 데이터베이스 정규화의 목적

1. 불필요한 데이터(data redundancy)를 제거, 데이터의 **중복을 최소화**하기 위해서 (저장 공간 최소화)
2. 각종 **이상 현상(Anomaly)** 을 방지하기 위해서, 테이블의 구성을 논리적이고 직관적으로 한다.
3. 데이터 테이블 구성이 논리적, 직관적으로 하기 위해서 (자료 구조의 안정성 최대화)
4. 다양한 관점에서의 query를 지원하기 위해서 (효과적인 검색 알고리즘)
5. 무결성 제약조건의 시행을 간단하게 하기 위해서 (데이터 무결성 유지)

## 데이터베이스 정규화의 장점

- 응용프로그램 단에서 불필요한 로직을 없앨 수 있다.
- 올바른 데이터만 얻을 수 있다.(변칙 방지)
- 불필요한 쿼리(예를들면, 서브 쿼리) 제거로 성능 향상

##  데이터베이스 정규화의 단점

- 릴레이션의 분해로 인해 릴레이션 간의 **연산(JOIN 연산)이 많아진다.** (응답 시간이 느려질 수 있다.)
    - 정규화를 수행한다는 것은 데이터를 결정하는 결정자에 의해 함수적 종속을 가지고 있는 일반 속성을 의존자로 하여 입력/수정/삭제 이상을 제거하는 것이다.
    - 데이터의 중복 속성을 제거하고 결정자에 의해 동일한 의미의 일반 속성이 하나의 테이블로 집약되므로 한 테이블의 데이터 용량이 최소화되는 효과가 있다. 따라서 정규화된 테이블은 데이터를 처리할 때 속도가 빨라질 수도 있고 느려질 수도 있는 특성이 있다.
    
    ---
    
  # 2. 정규화 과정
    
  ## **[ 제1 정규화 ]**
    
    제1 정규화란 테이블의 컬럼이 원자값(Atomic Value, 하나의 값)을 갖도록 테이블을 분해하는 것.
    
     예를 들어 아래와 같은 고객 취미 테이블이 존재한다고 하자.
    
    Clients
    
    | client | hobbys |
    | --- | --- |
    | 박지성 | 독서,위닝 |
    | 손흥민 | 독서,영화감상 |
    | 차범근 | 골프 |
    | 황의조 | 골프,영화감상 |
    
    위의 테이블에서 여러 고객의 취미를지 가지고 있기 때문에 제1 정규형을 만족하지 못하고 있다. 그렇기 때문에 이를 제1 정규화하여 분해할 수 있다. 제1 정규화를 진행한 테이블은 아래와 같다.
    
    Clients
    
    | client | hobby |
    | --- | --- |
    | 박지성 | 독서 |
    | 박지성 | 위닝 |
    | 손흥민 | 독서 |
    | 손흥민 | 영화감상 |
    | 차범근 | 골프 |
    | 황의조 | 골프 |
    | 황의조 | 영호감상 |
    
  ## **[ 제2 정규화 ]**
    
    제2 정규화란 제1 정규화를 진행한 테이블에 대해 완전 함수 종속을 만족하도록 테이블을 분해하는 것이다. 
    
    여기서 완전 함수 종속이라는 것은 기본키의 부분집합이 결정자가 되어선 안된다는 것을 의미한다. 
    
    Courses
    
    | id | class_name | class_room | grade |
    | --- | --- | --- | --- |
    | 101 | DB | 110 | 4.3 |
    | 201 | DB | 110 | 4.5 |
    | 202 | HardWare | 220 | 3.9 |
    | 102 | Data Structure | 112 | 4.0 |
    | 101 | Data Structure | 112 | 4.1 |
    
    위 테이블에서 pk(기본키)는 (id, class_name)으로 복합키이다. 그리고 (id, class_name)인 기본키는 성적을 결정하고 있다. (id, class_name) --> (grade) 
    그런데 여기서 class_room 컬럼은 기본키의 부분집합인 class_name에 의해 결정될 수 있다. (class_name) --> (class_room)
    즉, 기본키(id, class_name)의 부분키인 class_name이 결정자이기 때문에 위의 테이블의 경우 다음과 같이 기존의 테이블에서 class_room을 분해하여 별도의 테이블로 관리하여 제2 정규형을 만족시킬 수 있다.
    
    Courses                                                               Class
    
    | id | class_name | grade |
    | --- | --- | --- |
    | 101 | DB | 4.3 |
    | 201 | DB | 4.5 |
    | 202 | HardWare | 3.9 |
    | 102 | Data Structure | 4.0 |
    | 101 | Data Structure | 4.1 |
    
    | class_name | class_room |
    | --- | --- |
    | DB | 110 |
    | HardWare | 220 |
    | Data Structure | 112 |
    
  ## **[ 제3 정규화 ]**
    
    제3 정규화란 제2 정규화를 진행한 테이블에 대해 이행적 종속을 없애도록 테이블을 분해하는 것이다. 여기서 이행적 종속이라는 것은 A -> B, B -> C가 성립할 때 A -> C가 성립되는 것을 의미한다. 
    예를 들어 아래와 같은 테이블을 살펴보자.
    
    Courses
    
    | id | class_name | tuition |
    | --- | --- | --- |
    | 101 | DB | 20,000 |
    | 201 | DB | 20,000 |
    | 202 | HardWare | 30,000 |
    | 102 | Data Structure | 40,000 |
    | 101 | Data Structure | 40,000 |
    
    기존의 테이블에서 id는 class_name을 결정하고 있고, class_name은 수강료(tuition)를 결정하고 있다. 그렇기 때문에 이를 (id, class_name) 테이블과 (class_name, tuition) 테이블로 분해해야 한다.
    
    이행적 종속을 제거하는 이유는 비교적 간단하다. 예를 들어 101인 학생이 수강하는 강좌가 HardWare로 변경되었다고 하자. 이행적 종속이 존재한다면 101의 학생은 HardWare 수업을 20,000원이라는 수강료로 듣게 된다. 물론 강의 이름에 맞게 수강료를 다시 변경할 수 있지만, 이러한 번거로움을 해결하기 위해 제3 정규화를 하는 것이다.  
    
    즉, id를 통해  class_name을 참조하고,  class_name으로 tuition를 참조하도록 테이블을 분해해야 하므로 아래 테이블 2개로 나누어진다.
    
    | id | class_name |
    | --- | --- |
    | 101 | DB |
    | 201 | DB |
    | 202 | HardWare |
    | 102 | Data Structure |
    | 101 | Data Structure |
    
    | class_name | tuition |
    | --- | --- |
    | DB | 20,000 |
    | HardWare | 30,000 |
    | Data Structure | 20,000 |
    
  ## **[ BCNF 정규화 ]**
    
    BCNF 정규화란 제3 정규화를 진행한 테이블에 대해 모든 결정자가 후보키가 되도록 테이블을 분해하는 것이다. 예를 들어 다음과 같은 Courses 테이블이 존재한다고 하자.
    
    Courses
    
    | id | class_name | teacher |
    | --- | --- | --- |
    | 101 | DB | 손흥민 |
    | 201 | DB | 손흥민 |
    | 202 | HardWare | 차번근 |
    | 102 | DataStructure | 박지성 |
    | 101 | Java | 황의조 |
    
    Courses 테이블에서 기본키는 (id, class_name)이다. 그리고 기본키 (id, class_name)는 teacher를 결정하고 있다. 또한 여기서 teacher는 class_name을 결정하고 있다. 
    그런데 문제는 teacher가 class_name을 결정하는 결정자이지만, 후보키가 아니라는 점이다. 그렇기 때문에 BCNF 정규화를 만족시키기 위해서 위의 테이블을 분해해야 하는데, 다음과 같이 courses 테이블과 teacher 테이블로 분해할 수 있다.
    
    | id | teacher |
    | --- | --- |
    | 101 | 손흥민 |
    | 201 | 손흥민 |
    | 202 | 차번근 |
    | 102 | 박지성 |
    | 101 | 황의조 |
    
    | class_name | teacher |
    | --- | --- |
    | DB | 손흥민 |
    | HardWare | 차번근 |
    | DataStructure | 박지성 |
    | Java | 황의조 |