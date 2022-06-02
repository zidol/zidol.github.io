---
title:  "Java Stream(스트림)"
excerpt: "Java, Stream"

categories:
  - java
tags:
  - [java, stream]

toc: true
toc_sticky: true
 
date: 2022-06-02
last_modified_at: 2022-06-02
---
## 스트림 이란?

- 자료의 대상과 관계없이 동일한 연산을 수행

  배열, 컬렉션을 대상으로 연산을 수행 함

  일관성 있는 연산으로 자료의 처리를 쉽고 간단하게 함

  자료 처리에 대한 추상화가 구현되었다고 함
- 한번 생성하고 사용한 스트림은 재사용 할 수 없음

  자료에 대한 스트림을 생성하여 연산을 수행하면 스트림은 소모됨

  다른 연산을 수행하기 위해서는 스트림을 다시 생성해야 함
- 스트림 연산은 기존 자료를 변경하지 않음

  자료에 대한 스트림을 생성하면 스트림이 사용하는 메모리 공간은 별도로 생성되므로 연산이 수행되도 기존 자료에 대한 변경은 발생하지 않음
- 스트림 연산은 중간 연산과 최종 연산으로 구분 됨

  스트림에 대해 중간 연산은 여러 개의 연산이 적용될 수 있지만 최종 연산은 마지막에 한 번만 적용됨

  최종연산이 호출되어야 중간 연산에 대한 수행이 이루어 지고 그 결과가 만들어짐

  따라서 중간 연산에 대한 결과를 연산 중에 알수 없음

  이를 '지연 연산'이라 함

## 스트림 생성하고 사용하기

- 정수 배열에 스트림 생성하여 연산을 수행 하는 예

```java
public class IntArrayTest {

	public static void main(String[] args) {

		int[] arr = {1,2,3,4,5};

		int sumVal = Arrays.stream(arr).sum();
		long count = Arrays.stream(arr).count();

		System.out.println(sumVal);
		System.out.println(count);
	}

}

```

## 중간 연산과 최종 연산

- 중간 연산의 예 - filter(), map(), sorted() 등  조건에 맞는 요소를 추출(filter)하거나 요소를 변환 함(map)
- 최종 연산이 호출될 때 중간 연산이 수행되고 결과가 생성 됨
- 문자열 리스트에서 문자열의 길이가 5 이상인 요소만 출력하기

```java
sList.stream().filter(s->s.length() >= 5).forEach(s->System.out.println(s));
```

map()은 중간 연산이고, forEach()는 최종 연산임

- 중간 연산과 최종 연산에 대한 구현은 람다식을 활용함
- 최종 연산의 예 - forEach(), count(), sum() 등
    
    스트림이 관리하는 자료를 하나씩 소모해가며 연산이 수행 됨
    
    최종 연산 후에 스트림은 더 이상 다른 연산을 적용할 수 없음
    
    forEach() : 요소를 하나씩 꺼내 옴
    
    count() : 요소의 개수
    
    sum() : 요소들의 합
    

## ArrayList 객체에 스트림 생성하고 사용하기

- ArrayList에 문자열 자료(이름)을 넣고 이에 대한 여러 연산을 수행해보기

```java
public class ArrayListStreamTest {

	public static void main(String[] args) {
		List<String> sList = new ArrayList<String>();
		sList.add("Tomas");
		sList.add("Edward");
		sList.add("Jack");
		
		Stream<String> stream = sList.stream();
		stream.forEach(s->System.out.print(s + " "));
		System.out.println();
		
		sList.stream().sorted().forEach(s->System.out.print(s+ " "));
		sList.stream().map(s->s.length()).forEach(n->System.out.println(n));
		sList.stream().filter(s->s.length() >= 5).forEach(s->System.out.println(s));
		
	}

}
```

- 새로운 연산을 수행하기 위해서는 기존의 스트림은 재사용할 수 없고 stream()메서드를 호출하여 스트림을 다시 생성해야 함
- 정수 자료에 대한 여러 가지 연산 예

```java
public class IntArrayStreamTest {

	public static void main(String[] args) {
		int[] arr = {1,2,3,4,5};
		Arrays.stream(arr).forEach(n->System.out.print(n + "\t"));
		System.out.println();
					
		int sum  = Arrays.stream(arr).sum();
		System.out.println("sum: " + sum);
		
		List<Integer> list = new ArrayList<Integer>();
		list.add(1);
		list.add(2);
		list.add(3);
		list.add(4);
		list.add(5);
		int sum2 = list.stream().mapToInt(n->n.intValue()).sum();
		System.out.println(sum2);
	}

}
```
## Example

```java

public class TravelCustomer {
    private String name;
    private int age;
    private int price;

    public TravelCustomer(String name, int age, int price) {
        this.name = name;
        this.age = age;
        this.price = price;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }

    public int getPrice() {
        return price;
    }

    public void setPrice(int price) {
        this.price = price;
    }

    @Override
    public String toString() {
        return "TravelCustomer{" +
                "name='" + name + '\'' +
                ", age=" + age +
                ", price=" + price +
                '}';
    }
}
```

```java

import java.util.ArrayList;
import java.util.List;

public class TravelCustomerTest {
    public static void main(String[] args) {
        TravelCustomer customerLee = new TravelCustomer("이순신", 40, 100);
        TravelCustomer customerKim = new TravelCustomer("김유신", 20, 100);
        TravelCustomer customerHong = new TravelCustomer("홍길동", 13, 50);

        List<TravelCustomer> customerList = new ArrayList<TravelCustomer>();

        customerList.add(customerLee);
        customerList.add(customerKim);
        customerList.add(customerHong);

        System.out.println("고객 명단 출력");

        customerList.stream().map(c->c.getName()).forEach(s-> System.out.println(s));

        System.out.println("여행 비용");
        System.out.println(customerList.stream().mapToInt(c -> c.getPrice()).sum());

        System.out.println("20세 이상 고객 이름 정렬");
        customerList.stream().filter(c->c.getAge() >= 20).map(c -> c.getName()).sorted().forEach(s -> System.out.println(s));
    }
}
```

```bash
고객 명단 출력
이순신
김유신
홍길동
여행 비용
250
20세 이상 고객 이름 정렬
김유신
이순신

Process finished with exit code 0
```