---
title:  "Database transaction"
excerpt: "데이터베이스 트랜잭션"

categories:
  - Database
tags:
  - [Database, transaction]

toc: true
toc_sticky: true
 
date: 2022-05-30
last_modified_at: 2022-05-30
---
## **트랜잭션(Transaction)**

트랜잭션(Transaction)은 데이터베이스의 상태를 변환시키는 하나의 논리적 기능을 수행하기 위한 작업의 단위 또는 한꺼번에 모두 수행되어야 할 일련의 연산들을 의미한다.

### **트랜잭션의 특징**

1. 트랜잭션은 데이터베이스 시스템에서 병행 제어 및 회복 작업 시 처리되는 작업의 논리적 단위이다.
2. 사용자가 시스템에 대한 서비스 요구 시 시스템이 응답하기 위한 상태 변환 과정의 작업단위이다.
3. 하나의 트랜잭션은 Commit되거나 Rollback된다.


## **병행제어가 필요한 이유**

1. The Lost Update Problem : 두 개의 트랜잭션이 동일한 아이템에 접근하여 서로의 연산이 중첩될 때 결과적으로 올바르지 않은 값이 저장될 수 있다.
2. The Temporary Update (or Dirty Read) Problem : 한 트랜잭션이 값을 업데이트를 하다가 중간에 트랜잭션이 fail 하였다. 하지만 rollback 하기 이전에 다른 트랜잭션이 값을 읽게 되면 올바르지 않은 값을 읽는 것이다.
3. The Incorrect Summary Problem : 한 트랜잭션이 aggregate 함수(Sum, Max, Min 같은 함수)를 실행하고 있는데, 다른 트랜잭션이 이 값들 중 하나를 업데이트 하고 있을 때. aggregate 트랜잭션이 값이 업데이트 되기 이전을 사용할 때.


## **데이터베이스 병행처리 제어 목적**

1. 트랜잭션의 충돌을 방지하기 위해서 고립성을 강제하기 위함.  
2. 데이터베이스의 일관성을 보존하기 위해서  
3. read-write 또는 write-write 충돌을 막기 위해서 스케줄의 진정한 목적은 충돌 스케줄을 찾아서 직렬화 스케줄을 만드는 것이다.



## 트랜잭션의 성질(ACID)

### Atomicity(원자성)

1.  트랜잭션의 연산은 데이터베이스에 모두 반영되든지 아니면 전혀 반영되지 않아야 한다.(All-or-Nothing)
2. 트랜잭션 내의 모든 명령은 반드시 완벽히 수행되어야 하며, 모두가 완벽히 수행되지 않고 어느하나라도 오류가 발생하면 트랜잭션 전부가 취소되어야 한다.

### Consistency(일관성)

1. 트랜잭션이 그 실행을 성공적으로 완료하면 언제나 일관성 있는 데이터베이스 상태로 변환한다.
    - 트랜잭션을 수행하다가 장애가 발생하여 작업을 완료하지 못했다면, 지금까지 실행한 연산들 모두 취소하고 데이터베이스를 트랜잭션 작업 전의 상태로 되돌려 트랜잭션의 원자성을 보장.
2. **시스템이 가지고 있는 고정요소**는 트랜잭션 수행 전과 트랜잭션 수행 완료 후의 상태가 같아야 한다.

### Isolation(독립성, 격리성)

1. 둘 이상의 트랜잭션이 동시에 병행 실행되는 경우 어느 하나의 트랜잭션 실행중에 다른 트랜잭션의 연산이 끼어들 수 없다.
2. 수행중인 트랜잭션은 완전히 완료될 때까지 다른 트랜잭션에서 수행 결과를 참조할 수 없다.

  ex) 계좌 이체 진행 시 진행 전의 돈의 합과 진행 후의 돈의 합이 같아야 함.

### Durablility(영속성, 지속성)

1. 성공적으로 완료된 트랜잭션의 결과는 시스템이 고장나더라도 영구적으로 반영되어야 한다.
    - 트랜잭션이 성공적으로 완료된 후 데이터베이스에 반영한 수행 결과는 어떠한 경우에도 손실되지 않고 영구적이어야 함을 의미
    - 시스템에 장애가 발생하더라도 트랜잭션 작업 결과는 없어지지 않고 데이터베이스에 그대로 남아있어야 한다.
    - 트랜잭션의 지속성을 보장하려면 시스템에 장애가 발생했을 때 데이터베이스를 원래 상태로 복구하는 회복 기능이 필요.


## 트랜잭션 연산 및 상태

### Commit연산

- Commit 연산은 한개의 논리적 단위(트랜잭션)에 대한 작업이 성공적으로 끝났고 데이터베이스가 다시 일관된 상태에 있을 때, 이 트랜잭션이 행한 갱신 연산이 완료된 것을 트랜잭션 관리자에게 알려주는 연산이다.

![Untitled](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/679c5b65-e6e3-48e4-99ac-2a1d825bc8c6/?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20220530%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20220530T121357Z&X-Amz-Expires=86400&X-Amz-Signature=97944070b6bff67c48d26f89e0ccf25eec86c091aaeca6fd7b6f8516fa6b6578&X-Amz-SignedHeaders=host&x-id=GetObject)

### Rollback연산

1. Rollback 연산은 하나의 트랜잭션 처리가 비정상적으로 종료되어 데이터베이스의 일관성을 깨뜨렸을 때, 이 트랜잭션의 일부가 정상적으로 처리되었더라도 트랜잭션의 원자성을 구현하기 위해 이 트랜잭션이 행한 모든 연산을 취소(Undo)하는 연산이다.
2. Rollback시에는 해당 트랜잭션을 재시작하거나 폐기한다.

    ![Untitled](https://s3.us-west-2.amazonaws.com/secure.notion-static.com/9091c800-904b-491f-b54d-6cd5b49736ab/?X-Amz-Algorithm=AWS4-HMAC-SHA256&X-Amz-Content-Sha256=UNSIGNED-PAYLOAD&X-Amz-Credential=AKIAT73L2G45EIPT3X45%2F20220530%2Fus-west-2%2Fs3%2Faws4_request&X-Amz-Date=20220530T121357Z&X-Amz-Expires=86400&X-Amz-Signature=1c4ce65ca08ff19195d74b280eb8fdcfdb133bd9483e37cae00c20939402ea81&X-Amz-SignedHeaders=host&x-id=GetObject)

### 트랜잭션의 상태

![Untitled](https://zidols.notion.site/image/https%3A%2F%2Fs3-us-west-2.amazonaws.com%2Fsecure.notion-static.com%2F1559c000-77f3-48a9-b6e8-fe4c909fcbaa%2FUntitled.png?table=block&id=f6fd3c0e-650f-4129-ae93-9c9629a752c9&spaceId=370dbc5e-872d-4d9e-9f3a-f7113cda9427&width=2000&userId=&cache=v2)

**활동(Active) :** 트랜잭션이 실행중인 상태

**실패(Failed) :** 트랜잭션 실행에 오류가 발생하여 중단된 상태

**철회(Aborted) :** 트랜잭션이 비정상적으로 종료되어 Rollback 연산을 수행한 상태

**부분 완료(Partially Committed) :** 트랜잭션의 마지막 연산까지 실행했지만, Commit 연산이 실행되기 직전의 상태

**완료(Committed) :** 트랜잭션이 성공적으로 종료되어 Commit 연산을 실행한 후의 상태

종료된 상태(T**erminated) : 트랜잭션이 완전희 끝난 상태**

참고  
[https://brunch.co.kr/@skeks463/27](https://brunch.co.kr/@skeks463/27)  
[https://coding-factory.tistory.com/226](https://coding-factory.tistory.com/226)  
[https://jhkang-tech.tistory.com/103](https://jhkang-tech.tistory.com/103) 