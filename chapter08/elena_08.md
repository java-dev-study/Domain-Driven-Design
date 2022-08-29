## 애그리거트 트랜잭션 관리

**1. 애그리거트와 트랜잭션**
- 운영자와 고객이 동시에 한 주문 애그리거트를 수정할 때
- 트랜잭션마다 리포지터리는 새로운 애그리거트 객체를 생성하므로\
운영자 스레드와 고객 스레드는 같은 주문 애그리거트를 나타내는 다른 객체를 구하게 됨
- 운영자 스레드와 고객 스레드는 개념적으로 동일하지만 물리적으로 서로 다른 애그리거트 객체를 사용
- 두 스레드는 각각 트랜잭션을 커밋할 때 수정한 내용을 DB에 반영
→ 애그리거트의 일관성이 깨질 수 있음

`방지하기 위한 방법`
1. 운영자가 배송지 정보를 조회하고 상태를 변경하는 동안, 고객이 애그리거트를 수정하지 못 하게 막기
2. 운영자가 배송지 정보를 조회한 이후에 고객이 정보를 변경하면, 운영자가 애그리거트를 다시 조회한 뒤 수정하도록 함

- 자체 트랜잭션과 관련이 있음
- 트랜잭션 처리 방식 : `선점 잠금`과 `비선점 잠금`

---

**2. 선점 잠금**
- Pessimistic Lock
- 먼저 애그리거트를 구한 스레드가 애그리거트 사용이 끝날 때까지\
다른 스레드가 해당 애그리거트를 수정하지 못하게 막는 방식
1. 스레드1이 선점 잠금 방식으로 애그리거트를 구한 뒤 이어서 스레드2가 같은 애그리거트를 구함
2. 스레드2는 스레드1이 애그리거트에 대한 잠금을 해제할 때까지 Blocking 됨
3. 스레드1이 애그리거트를 수정하고 트랜잭션을 커밋하면 잠금을 해제
4. 대기하고 있던 스레드2가 애그리거트에 접근
5. 스레드1이 트랜잭션을 커밋한 뒤에 스레드2가 애그리거트를 구하게 되므로 스레드2는 스레드1이 수정한 애그리거트의 내용을 보게 됨

→ 한 스레드가 애그리거트를 구하고 수정하는 동안 다른 스레드가 수정할 수 없으므로 동시에\
애그리거트를 수정할 때 발생하는 데이터 충돌 문제 해소

- 보통 DBMS가 제공하는 `행단위 잠금`을 사용해서 구현
- 오라클 : for update와 같은 쿼리를 사용해서 특정 레코드에 한 커넥션만 접근할 수 있는 잠금장치 제공
- JPA EntityManager : LockModeType을 인자로 받는 find() 메서드 제공
- 스프링 데이터 JPA : `@Lock` Annotation을 사용해서 잠금 모드를 지정
　
 
1. 선점 잠금과 교착상태
- `선점 잠금`을 사용할 때는 잠금 순서에 따른 `교착 상태`가 발생하지 않도록 주의
- `선점 잠금`에 따른 `교착 상태`는 상대적으로 사용자 수가 많을 때 발생할 가능성 ↑
- 사용자 수가 많아지면 교착 상태에 빠지는 스레드는 더빠르게 증가

`방지하기 위한 방법`
- 잠금을 구할 때 최대 대기 시간을 지정해야 함
- JPA : `선점 잠금`을 시도할 때 최대 대기 시간을 지정하려면\
↓
```java
Map<String, Object> hints = new HashMap<>();
hints.put("javax.persistence.Lock.timeout", 2000);
Order order = entityManager.find(Order.class, orderNo, LockModeType.PESSIMISTIC_WRITE, hints);
```

- `java.persistence.lock.timeout` : 잠금을 구하는 대기 시간을 밀리초 단위로 지정
- 스프링 데이터 JPA : `@QueryHints` Annotation을 사용해서 쿼리 힌트 지정 가능

---

**3. 비선점 잠금**