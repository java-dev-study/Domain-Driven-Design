## 1.1 도메인이란?

도메인 : 소프트웨어로 해결하고자 하는 문제 영역
한 도메인은 다시 하위 도메인으로 나눌 수 있다.
특정 도메인을 위한 소프트웨어라고 해서 도메인이 제공해야 할 모든 기능을 직접 구현하는 것은 아님.

## 1.2 도메인 전문가와 개발자 간 지식 공유

요구사항을 올바르게 이해하는 것이 중요
이해관계자와 개발자도 도메인 지식을 갖춰야 함

## 1.3 도메인 모델

도메인 모델은 특정 도메인을 개념적으로 표현한 것
도메인을 이해하는 데 도움이 된다면, 표현 방식이 무엇인지는 중요하지 않음.
ex) 객체를 이용한 도메인 -> 도메인이 제공하는 기능과 도메인의 주요 데이터 구성을 파악해야하는데, 이런 면에서 기능과 데이터를 함계 보여주는 객체 모델은 도메인을 모델링하기에 적함

## 1.4 도메인 모델 패턴

도메인 모델은 아키텍처 상의 도메인 계층을 객체 지향 기법으로 구현하는 패턴을 말한다.
도메인 계층은 도메인의 핵심 규칙을 구현한다.
=> 핵심 규칙을 구현한 코드는 도메인 모델에만 위치하기 때문에 규칙이 바뀌거나 규칙을 확장해야 할 때 다른 코드에 영향을 덜 주고 변경 내역을 모델에 반영할 수 있다.


## 1.5 도메인 추출

도메인을 모델링할 때 기본이 되는 작업은 모델을 구성하는 핵심 구성요소, 규칙, 기능을 찾는 것
이 과정은 요구사항에서 출발한다.

## 1.6 엔티티와 밸류

도출한 모델은 크게 엔티티와 밸류로 구분할 수 있다.
엔티티와 밸류를 제대로 구분해야 도메인을 올바르게 설계하고 구현할 수 있기 때문에 이 둘의 차이를 명확하게 이해해야 한다.

엔티티?
엔티티의 가장 큰 특징은 식별자를 가진다는 것이다. ex) merchant_seq

엔티티 식별자를 생성하는 시점은 도메인의 특징과 사용하는 기술에 따라 달라진다.
- 특정 규칙에 따라 생성 
- UUID나 Nano ID와 같은 고유 식별자 생성기 사용
- 값을 직접 입력
- 일련번호 사용 (시퀀스나 DB의 자동 증가 컬럼 사용)

밸류타입?
밸류 타입을 사용함으로써 개념적으로 완전히 구분된 하나를 잘 표현할 수 있담, 또한 코드의 의미를 더 잘 이해할 수 있도록 한다. 
식별자는 단순한 문자열이 아니라 도메인에서 특별한 의미를 지니는 경우가 많기 때문에 식별자를 위한 밸류 타입을 사용해서 의미가 잘 드러나도록 할 수 있다.


도메인 모델에 set 메서드 넣지 않기
도메인 모델에 get/set 메서드를 무조건 추가하는 것은 좋지 않은 버릇 -> 특히 set 메서드는 도메인의 핵심 개념이나 의도를 코드에서 사라지게 하기 때문
또한 set 메서드의 또 다른 문제는 도메인 객체를 생성할 때 온전하지 않은 상태가 될 수 있다. 

## 1.7 도메인 용어와 유비쿼터스 언어

나쁜 예) step1, step2, step3.. -> 좋은 예) 결제 대기 중, 상품 준비 중, 출고 완료됨 ..
최대한 도메인 용어를 사용해서 도메인 규칙을 코드로 작성하게되면 버그도 줄어든다. 
이렇게 전문가, 관계자, 개발자가 도메인과 관련된 공통의 언어를 사용하는것을 유비쿼터스 언어라고하며, 이는 소통 과정에서 발생하는 용어의 모호함을 줄일 수 있고 개발자는 도메인과 코드 사이에서 불필요한 해석 과정을 줄일 수 있다.