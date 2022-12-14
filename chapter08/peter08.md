# 8. 애그리거트 트랜잭션 관리 
## 8.1 애그리거트와 트랜잭션
한 주문 애그리거트에서 운영자,고객이 동시에 같은 주문 애그리거트 수정하면, 애그리거트 일관성이 깨지는 일이 생길 수 있다. 
일관성이 깨지는 문제가 발생하지 않도록 하려면 
- 운영자가 배송지 정보를 조회하고 상태를 변경하는 동안, 고객이 애그리거트 수정하지 못하게 막는다. 
- 운영자가 배송지 정보를 조회한 이후에 고객이 정보를 변경하면, 운영자가 애그리거트를 다시 조회한 뒤 수정하도록 한다. 
DBMS가 지원하는 트랜잭션과 함께 애그리거트를 위한 추가적인 트랜잭션 처리 기법이 필요하다. 
- 선점 잠금 
- 비선점 잠금

## 8.2 선점 잠금 
선점 잠금은 먼저 애그리거트를 구한 스레드가 애그리거트 사용이 끝날 때까지 다른 스레드가 해당 애그리거트를 수정하지 못하게 막는 방식이다. 
스레드 1이 선점 잠금방식으로 애그리거트를 구한 뒤 이어서 스레드2가 같은 애그리거트를 구할 때, 
스레드2는 스레드1이 애그리거트에 대한 잠금을 해제할 때까지 블로킹된다. 
스레드1이 애그리거트를 수정하고 트랜잭션을 커밋하면 잠금을 해제한다. 이순간 대기하고 있던 스레드2가 애그리거트에 접근하게 된다. 
스레드1이 트랜잭션을 커밋한 뒤에 스레드2가 애그리거트를 구하게 되므로 스레드2는 스레드1이 수정한 애그리거트의 내용을 보게된다. 
DBMS가 제공하는 행단위 잠금을 사용해서 구현한다. for update와 같은 쿼리를 사용해 특정 레코드에 한 커넥션만 접근할 수 있는 잠금장치를 제공한다. 
JPA EntityManager 는 LockModeType을 인자로 받는 find() 메서드를 제공한다. 
LockModeType.PESSIMISTIC_WRITE를 값으로 전달하면 해당 엔티티와 매핑된 테이블을 이용해서 선점 잠금 방식을 적용할 수 있다. 
Spring Data JPA는 @Lock 애너테이션을 사용해서 잠금모드를 지정한다. 

### 8.2.1 선점 잠금과 교착 상태 
선점 잠금 기능을 사용할 때는 잠금 순서에 따른 교착 상태가 발생하지 않도록 주의해야 한다. 
잠금을 구할 때 최대 대기 시간을 지정해야 한다. JPA는 javax.persistence.lock.timeout 힌트는 잠금을 구하는 대기 시간을 밀리초 단위로 지정한다. DBMS에 따라 힌트가 적용되지 않을 수 있어 사용전 사용하는 DBMS의 관련기능 제공 여부를 확인한다. 
Spring Data JPA 는 @QueryHints 애너테이션을 사용해서 쿼리 힌트를 지정할 수 있다. 

## 8.3 비선점 잠금 
비선점 잠금은 동시에 접근하는 것을 막는 대신 변경한 데이터를 실제 DBMS에 반영하는 시점에 변경 가능 여부를 확인하는 방식이다. 
비선점 잠금 구현은 애그리거트에 버전으로 사용할 숫자 타입 프로퍼티를 추가해야한다. 
애그리거트와 매핑되는 테이블의 버전 값이 현재 애그리거트의 버전과 동일한 경우에만 데이터를 수정한다. 그리고 수정에 성공하면 버전 값을 1 증가한다. 
JPA는 버전을 이용한 비선점 잠금 기능을 지원한다. 
@Version 애너테이션을 붙이고 매핑되는 테이블에 버전을 저장할 칼럼을 추가하면 된다. 
비선점 잠금 방식을 여러 트랜잭션으로 확장하려면 애그리거트 정보를 뷰로 보여줄 때 버전 정보도 함께 사용자 화면에 전달해야 한다. 
관련 Exception 처리 
- VersionConflictException 은 이미 누군가가 애그리거트를 수정했다는 것을 의미 
- OptimisticLockingFailureException은 누군가가 거의 동시에 애그리거트를 수정했다는 것을 의미 
버전 충동 상황에 대한 구분이 명시적으로 필요 없다면 응용서비스에서 프레임워크용 익셉션을 발생시키는 것도 고려할 수 있다. 

### 8.3.1 강제 버전 증가 
애그리거트에 애그리거트 루트 외에 다른 엔티티가 존재할 때 
루트가 아닌 다른 엔티티의 값만 변경 되면 이 경우 JPA는 루트 엔티티의 버전 값을 증가시키지 않는다. 
애그리거트의 구성요소 중 일부 값이 바뀌면 논리적으로 그 애그리거트는 바뀐것이다. 
따라서 애그리거트 내에 어떤 구성요소의 상태가 바뀌면 루트 애그리거트의 버전값이 증가해야 비선점 잠금이 올바르게 동작한다. 
JPA 에서는 Entity#find() 메서드로 엔티티를 구할 때 강제로 버전 값을 증가시키는 잠금 모드를 지원한다. 
LockModeType.OPTIMISTIC_FORCE_INCREMENT 를 사용하면 해당 엔티티의 상태가 변경되었는지에 상관없이 트랜잭션 종료 시점에 버전 값을 증가 처리 한다. 

## 8.4 오프라인 선점 잠금 
오프라인 선점 잠금은 여러 트랜잭션에 걸쳐 동시 변경을 막는다. 
첫 번째 트랜잭션을 시작할 때 오프라인 잠금을 선점하고, 마지막 트랜잭션에서 잠금을 해제한다. 
잠금을 해제하기 전까지 다른 사용자는 잠금을 구할 수 없다. 
선점 사용자가 수정 요청을 수행하지 않고 프로그램을 종료하면 다른 사용자는 영원히 잠금을 구할 수 없는 상황이 올 수도 있다. 
이를 방지하기 위해 오프라인 선점 방식은 잠금 유효 시간을 가져야한다. 
잠금 유효 시간은 일정 주기로 유효 시간을 증가시키는 방식이 필요하다. 

### 8.4.1 오프라인 선점 잠금을 위한 LockManager 인터페이스와 관련 클래스 
오프라인 선점 잠금은 크게 잠금 선점 시도, 잠금 확인, 잠금 해제, 잠금 유효 시간 연장의 네 가지 기능이 필요하다. 
LockManager#TryLock() 을 이용해서 잠금을 시도 한다. 
잠금을 선점하는데 실패하면 LockException을 발생시킨다. 
LockManager#CheckLock() 을 실행하여 잠금이 유효한지 확인한다. 
- 잠금 유효 시간이 지낚으면 이미 다른 사용자가 잠금을 선점한다. 
- 잠금을 선점하지 않은 사용자가 기능을 실행했다면 기능 실행을 막아야 한다. 


### 8.4.2 DB를 이용한 LockManager 구현 
잠금 정보를 저장하기 위한 테이블을 생성한다. ex) locks
```sql
create table locks(
	`type` varchar(255),
	id varchar(255),
	lockid varchar(255),
	expiration_time datetime,
	primary key (`type`,id)

) character set utf8;

create unique index locks_idx ON locks (lockid);
```




