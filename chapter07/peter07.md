# 7. 도메인 서비스
## 7.1 여러 에그리거트가 필요한 기능 
한 애그리거트로 기능을 구현 할 수 없을 때 ex) 결제 금액 계산 로직..
```
상품 애그리거트 : 구매하는 상품의 가격이 필요하다. 또한 상품에 따라 배송비가 추가되기도 한다. 
주문 애그리거트 : 상품별로 구매 개수가 필요하다. 
할인 쿠폰 애그리거트 : 쿠폰별로 지정한 할인 금액이나 비율에 따라 주문 총 금액을 할인 한다. 할인 쿠폰을 조건에 따라 중복 사용할 수 
있다거나 지정한 카테고리의 상품에만 적용할 수 있다는 제약 조건이 있다면 할인 계산이 복잡해진다. 
회원 애그리거트: 회원 등급에 따라 추가 할인이 가능하다. 
```
한 애그리거트에 넣기 애매한 도메인 기능을 억지로 특정 애그리거트에 구현 하면 애그리거트는 자신의 책임 범위를 넘어서는 기능을 구현하기 때문에 코드가 길어지고, 외부에 대한 의존이 높아지게 되고, 코드를 복잡하게 만들어 수정을 어렵게 만드는 요인이 된다. 
-> 이를 해소하는 가장 쉬운 방법 중 하나는 도메인 기능을 별도 서비스로 구현하는 것 

## 7.2 도메인 서비스 
도메인 영엑어 위치한 도메인 로직을 표현할 때 사용한다. 
- 계산 로직 : 여러 애그리거트가 필요한 계산 로직이나, 한 애그리거트에 넣기에는 다소 복잡한 계산 로직 
- 외부 시스템 연동이 필요한 로직 : 구현하기 위해 타 시스템을 사용해야 하는 도메인 로직 

### 7.2.1 계산 로직과 도메인 서비스 
도메인 서비스를 이용해서 도메인 개념을 명시적으로 드러낸다. 
응용 영역의 서비스가 응용로직을 다룬다면 도메인 서비스는 도메인 로직을 다룬다. 
도메인 서비스는 상태 없이 로직만 구현한다. (도메인 로직을 구현하는 데 필요한 상태는 다른 방법으로 전달받는다.)
- 도메인 서비스 객체를 애그리거트에 주입하지 않는다. 
도메인 서비스 객체를 파라미터로 전달한다는 것은 애그리거트가 도메인 서비스에 의존한다는 것을 의미한다. 
도메인 서비스의 기능을 실행할 때 애그리거트를 전달하기도 한다. 
도메인 서비스는 도메인 로직을 수행하지 응용로직을 수행하진 않는다. 

### 7.2.2 외부 시스템 연동과 도메인 서비스 
외부 시스템이나 타 도메인과의 연동 기능도 도메인 서비스가 될 수 있다. 
도메인 서비스는 도메인 로직 관점에서 인터페이스를 작성 하고, 역할관리 시스템과 연동한다는 관점으로 작성하지 않는다. 
생성된 인터페이스를 구현한 클래스는 인프라스트럭처 영역에 위치해 연동을 포함한 권한 검사 기능을 구현한다. 

### 7.2.3 도메인 서비스의 패키지 위치 
도메인 서비스의 위치는 다른 도메인 구성요소와 동일한 패키지에 위치한다. 
도메인 서비스의 개수가 많거나 엔티티나 밸류와 같은 다른 구성요소와 명시적으로 구분하고 싶다면 
domain 패키지 밑에 domain.modle, domain.service, domain.repository와 같이 하위 패키지를 구분하여 위치한다. 

### 7.2.4 도메인 서비스의 인터페이스와 클래스 
도메인 서비스의 로직이 고정되어 있지 않은 경우 도메인 서비스 자체를 인터페이스로 구현하고, 이를 구현한 클래스를 둘 수도 있다. 
특히 도메인 로직을 외부 시스템이나 별도 엔진을 이용해서 구현할 때 인터페이스와 클래스를 분리하게 된다. 
도메인 서비스의 구현이 특정 구현 기술에 의존하거나 외부 시스템의 API를 실행한다면 도메인 영역의 도메인 서비스는 인터페이스로 추상화 해야한다. 


