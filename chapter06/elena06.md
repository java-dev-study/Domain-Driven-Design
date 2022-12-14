## 응용 서비스와 표현 영역

**1. 표현 영역과 응용 영역**
- 도메인이 제 기능을 하려면 사용자와 도메인을 연결해 주는 매개체가 필요 → `응용 역역`과 `표현 영역`
- `응용 서비스`의 메서드가 요구하는 파라미터와 `표현 영역`이 사용자로부터 잔달받은 데이터의\
형식이 일치하지 않으면 `표현 영역`은 `응용 서비스`가 요구하는 형식으로 사용자 요청을 반환함
- `응용 서비스`를 실행한 뒤 `표현 영역`은 실행 결과를 사용자에게 알맞은 형식으로 응답

`표현 영역`
- 사용자의 요청을 해석 : ID/PW 입력 후 전송 시 요청을 표현 영역에 전달
- URL, 요청 파라미터, 쿠키, 헤더 등을 이용해 사용자가 원하는 기능 판별 & 응용 서비스 실행
- 사용자와 상호작용을 처리 → `응용 서비스`는 `표현 영역`에 의존하지 않음

`응용 영역`
- 실제 기능을 제공하는 것
- 기능을 실행하는 데 필요한 입력 값을 메서드 인자로 받고 실행 결과를 리턴
- 단지 기능 실행에 필요한 입력 값을 받고 실행 결과만 리턴

---

**2. 응용 서비스의 역할**
- 사용자(클라이어트)가 요청한 기능을 실행
- 사용자의 요청을 처리하기 위해 `리포지터리`에서 `도메인 객체`를 가져와 사용
- `표현 영역`의 입장에서 보았을 때 응용 서비스는 `도메인 영역``과 표현 영역`을 연결해 주는 창구 역할
- 주로 도메인 객체 간 흐름을 제어 → 단순한 형태
- `응용 서비스`가 복잡하다면 → 도메인 로직의 일부를 구현하고 있을 가능성 ↑\
→ 코드 중복, 로직 분산 등 코드 품질에 안 좋은 영향일 미칠 수 있음
- 트랜잭션 처리도 담당
- 접근 제어, 이벤트 처리

1) 도메인 로직 넣지 않기
- `도메인 로직`을 `도메인 영역`과 `응용 서비스`에 분산해서 구현하면 코드 품질에 문제 발생
> 1. 코드의 응집성 ↓
> 2. 여러 응용 서비스에서 동일한 도메인 로직을 구현할 가능성 ↑
> → 코드 변경을 어렵게 만듦 == S/W의 가치 ↓

---

**3. 응용 서비스의 구현**
- `표현 영역`과 `도메인 영역`을 연결하는 매개체 역할 → `파사드`와 같은 역할

1) 응용 서비스의 크기
- 보통 다음 두 가지 방법 중 한 가지 방식으로 구현
> 1. 한 응용 서비스 클래스에 회원 도메인의 모든 기능 구현하기
> 2. 구분되는 기능별로 응용 서비스 클래스를 따로 구현하기

`한 응용 서비스 클래스에 구현`
- 한 도메인과 관련된 기능을 구현한 코드가 한 클래스에 위치
- 각 기능에서 동일 로직에 대한 코드 중복을 제거할 수 있는 것이 장점
- 한 서비스 클래스의 크기(코드 줄 수)가 커진다는 것이 단점
- 코드 크기가 커지면 연관성이 적은 코드가 한 클래스에 함께 위치할 가능성 ↑\
→ 관련 없는 코드가 뒤섞여 코드를 이해하는 데 방해
- 코드가 모이기 시작하면 엄연히 분리하는 것이 좋은 상왕임에도 습관적으로 기존에 존재하는 클래스에 억지로 끼워 넣게 됨\
→ 코드를 점점 얽히게 만들어 코드 품질 ↓

`구분되는 기능별로 구현`
- 한 `응용 서비스` 클래스에서 1~3개의 기능 구현
- 클래스의 개수 多
- 한 클래스에 관련 기능을 모두 구현하는 것과 비교해서 코드 품질을 일정 수준으로 유지 가능
- 각 클래스별로 필요한 의존 객체만 포함하므로 다른 기능을 구현한 코드에 영향 받지 X

　
2) 응용 서비스의 인터페이스와 클래스
- 구현 클래스가 여러 개인 경우 인터페이스 필요
- 구현 클래스가 다수 존재하거나 런타임에 구현 객체를 교체해야 할 때 인터페이스 사용
- 그러나 인터페이스가 명확하게 필요하기 전까지는 `응용 서비스`에 대한 인터페이스를 작성하는 것은 비효율적
- `표현 영역`이 아닌 `도메인 영역`이나 `응용 영역`의 개발을 먼저 시작하면 `응용 서비스 클래스가 먼저 만들어짐\
→ `표현 영역`의 단위 테스트를 위해 응용 서비스 클래스의 가짜 객체가 필요한데 이를 위해 인터페이스를 추가할 수도 있음\
→ 그러나 Mockito와 같은 테스트 도구는 클래스에 대해서도 테스트용 대역 객체를 만들 수 있기 때문에 인터페이스 필요 X

　
3) 메서드 파라미터와 값 리턴
- `응용 서비스`가 제공하는 메서드는 도메인을 이용해서 사용자가 요구한 기능을 실행하는 데 필요한 값을 파라미터로 전달받아야 함
- 각 값을 개별 파라미터로 전달받을 수도 있고 값 전달을 위해 별도 데이터 클래스를 만들어 전달받을 수도 있음
- 웹 프레임워크 : 웹 요청 파라미터를 자바 객체로 변환하는 기능 제공\
→ `응용 서비스`에서 데이터로 전달할 요청 파라미터가 두 개 이상 존재하면 데이터 전달을 위한 별도 클래스를 사용하는 것이 편리
- `식별자` : 결과 데이터가 필요한 대표적인 예\
→ 결과를 모델에 담아 뷰 코드에서 링크 생성 / `응용 서비스`에서 애그리거트 객체를 그대로 리턴
- `응용 서비스`에서 애그리거트 자체를 리턴하면 도메인의 로직 실행을 `응용 서비스`와 `표현 영역` 두 곳에서 할 수 있게 됨\
→ 코드의 응집도 ↓
- `응용 서비스`는 `표현 영역`에서 필요한 데이터만 리턴하는 것이 기능 실행 로직의 응집도를 높이는 확실한 방법

　
4) 표현 영역에 의존하지 않기
- `응용 서비스`의 파라미터 타입을 결정할 때 `표현 영역`과 관련된 타입을 사용하면 안 됨\
→ ex) `HttpServletRequest`나 `HttpSession`을 응용 서비스에 파라미터로 전달하면 안 됨
- `응용 서비스`에서 `표현 영역`에 대한 의존이 발생하면 `응용 서비스`만 단독으로 테스트하기 어려워짐
- `표현 영역`의 구현이 변경되면 `응용 서비스`의 구현도 함께 변경해야 하는 문제 발생

→ `응용 서비스`가 `표현 영역`의 역할까지 대신하는 상황이 벌어질 수도 있음

- 해당 문제를 예방하는 방법은 서비스 메서드의 파라미터와 리턴 타입으로 `표현 영역`의 구현 기술을 사용하지 않는 것

　
5) 트랜잭션 처리
- 스프링과 같은 프레임워크가 제공하는 트랜잭션 관리 기능을 이용하면 쉽게 처리 가능
- 프레임워크가 제공하는 규칙을 따르면 간단한 설정만으로 트랜잿견을 시작하여 Commit하고 Exception이 발생하면 Rollback 가능
- 스프링은 `@Transaction`이 적용된 메서드가 `RuntimeException`을 발생시키면 트랜잭션 Rollback / 그렇지 않으면 Commit

---

**4. 표현 영역**
- `표현 영역`의 책임
> 1. 사용자가 시스템을 사용할 수 있는 흐름(화면)을 제공하고 제어
> 2. 사용자의 요청을 알맞은 응용 서비스에 전달하고 결과를 사용자에게 제공
> 3. 사용자의 세션을 관리

`1. 흐름(화면) 제공 및 제어`
- 웹 서비스의 `표현 영역` : 사용자가 요청한 내용을 응답으로 제공
- 응답 : 다음 화면으로 이동할 수 있는 링크 or 데이터를 입력하는 데 필요한 폼 등

`2. 알맞은 응용 서비스에 전달 및 결과 제공`
- 사용자의 요청 데이터를 `응용 서비스`가 요구하는 형식으로 변환
- `응용 서비스`의 결과를 사용자에게 응답할 수 있는 형식으로 변환
- MVC 프레임워크 : HTTP 요청 파라미터로부터 자바 객체를 생성하는 기능 지원

`3. 세션 관리`
- 웹은 쿠키나 서버 세션을 이용해서 사용자의 연결 상태를 관리
- 권한 검사와도 연결
---

**5. 값 검증**
- `표현 영역`과 `응용 서비스` 두 곳에서 모두 수행 가능
- 원칙적으로는 모든 값에 대한 검증은 `응용 서비스`에서 처리
- `표현 영역`은 잘못된 값이 존재하면 이를 사용자에게 알려주고 값을 다시 입력받아야 함\
→ 스프링 MVC : 잘못된 입력값이 있을 경우 `Errors`나 `BindingResult` 사용

`응용 서비스`
- `응용 서비스`에서 각 값이 유효한지 확인할 목적으로 Exception을 사용할 때는 사용자에게 좋지 않은 경험을 제공
- 값을 검사하는 시점에 첫 번째 값이 올바르지 않아 Exception을 발생시키면 나머지 항목에 대해서는 값을 검사하지 않음\
→ 사용자는 첫 번째 값에 대한 에러 메시지만 보게 되고 나머지 항목에 대해서는 값이 올바른지 알 수 X\
→ 이 불편을 해소하기 위해 에러 코드를 모아 하나의 Exception으로 발생시키는 방법도 있음
- 코드가 늘어나는 불편함도 있으나 `응용 서비스`의 완성도가 높아지는 이점도 있음
- **데이터의 존재 유무와 같은 논리적 오류를 검증**

`표현 영역`
- `응용 서비스`가 `ValidationErrorException`을 발생시키면 Exception에서 에러 목록을 가져와 표현 영역에서 사용할 형태로 반환 처리
- `표현 영역`에서 필수 값과 값의 형식을 검사하면 `응용 서비스`는 ID 중복 여부와 같은 **논리적 오류**만 검사하면 됨\
→ `표현 영역`과 `응용 서비스`가 값 검사를 나눠서 수행
- 스프링과 같은 프레임워크는 값 검증을 위한 Validator 인터페이스를 별도로 제공
- 이 인터페이스를 구현한 검증기를 따로 구현하면 코드를 간결하게 줄일 수 있음
- **필수 값, 값의 형식, 범위 등을 검증**

---

**6. 권한 검사**
- `사용자 U가 기능 F를 실행할 수 있는지` 확인하는 것
- 개발하는 시스템마다 권한의 복잡도가 상이
- 다양한 상황을 충족하기 위해 `스프링 시큐리티` 같은 프레임워크는 유연하고 확장 가능한 구조를 가짐
- 보통 다음 세 곳에서 권한 검사를 수행
> 1. 표현 영역
> 2. 응용 서비스
> 3. 도메인

`표현 영역`
- 인증된 사용자인지 아닌지 검사 : ex) 회원 정보 변경 기능
- `서블릿 필터` : 접근 제어를 하기 좋은 위치
- 권한에 대해서 동일한 방식으로 필터를 사용해서 URL별 권한 검사 가능

`응용 서비스`
- URL 만으로 접근 제어를 할 수 없는 경우 `응용 서비스`에서 메서드 단위로 권한 검사 수행
- 꼭 `응용 서비스` 코드에서 직접 해야 할 필요는 X : ex) 스프링 시큐리티는 AOP 활용하여 Annotation으로 검사

`도메인`
- 개별 도메인 객체 단위로 권한 검사를 해야 하는 경우 구현이 복잡
- 스프링 시큐리티와 같은 보안 프레임워크를 확장해서 개별 도메인 객체 수준의 권한 검사 기능을 프레임워크에 통합 가능

---

**7. 조회 전용 기능과 응용 서비스**
- 5장 : 조회 화면을 위한 조회 전용 모델과 DAO를 만드는 내용
- 서비스에서 이들 조회 전용 기능을 사용하면 서비스 코드가 단순히 조회 전용 기능을 호출하는 형태로 끝날 수 있음
- 서비스에서 수행하는 추가적인 로직 X
- 단일 쿼리만 실행하는 조회 전용 기능 → 트랜잭션 필요 X
- `응용 서비스`가 사용자 요청 기능을 실행하는 데 별다른 기여를 하지 못한다면 굳이 서비스 생성

→ 굳이 서비스를 만들 필요 없이 `표현 영역`에서 바로 조회 전용 기능 사용해도 문제 X
