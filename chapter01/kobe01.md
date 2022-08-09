# 도메인 모델 시작하기

### 도메인이란?

- 상품 조회, 구매, 결제, 배송 추적등의 기능을 제공해야하는 온라인 서점은 소프트웨어로 해결하고자 하는 문제영역 즉 도메인이라 할 수 있다. - 도메인은 소프트웨어로 해결하고자 하는 문제 영역이다.

- 소프트웨어로 해결하고자 하는 문제 영역은 하위 문제 영역으로 나눌 수 있다.

       - “물건 구매” 도메인의 하위에는 주문, 결제, 배송 같은 하위 도메인이 있다.

 - 하위 도메인을 정하는 기준은 구현해야하는 프로젝트의 필요성, 사이즈에 따라 변한다. 

( 소규모 업체는 수작업으로 배송을 유지할 수 있다.)

### 1.2 도메인 전문가와 개발자

- 고객은 도메인에 대한 자신들의 지식과 경험을 바탕으로 기능 개발을 요구한다.

      -고객이 원하는 기능이 곧 요구사항이고, 도메인에 맞는 요구사항을 잘 파악하기 위해서는 개발자도 

       요구사항을 잘 이해하고 있어야한다. → 결제 도메인 프로세스(요구사항)를 파악해야함.

요구사항과 결과 → Garbage In, Garbage Out.

### 1.3 도메인 모델

- 도메인 모델이란 특정 도메인을 개념적으로 표현한 것.

-PG 플랫폼 팀 Secure API 속 국내 프로세스, 해외결제 프로세스, 간편 결제 프로세스는 어떤 모델로 그려야 좋         을까?   ( 객체 모델링?, 상태 다이어그램? ) → 가장 중요한 건 도메인을 쉽게 이해할 수 있는 모델을 사용하는 것.

하나의 도메인은(결제 프로세스) 다양한 하위 도메인(국내, 해외, 간편)으로 나뉘어야 하는데, 해당 도메인을 이해하기 위해서는 하위 도메인 모델 역시 필수적이다.

### 1.4 도메인 모델 패턴

일반적인 애플리케이션 아키텍처는 4개의 영역으로 구성된다. - 표현, 응용, 도메인 , 인프라 스트럭처 

**표현 (UI)** : 사용자 요청을 처리하고 정보를 보여준다. (Secure JSP)

**응용** : 기능 실행. 업무 로직을 직접 구현하지 않으며 도메인 계측을 조합해서 기능을 실행.

**도메인** : 시스템이 제공할(결제) 도메인 규칙을 구현한다. 

**인프라 스트럭처 :** DB나 메시징 시스템과 같은 외부 시스템과의 연동을 처리.

**객체 지향적인 도메인 모델 패턴**

```java
public enum OrderState {

PAYMENT_WAITING {
public booleanisShippingChangeable() {
return true;
        }
    },

PREPARING {
public booleanisShippingChangeable() {
return true;
        }
    },
    SHIPPED, DELIVERING, DELIVERY_COMPLETED;

public booleanisShippingChangeable() {
return false;
    }
}
```

OrderState 에서 return을 이용해 로직을 구현하던 이전과 달리

```java

private booleanisShippingChangeable()          ///상품 준비 중, 주문 대기중일때
{
return state == OrderState.PAYMENT_WAITING ||
                state == OrderState.PREPARING;
    }

}

enum OrderState
{
    PAYMENT_WAITING,PREPARING,SHIPPED,DELIVERING,DELIVERY_COMPLETED;
}
```

해당 코드에서는 Order에서 로직 구현을 진행하고 있다. 위와 같은 구현( OrderState가 아닌 Order에서 로직 구현 ) 을 이용해서 다른 코드에 영향 없이 변경 내역을 도메인 모델 (Order) 에 적용할 수 있다.

도메인 모델 → 도메인 모델이라는 용어는 도메인을 표현하는 개념적 모델을 의미하기도 하지만, 도메인 계층을 구현할 때 사용하는 객체 모델을 언급하기도 함. → (Order와 그림을 말하는지? 질문)

### 1.5 도메인 모델 도출

도메인을 모델링할 때 기본이 되는 작업은 요구사항을 토대로 모델을 구성하는 핵심 구성요소, 규칙, 기능을 찾는 것.

전체적인 기능을 확인하기 위해서는 코드를 보는 것이 아닌 상위 수준의 문서를 확인하는 것이 도움이 된다.  

도메인을 깊게 이해하기 위햇너는 도메인 관점을 잘 표현한 코드가 문서로서의 의미를 가진다.

질문 : Order 클래스 안에 있는, OrderLine 과 ShippingInfo 

### 1.6 엔티티와 밸류

- 엔티티
 
엔티티의 가장 큰 특징은 식별자를 가진다는 것이다.(Order 도메인의 주문 번호) (TID, TRANSNO)
식별자를 생성하는 방법 : 특정 규칙 사용, UUID, 일련번호(AUTO_INCREMENT)

- 밸류
 
개념적으로 완전히 하나임을 표현할때 사용한다.

```java
public classShippingInfo{

private String receiverName;                     //받는 사람
private String receiverPhoneNumber;

private String shippingAddress1;                 //주소
private String shippingAddress2;
private String shippingAddress3;
private String zipcode;

}
```

```java
public classShippingInfo
{
private Receiver receiver;
private Address address;
}
```

또한 책 예제에서 보듯, 밸류를 통해 해당 밸류에 대한 메소드를 만들 수 있다.

total = price * quantity → total = Money.calculate() → (명시적으로 작성하여 가독성 증가)

또한 밸류 객체는 안전한 코드를 작성하기 위해 데이터를 변경하는 것이 아닌 new 연산자를 이용하여 새로운 밸류 객체를 생성한다. 

```java
Money price = price.setValue(1000) X

Money price = new Money(price.getValue(1000)) O
```

- 도메인 모델에 set 메서드 넣지 않기.

코드를 작성하면서 습관적으로 set 메소드를 사용하는건 좋지 않다.

ex) ChangeShippingInfo() → setShippingInfo() 
      (배송지를 바꿈과 배송지를 설정 하는 것은 의미가  다르다.)
    
          completePayment() → setOrderState 
          (주문 완료와 주문 상태를 바꾸는 것의 의미는 다르다.)
    
    set 메서드로 데이터를 전달하도록 구현하면
    
    ```java
    Order order = new Order();   -> 처음 order을 생성할때는 Order 객체는 불완전하다.
    order.setOrderLine(lines);
    order.setShippingInfo(shippingInfo);
    
    order.setState(OrderState.PREPARING);
    ... 해당 객체의 값이 null이 될 가능성이 있다. -> 불완전하다.
    
    - 생성자를 이용해 완전한 객체를 만들어줘야한다.
    Order order = new Order(orderer, lines, shippingInfo, OrderState.PREPARING);
    setOreder~
    
    ```
    
    ﹖ 질문 사항 - 책 053 page OrderNo를 밸류로 만들 필요성 (그냥 필드 이름만 설정하면 되지 않을까)
    Money와 같은 경우는 금액을 계산하는 추가 기능이 있지만, OrderNo같은 경우는 딱히 변하지 않는데 궁금.
    
    ### 1.7 도메인 용어와 유비쿼터스 언어
    
    코드를 작성할때 개발자에게 코드의 의미를 확실하게 해주기 위해서 유비쿼터스 언어를 사용해야한다.
    
    EX) STEP1, STEP2, STEP3 → X                PAYMENT_WAITING,PREPARING,SHIPPED → 0
    
    유비쿼터스 언어를 사용하여 보다 더 직관적이고, 용어의 모호함을 줄일 수 있다.