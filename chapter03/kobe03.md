### 애그리거트

도메인을 나눌때는 상위 수준의 모델에서 정리를 진행해야, 개념을 파악하는데 시간이 오래 걸리지 않는다.

바로 개별 객체 수준에서 모델을 바라보면, 상위 수준에서 관계를 파악하기 어렵다.

ex) 시큐어 → 국내 결제, 해외 결제, 간편 결제 0  — 시큐어 → 국민카드, 비자 카드, 카카오페이 X
백 개 이상의 테이블을 ERD로 정리하면 보기 복잡함과 같다.

상위 수준의 관계를 파악하기 어렵다는 뜻은 코드를 변경하고 확장하는것이 어렵다는 것을 뜻한다.

상위 수준의 관계를 쉽게 파악할 수 있도록 객체간의 관계를 군으로 묶은 것을 “애그리거트”라 한다.

애그리거트는 객체를 더 큰 수준의 묶음으로 관리하기 때문에 도메인을 단순한 구조로 묶어준다.
복잡도가 낮아지는 만큼 도메인을 확장하고, 변경하는 데 필요한 노력이 줄어든다.

- 하나의 애그리거트에 묶어진 객체들은 생성과 제거를 함께 해야한다.
- 애그리거트는 독립된 묶음으로, 다른 애그리거트에 영향을 끼치지 않는다.(재스퍼한테 물어보기)
- 함께 변경되는 빈도가 높은 객체는 한 애그리거트에 속할 가능성이 높다. ( 주문 정보, 배송 정보)

💡 ”A가 B를 갖는다" 라는 요구사항이 있다고 해서 이것이 애그리거트에 속한다는 것은 아니다.
( 제품과 리뷰 관계 → 제품이 생성된다고 리뷰가 생성되는 것이 아니기 때문에 같은 애그리거트 X )

처음 도메인 모델을 만들기 시작하면 큰 애그리거트로 보이는 것들이 많지만, 도메인 규칙을 이해할수록 애그리거트의 실제 크기는 준다. → 한 개의 애그리거트는 한 개의 엔티티 객체만 갖는 경우가 많다.

질문 → transact_table, transcat_web table, settlement_table, settlemet_web table → ??????



### 애그리거트 루트

- Order 엔티티는 totalAmounts를 가지고 있다.
- quantity와 price를 가지고있는 OrderLine 밸류

구매 상품 개수를 변경하면 OrderLine의 quantity를 변경, totalAmounts도 변경해야한다.  → 도메인 규칙



### 도메인 규칙과 일관성

애그리거트는 여러 객체로 구성되기 때문에, 애그리거트의 모든 객체가 정상 상태를 가져야한다.
모든 객체를 정상 상태로 하기 위한 책임을 가지는 애그리거트가 애그리거트 루트이다.

- 애그리거트에 속한 객체는 애그리거트 루트 엔티티에 직접적, 혹은 간접적으로 속하게 된다.
- 애그리거트 루트의 핵심 역할은 애그리거트의 일관성이 깨지지 않도록 하는 것이다.
- 애그리거트 루트는 배송지 변경, 상품 변경과 같은 기능을 제공하고, 애그리거트 루트인 Order가 이 기능을 구현한 메서드를 제공한다. → 애그리거트 루트의 코드 길이가 길어짐..?
- 애그리거트 외부에서 애그리거트의 속한 객체를 직접 변경하면 안 된다.
- set메서드를 public으로 X , 밸류 타입은 불변으로 구현한다.

```java
ShippingInfo shippingInfo = order.getShippingInfo();
shippingInfo.setAddress("인천 남동구"); -> 밸류(ShippingInfo가 불변이면 컴파일 에러!)
```

밸류의 값이 바뀌게 하려면 new를 이용해 새로운 밸류 타입 객체를 생성 후 변경해야한다. → 일관성 유지



### 애그리거트 루트의 기능 구현

애그리거트 루트는 애그리거트 내부의 다른 객체를 조합해서 기능을 완성한다.

- Order은 총 주문 금액을 구하기 위해서 OrderLine 목록을 사용한다.
- Member는 암호를 변경하기 위해 Password 객체에 암호가 일치한지 확인한다.

애그리거트 루트는 상태 변경을 위임하기도 한다.

```java
public class Order {  //....

private OrderLines orderLines;
...
orderLines.changeOrderLines(newLines);
// 애그리거트 루트에서 OrderLines를 이용해 상태 변경
}
```

- 외부에서 객체의 값을 바꿀 수 없게 하기 위해서는 불변 객체를 이용하거나 protected를 사용해서 외부에서 변경 할 수 없도록 지정해야한다.



### 트랜잭션 범위

트랜잭션 범위는 작을수록 좋다. DB의 예와 같이 한 트랜잭션에서는 한 개의 애그리거트만 수정해야 한다.

한 트랜잭션에서 한 애그리거트만 수정한다는 것은 애그리거트에서 다른 애그리거트를 변경하지 않는다는 것이다.

if) 한 트랜잭션에서 두 개 이상의 애그리거트를 수정하게 된다면 결합도 증가.

부득이 하게 한 트랜잭션으로 두 개 이상의 애그리거트를 수정해야한다면 애그리거트에서 다른 애그리거트를 직접 수정하지 않고, 응용 서비스에서 두 애그리거트를 수정하도록 구현한다.



### 리포지터리와 애그리거트

애그리거트는 개념 상 한 개의 도메인 모델을 표현하기 댸문에 객체의 영속성을 처리하는 리포지터리는 애그리거트 단위로 존재한다.

OrderLine은 Order에 속하는 구성요소 이기 때문에 Order 리포지토리만 생성한다.

리포지토리는 해당 애그리거트를 영속화(저장) 하고 사용하기 때문에 해당 메소드를 지원한다.

- save : 애그리거트 저장, findById : ID로 애그리거트 찾기

어떤 기술을 사용하는지에 따라서, 에그리거트의 구현도 영향을 받는데, 레거시 한 DB를 사용하는 경우에는 DB 테이블에 맞게끔 모델을 변경해야한다.

애그리거트는 개념적으로 하나이기 때문에 리포지토리는 애그리거트 전체를 저장소에 영속화해야한다.
ex) 현재 거래에 대한 테이블 transact , transact_web 테이블에 한 번에 들어가야한다.

이러한 조건을 만족시키지 않는다면, NullPointerException과 같은 문제가 발생할 수 있다.



### ID를 이용한 애그리거트 참조

한 객체가 다른 객체를 참조하는 것 처럼 애그리거트도 다른 애그리거트를 참조한다.

에그리거트 관리 주체는 애그리거트 루트이므로 애그리거트에서 다른 애그리거트를 참조한다는 것은 다른 애그리거트의 루트를 참조한다는 것과 같다.

Order과 Member 의 예를 들면

order.getOrder().getMember().getId() 와 같은 방법으로, 회원 아이디를 찾을 수 있다.

하지만 이러한 필드를 이용한 애그리거트 참조는 다음과 같은 문제를 야기할 수 있다.

- 편한 탐색 오용 - 다른 애그리거트의 상태를 그냥 변경 하고 싶어지는 유혹
- 성능에 대한 고민 - 지연로딩과 즉시 로딩
- 확장 어려움 - 시스템이 커져, 도메인이 감당해야하는 트래픽이 커졌을 경우에 도메인 별로 다른 데이터 저장소를 사용하기도 한다. → 더 이상 다른 애그리거트 루트를 참조하기 위해 단일 기술을 사용하지 않음

위와 같은 문제를 해결하기 위해서 ID를 이용해 애그리거트를 참조한다.

```java
order.getOrder().getMember().getId() -> X
Member member = memberRepository.findById(order.getOrder.getMemberId()) -> O
```

ID 참조를 이용하여 애그리거트 간 참조가 아닌 한 애그리거트 안 객체의 참조로 변경.

하지만 ID 참조를 이용하면 N+1의 문제가 생길 수 있다.
만약 장바구니 안 10개의 상품이라면 장바구니 10개 + 장바구니 정보 가져오는 1 개 → N+1

이러한 문제를 해결하기 위해서 조회 전용 쿼리를 생성하여 활용하는 방법이 있다.

애그리거트마다 서로 다른 저장소를 사용하면 한 번의 쿼리로 관련 애그리거트를 조회할 수 없다.

이때는 조회 성능을 높이기 위해 캐시를 적용하거나 조회 전용 저장소를 따로 구성한다.

(코드는 복잡해지지만, 시스템 처리량은 증가!)



### 애그리거트 간 집합 연관

애그리거트 1-N 관계와 M-N 관계.

```java
public class Category {

private Set<Product> products; // 다른 애그리거트에 대한 1-N 연관 (한 카테고리에 다수 상품)
```

위와 같은 카테고리, 상품 관계를 실제 서비스에 맞춰 생각하게 된다면
카테고리 별로 상품을 조회할때 페이지가 있기 마련인데, 처음에 모든 데이터를 조회한다면 너무 느리다.

이러한 문제 해결하기 위해서, 상품과 카테고리 예와 같이 M-N 관계, 1-N 관계 조회 쿼리를 구현하는게 효과적이다.



### 애그리거트를 팩토리로 사용하기

Product와 Store의 예시 처럼 애그리거트가 가지고 있는 데이터를 이용해서 다른 애그리거트를 생성해야 한다면 애그리거트에 팩토리 메서드를 구현하는 것을 고려해 보자.