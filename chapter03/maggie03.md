## 애그리거트
- 복잡한 도메인을 이해하고 관리 하기 쉽게 변경 하고자 사용하는 모델 
- 일괄성 관리의 용이 
- 복잡한 도메인 구조 -> 단순 구조로 변경 가능 
- 개발 시간 단축 가능 
- 독립된 객체 (타 애그리거트 미관리) 
- A가 B를 갖는다고 동일 애그리 거트가 아님. ( Ex. 제품<->리뷰 )
  
## 애그리거트 루트 
- 일괄된 객체를 유지하기 위한 애그리거트 관리 주체 
- setter 공개(public) 범위로 정하지 않기 
- 밸류 타입은 불변으로.. (1장에도 있음.)
- 기능 실행 위임 가능
- 트랜잭션 범위는 작을수록 좋음. ( 각 에그리거트는 독립적이어야함.)
- 만약, 두개 이상 트랜잭션 처리가 필요하다면 응용 영역에서 구현하여 사용하도록..

[하지말자]
- (x) setter 범위 public 으로 설정(데이터 일관성 무시) 
- (x) 응용로직에 구현 (중복 구현 및 유지보수 어려움)

## 리포지터리와 애그리거트
- 애그리거트 상태 변경 시, 모든 변경은 저장소에 반영되야함. (리포지터리 영속성)

## ID 를 이용한 애그리거트 참조
- 한 애그리거트의 속한 객체만 참조하여 관련 문제점의 대해(편한 탐색 오용, 성능 저하, 확장성 불가) 해결 가능.
- 애그리거트 별 다른 구현기술 사용 가능
- ID 참조방식 + N+1 -> 조회 전용 쿼리 사용 (JOIN) 

## 애그리거트 간 집합 연관 
- 컬렉션을 이용 
- N-1 , 1-N , M-N (JPA 에서는 어노테이션도 있는듯..)

## 애그리거트를 팩토리로 사용하기
- 팩토리 매소드 사용 
- 도메인 로직이 한곳에 위치하여 응용 서비스 영향도 없음. 
- 도메인 응집도 향상 
