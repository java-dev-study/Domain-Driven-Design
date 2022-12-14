### Chapter02 아키텍처 개요

#### 2.1 네 개의 영역

- 표현
  - 사용자의 요청을 받아 응용 영역에 전달하고 응용 영역의 처리 결과를 다시 사용자에게 보여줌
- 응용
  - 시스템이 사용자에게 제공해야할 기능 구현
- 도메인
  - 도메인 모델 구현
- 인프라스트럭처
  - 구현 기술 처리

#### 2.2 계층 구조 아키텍처

- 표현 -> 응용 -> 도메인 -> 인프라스트럭처
- 계층 구조는 상위 계층에서 하위 계층으로의 의존만 존재하고 하위 계층은 상위 계층에 의존하지 않음
- 구현의 편리함을 위해 계층 구조를 유연하게 적용
- 응용 영역이 인프라스트럭처 영역에 의존할 경우, 테스트 어려움 및 기능 확장의 어려움 발생

#### 2.3 DIP

- 저수준 모듈이 고수준 모듈에 의존하도록 변경 -> **추상화한 인터페이스**
- 하위 기능을 추상화한 인터페이스는 고수준 모듈 관점에서 도출
- 실제 구현 대신 스텁이나 목 객체를 통해 테스트 가능

#### 2.4 도메인 영역의 주요 구성요소

- 엔티티
  - 고유의 식별자를 갖는 객체로, 도메인 모델의 데이터와 기능을 함께 제공함
- 밸류
  - 고유의 식별자를 갖지 않는 객체로 개념적으로 하나인 값을 표현함
  - 엔티티의 속성으로 사용할 뿐만 아니라 다른 밸류 타입의 속성으로도 사용
- 애그리거트
  - 연관된 엔티티와 밸류 객체를 개념적으로 하나로 묶은 것
- 리포지터리
  - 애그리거트 단위로 도메인 객체 저장 및 조회
- 도메인 서비스
  - 특정 엔티티에 속하지 않은 도메인 로직 제공

#### 2.5 요청 처리 흐름

1. 표현 영역은 사용자 요청 데이터를 응용 서비스에 맞게 변환해서 전달
2. 응용 서비스는 도메인 모델을 이용해서 기능 구현
3. 기능 구현에 필요한 도메인 객체를 리포지터리에서 가져와 실행하거나 신규 도메인 객체를 생성해서 리포지터리에 저장

#### 2.6 인프라스트럭처 개요

- 무조건 인프라스트럭처에 대한 의존을 없애기 보다, DIP의 장점을 해치지 않는 범위에서 응용 영역과 도메인 영역에서 구현 기술에 대한 의존을 가져가는 것도 나쁘지 않음
  - ex) `@Transactional`, `@Entity`, `@Table`

#### 2.7 모듈 구성

- 패키지 구성 규칙에 정답은 없으며, 도메인이 클 경우 하위 도메인으로 나누고 각 하위 도메인마다 별도 패키지 구성
- 도메인 모듈은 도메인에 속한 애그리거트를 기준으로 다시 패키지 구성

