## 도메인 모델과 바운디드 컨텍스트

**1. 도메인 모델과 경계**
- 단일 모델 생성 시도 : 처음 도메인 모델을 만들 때 빠지기 쉬운 함정
- 논리적으로 같은 존재처럼 보이지만 하위 도메인에 따라 다른 용어를 사용하는 경우도 존재
- 하위 도메인마다 사용하는 용어가 다르기 때문에 올바른 도메인 모델을 개발하려면 하위 도메인마다 모델 생성
- 각 모델은 명시적으로 구분되는 경계를 가져서 섞이지 않도록 해야 함
- `모델`은 특정한 컨텍스트(문맥) 하에서 완전한 의미를 가짐
- `바운디드 컨텍스트(Bounded Context)` : 구분되는 경계를 갖는 컨텍스트를 DDD에서 지칭하는 용어

---

**2. 바운디드 컨텍스트**
- 모델의 경계를 결정
- 한 개의 `바운디드 컨텍스트`는 논리적으로 한 개의 모델을 가짐
- 용어를 기준으로 구분
- 실제로 사용자에게 기능을 제공하는 물리적 시스템으로 도메인 모델은 이 `바운디드 컨텍스트` 안에서 도메인 구현
- 기업의 팀 조직 구조에 따라 결정되기도 함
- 용어를 명확하게 구분하지 못해 두 하위 도메인을 하나의 `바운디드 컨텍스트`에서 구현하기도 함\
→ 이처럼 여러 하위 도메인을 하나의 `바운디드 컨텍스트`에서 개발할 때 하위 도메인의 모델이 섞이지 않도록 주의해야 함
- `바운디드 컨텍스트`는 구현하는 하위 도메인에 알맞은 모델을 포함

---

**3. 바운디드 컨텍스트 구현**
- `바운디드 컨텍스트`는 도메인 기능을 사용자에게 제공하는 데 필요한 표현 영역, 응용 서비스, 인프라스트럭처 영역, DB 테이블을 모두 포함
- 모든 `바운디드 컨텍스트`를 반드시 도메인 주도로 개발할 필요 X
- `서비스-DAO 구조` : 도메인 기능이 서비스에 흩어지게 되지만 도메인 기능 자체가 단순하면 코드 유지 보수에 문제 X
- `한 바운디드 컨텍스트` → 두 방식을 혼합해서 사용 가능 `ex) CQRS 패턴`
- `각 바운디드 컨텍스트` → 서로 다른 구현 기술 사용 가능
- 사용자에게 보여지는 UI를 반드시 가지고 있어야 하는 것 X\
→ UI를 처리하는 서버를 두고 UI 서버에서 `바운디드 컨텍스트`와 통신해서 사용자 요청을 처리하기도 함\
→ 이 구조에서 UI 서버는 각 `바운디드 컨텍스트`를 위한 **파사드 역할** 수행

---

**4. 바운디드 컨텍스트 간 통합**
`예시`
> - 온라인 쇼핑 사이트에서 매출 증대를 위해 카탈로그 하위 도메인에 개인화 추천 기능 도입
> 1. 통합이 필요한 기능 : 사용자가 제품 상세 페이지를 볼 때, 보고 있는 상품과 유사한 상품 목록을 하단에 표시
> 2. 카탈로그 : 제품을 중심으로 도메인 모델 구현 / 추천 : 추천 연산을 위한 모델 구현

- `직접 통합 방법` : REST API 호출
- `간접 통합 방법` : 메세지 큐 사용 (비동기로 메세지 처리)
- 두 `바운디드 컨텍스트`를 개발하는 팀은 메시징 큐에 담을 데이터의 구조 협의 → 큐를 누가 제공하느냐에 따라 데이터 구조가 결정

---

**5. 바운디드 컨텍스트 간 관계**
- 가장 흔한 관계 : 한쪽에서 API를 제공하고 다른 한쪽에서 그 API를 호출하는 관계 `ex) REST API`\
→ 이 관계에서 API를 사용하는 `바운디드 컨텍스트`는 API를 제공하는 `바운디드 컨텍스트`에 의존
- `하류 컴포넌트`가 `상류 컴포넌트`에 의존\
→ `상류 컴포넌트` : 일종의 서비스 공급자 역할 / `하류 컴포넌트` : 그 서비스를 사용하는 고객 역할

　
`상류 컴포넌트 / 하류 컴포넌트`
- `상류 컴포넌트`는 `하류 컴포넌트`가 사용할 수 있는 통신 프로토콜을 정의하고 이를 공개 (프로토콜 버퍼, 공개 호스트 서비스)
- `상류 컴포넌트`의 서비스는 상류 `바운디드 컨텍스트`의 도메인 모델을 따름\
→ `하류 컴포넌트`는 상류 서비스의 모델이 자신의 도메인 모델에 영향을 주지 않도록 보호해 주는 **완충 지대** 생성 필요

　
`공유 커널(SHARED KERNEL)`
- 두 `바운디드 컨텍스트`가 같은 모델을 공유하는 경우도 존재\
→ 이렇게 두 팀이 공유하는 모델
- 중복을 줄여 줌
- 한 모델을 공유하기 때문에 한 팀에서 임의로 모델을 변경하면 X

　
`독립 방식(SEPARATE WAY)`
- 서로 통합하지 않는 방식
- 두 `바운디드 컨텍스트` 간에 통합하지 않으므로 서로 독립적으로 모델을 발전시킴
- 두 `바운디드 컨텍스트` 간의 통합은 수동으로 이루어짐
- 규모가 커질수록 수동 통합에는 한계가 존재

---

**6. 컨텍스트 맵**
- 전체 비즈니스를 조망할 수 있는 지도
- `바운디드 컨텍스트` 간의 관계를 표시한 것
- `바운디드 컨텍스트` 영역에 주요 애그리거트를 함께 표시하면 모델에 대한 관계가 더 명확히 드러남
- 하위 도메인과 일치하지 않는 `바운디드 컨텍스트`를 찾아 도메인에 맞게 조절
- 어떤 `바운디드 컨텍스트`에 집중할지 파악하는 데에 도움을 줌