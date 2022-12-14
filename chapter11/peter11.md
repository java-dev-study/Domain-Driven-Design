# 11 CQRS 
## 11.1 단일 모델의 단점 
여러 애그리거트에서 데이터를 가져와 출력하는 기능을 구현하기에는 고려할게 많아서 구현을 복잡하게 만드는 원인이 된다. 
구현 복잡도를 낮추는 간단한 방법은 상태 변경을 위한 모델과 조회를 위한 모델을 분리하는 것 

## 11.2 CQRS
Command Query Responsibility Segregation 
상태를 변경하는 명령을 위한 모델과 상태를 제공하는 조회를 위한 모델을 분리하는 패턴 
CQRS를 사용하면 각 모델에 맞는 구현 기술을 선택할 수 있다. 
명령 모델은 객체 지향에 기반해서 도메인 모델을 구현하기에 적당한 JPA를 사용하고, 
조회 모델은 DB 테이블에서 SQL로 데이터를 조회할 때 좋은 마이바티스를 사용해서 구현하면 된다. 
조회 모델에는 응용서비스가 존재하지 않을 수도 있다. 
명령 모델과 조회 모델이 서로 다른 DB를 사용할 수있다. (명령모델=RDBMS,조회모델=NoSQL)
서로 다른 DB를 사용하면 데이터 동기화 시점에 따라 구현 방식이 달라질 수 있다. 
명령 모델에서 바뀌자마자 변경 내역을 바로 조회 모델에 반영해야 한다면 동기 이벤트와 글로벌 트랜잭션을 사용해서 실시간으로 동기화할 수 있다. (전반적인 성능이 떨어지는 단점이 있다)

### 11.2.1 웹과 CQRS 
조회 요청이 많은 일반적인 웹 서비스는 조회 성능을 높이기 위해 다양한 기법을 사용한다. 
조회 전용 모델 캐싱, 쿼리 최적화 

### 11.2.2 CQRS 장단점 
CQRS 패턴 적용의 장점은 명령 모델을 구현할 때 도메인 자체에 집중할 수 있다는 점이다. 
조회 성능을 위한 코드가 명령 모델에 없으므로 도메인 로직을 구현하는 데 집중할 수 있고, 명령 모델에서 조회 관련 로직이 사라져 복잡도가 낮아 진다. 
또 다른 장점은 조회 성능을 향상시키는데 유리하다는 점이다. (캐시 기술 적용,조화에 특화된 쿼리 사용)

CQRS 패턴 적용의 단점은 구현해야 할 코드가 더 많다는 점이다. 단일 모델을 사용할 때 발생하는 복잡함 때문에 발생하는 구현비용과 조회 전용 모델을 만들 때 발생하는 구현 비용을 따져봐야 한다.  
또 다른 단점은 더 많은 구현 기술이 필요하다는 점이다. 

도메인이 복잡하지 않은데 CQRS를 도입하면 두 모델을 유지하는 비용만 높아지고 얻을 수 있는 이점이 없다. 
반면에 트래픽이 높은 서비스인데 단일 모델을 고집하면 유지 보수 비용이 오히려 높아질 수 있다. 





