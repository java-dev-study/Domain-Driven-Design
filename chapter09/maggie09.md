### 9.1 도메인 모델과 경계
- 단일모델을 만드는 것은 무리다.
- 하위 도메인마다 모델을 만들자. 어떻게 ? 바운디드 컨텍스트를 사용해서!

### 9.2 바운디드 컨텍스트
- 하위 도메인의 모델이 섞이지 않도록 유의 
- 만약, 한개의 바운디드 컨텍스트에 여러 하위 도메인을 포함하더라도 하위 도메인마다 패키지는 나눌 것 

### 9.3 바운디드 컨텍스트 구현 
- 표현영역, 응용 서비스, 도메인 모델 , 인프라스트럭처, 테이블 모두 포함.
- 즉, 도메인 기능을 제공하는데 필요한 모든 요소 포함
- 서로 다른 기술 구현 가능 ( 조회는 바로 DAO 호출 ) ->  CQRS (11장으로...)

### 9.4 바운디드 컨텍스트 통합
- 직접 통합 : REST API 
- 간접 통합 : 메시지 큐 
- 통합할 컨텍스트의 주체의 따라 데이터 구조 맞춰야 함. 

### 9.5 바운디드 컨텍스트 간 관계
- 공개 호스트 서비스 (Ex. 검색) / 모든 요구사항을 수용할 수 있는 서비스 제공
- 공유 커널 
- 독립 방식 

### 9.6 컨텍스트 맵 
- 말그대로 컨텍스트를 도형화 시킴. 관계라고 보면 됨.
