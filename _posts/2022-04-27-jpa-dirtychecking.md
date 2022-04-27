---
title:  "JPA - 변경 감지와 병합(merge)"
excerpt: "jpa 수정 로ㄱ"

categories:
  - JPA
tags:
  - [JPA, 변경감지, dirtychecking, 병합, merge]

toc: true
toc_sticky: true
 
date: 2022-04-27
last_modified_at: 2022-04-27
---
# **변경 감지와 병합(merge)**

## **준영속 엔티티**

영속성 컨텍스트가 더는 관리하지 않는 엔티티를 말한다.
아래 코드에서 itemService.saveItem(book) 에서 수정을 시도하는 Book 객체다.  → Book 객체는 이미 DB에 한번 저장되어서 식별자가 존재한다. 이렇게 임의로 만들어낸 엔티티도 기존 식별자를 가지고 있으면 준영속 엔티티로 볼 수 있다.

## **준영속 엔티티를 수정하는 2가지 방법**

1. 병합( merge ) 사용
2. 변경 감지 기능 사용

### 1. 병합

병합은 준영속 상태의 엔티티를 영속 상태로 변경할 때 사용하는 기능이다.

controller

```java
@PutMapping("/items/{itemId}/edit")
public String updateItem(@PathVariable("itemId") Long itemId
												, @ModelAttribute("form") BookForm form) {

    Book book = new Book();
    book.setId(form.getId());   // jpa가 식별할수 있는 id가 있어서 준영속 엔티티가 됨
    book.setName(form.getName());
    book.setPrice(form.getPrice());
    book.setStockQuantity(form.getStockQuantity());
    book.setAuthor(form.getAuthor());
    book.setIsbn(form.getIsbn());
    itemService.saveItem(book); //merge 보단 dirty checking (변경감지)로 구현 권장

    return "redirect:/items";
}
```

service영역

```java
@Service
@Transactional(readOnly = true)
@RequiredArgsConstructor
public class ItemService {

    private final ItemRepository itemRepository;

    @Transactional
    public void saveItem(Item item) {
        itemRepository.save(item);
    }
	//.....
}
```

respository영역

```java
@Repository
@RequiredArgsConstructor
public class ItemRepository {

    private final EntityManager em;

    public void save(Item item) {
        if (item.getId() == null) {
            em.persist(item);
        } else {
            em.merge(item);
        }
    }
}
```

**병합: 기존에 있는 엔티티**

- save() 메서드는 식별자 값이 없으면( null ) 새로운 엔티티로 판단해서 영속화(persist)하고 식별자가 있
으면 병합(merge)
- 지금처럼 준영속 상태인 상품 엔티티를 수정할 때는 id 값이 있으므로 병합 수행

상품레파지토리 save() 메서드 하나로 저장과 수정(병합)을 다 처리하고있다. 식별자(pk) 값이 없으면  새로운 Entity로 판단하여 persist()로 영속화하여 DB에 저장한다.

식별자가 없으면 이미 한번 영속화 되었던 Entity로 판단하여 merge()로 수정(병합)한다.

![변경감지]({{ site.url }}{{ site.baseurl }}/assets/images/merge.png)

**병합 동작 방식**

1. merge()를실행한다.
2. 파라미터로 넘어온 준영속 엔티티의 식별자 값으로 1차 캐시에서 엔티티를 조회한다.
    
    2-1. 만약 1차 캐시에 엔티티가 없으면 데이터베이스에서 엔티티를 조회하고, 1차 캐시에 저장한다.
    
3. 조회한 영속 엔티티( mergeMember )에 member 엔티티의 값을 채워 넣는다. (member 엔티티의 모든 값을 mergeMember에 밀어 넣는다. 이때 mergeMember의 “회원1”이라는 이름이 “회원명변경”으로 바뀐다.)
    
4. 영속 상태인 mergeMember를 반환한다.

**병합시 동작 방식을 간단히 정리**

1. 준영속 엔티티의 식별자 값으로 영속 엔티티를 조회한다.
2. 영속 엔티티의 값을 준영속 엔티티의 값으로 모두 교체한다.(병합한다.)
3. 트랜잭션 커밋 시점에 변경 감지 기능이 동작해서 데이터베이스에 UPDATE SQL이 실행

> 주의: **변경 감지** 기능을 사용하면 원하는 속성만 선택해서 변경할 수 있지만, 병합을 사용하면 모든 속성이 변경된다. 병합시 값이 없으면 null 로 업데이트 할 위험도 있다. (병합은 모든 필드를 교체한다.)
> 

여기서 사용하는 수정(병합)은 준영속 상태의 엔티티를 수정할 때 사용한다. 영속 상태의 엔티티는 변경 감 지(dirty checking)기능이 동작해서 트랜잭션을 커밋할 때 자동으로 수정되므로 별도의 수정 메서드를 호 출할 필요가 없고 그런 메서드도 없다.

실무에서는 보통 업데이트 기능이 매우 재한적이다. 그런데 병합은 모든 필드를 변경해버리고, 데이터가 없으면 null 로 업데이트 해버린다. 병합을 사용하면서 이 문제를 해결하려면, 변경 폼 화면에서 모든 데 이터를 항상 유지해야 한다. 실무에서는 보통 변경가능한 데이터만 노출하기 때문에, 병합을 사용하는 것이 오히려 번거롭다.


## **가장 좋은 해결 방법**

### **엔티티를 변경할 때는 항상 변경 감지를 사용하세요**

- 컨트롤러에서 어설프게 엔티티를 생성하지 않는다.
- 트랜잭션이 있는 서비스 계층에 식별자( id )와 변경할 데이터를 명확하게 전달한다.(파라미터 or dto)
- 트랜잭션이 있는 서비스 계층에서 영속 상태의 엔티티를 조회하고, 엔티티의 데이터를 직접 변경한다.
- 트랜잭션 커밋 시점에 변경 감지가 실행된다.

## 2. 변경감지

controller

```java
@PostMapping("/items/{itemId}/edit")
    public String updateItem(@PathVariable("itemId") Long itemId, @ModelAttribute("form") BookForm form) {
        itemService.changeBook(form);
        return "redirect:/items";
    }
```

service

```java
@Service
@Transactional(readOnly = true)
@RequiredArgsConstructor
public class ItemService {

    private final ItemRepository itemRepository;

	  @Transactional
    public void changeBook(BookForm form) {
        Book findItem = (Book)itemRepository.findOne(form.getId());
        findItem.changeBook(form);
    }
	//.....
}
```

Book.java

```java
@Entity
@DiscriminatorValue("B")
@Getter
@Setter
public class Book extends Item {

    private String author;
    private String isbn;

    //변걍감지(더티체킹) 변경
    public void changeBook(BookForm bookForm) {
        this.setName(bookForm.getName());
        this.setPrice(bookForm.getPrice());
        this.setStockQuantity(bookForm.getStockQuantity());
        this.setAuthor(bookForm.getAuthor());
        this.setIsbn(bookForm.getIsbn());
    }
}
```

## 참조
김영한님 ****실전! 스프링 부트와 JPA 활용1 - 웹 애플리케이션 개발 강의****