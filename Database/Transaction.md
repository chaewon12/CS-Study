# 🔁 트랜잭션(Transaction)
> ### 트랜잭션이란?
**더 이상 쪼개질 수 없는 업무처리의 단위.
즉, 한번에 완전히 수행 완료 되어야할 데이터베이스 연산의 모음이다. 여기서 데이터베이스를 대상으로 수행하는 연산은 CRUD(Create, Read, Update, Delete)를 가리킨다.
트랜잭션은 원자적, 일관적, 고립적, 지속적인 성질을 가지며, 주로 데이터베이스에서 데이터 일관성을 유지하고 데이터 손실을 방지하는 데 사용된다.**

### 🛍️ 상점에서 물건 구매 흐름을 생각해보자!
만약 여러 구매자가 동시에 같은 상품을 주문하고 재고를 업데이트하지 않는 트랜잭션을 사용한다면, 재고 수량이 정확하지 않을 수 있다. 트랜잭션을 사용하여 재고가 감소되는 동시에 여러 고객 주문 사이에서 상품 수량에 일관성을 유지할 수 있다.
![](https://velog.velcdn.com/images/nyoung215/post/93a646b5-0c61-4d8a-9303-0117b8417c7e/image.jpeg)
> **주문 생성(Create), 재고 확인(Read), 주문 처리(Update), 재고 업데이트(Update) 까지가 하나의 트랜잭션이 되어야 한다.**

<br></br>
## 트랜잭션의 특징(ACID)
***
- ### 원자성(Atomicity)
    - 트랜잭션의 연산은 데이터베이스에 모두 반영되든지 아니면 전형 반영되지 않아야 한다.
    - 트랜잭션 내의 모든 명령은 반드시 완벽히 수행되어야 하며, 어느 하나라도 오류가 발생하면 모든 작업이 롤백된다.
  > - #### 원자성은 어떻게 보장될까?
  원자성은 트랜잭션의 수행 내역을 로그에 기록하여 보장된다.
  트랜잭션이 성공적으로 완료된 경우 로그를 커밋하고, 오류가 발생한 경우 로그를 롤백하여 원자성을 유지한다.

  | 용어                            | 설명                                                                      |
      | --------------------------------- | --------------------------------------------------------------------------- |
  | **롤백 세그먼트 (Rollback Segment)** | 이전 데이터들이 임시로 저장되는 데이터베이스 공간                                    |
  | **세이브 포인트 (Save Point)**      | 확실한 부분에 대해서는 롤백이 되지 않도록 지정하는 중간 저장 지점 (트랜잭션의 길이가 길어지게 될 경우 롤백 과정 역시 길어지기 때문) |

![](https://velog.velcdn.com/images/nyoung215/post/f7ff87c4-8e34-402a-a961-3c588baa13d5/image.png)


- ### 일관성(Comsistency)
    - 트랜잭션 실행 후에 데이터베이스가 언제나 일관된 상태로 변환되어야 하고, 실행 전과 후가 일관성 있는 제약 조건을 유지해야 한다.
    - 트랜잭션이 수행된 후에도 데이터의 무결성과 규칙 준수를 보장해야 한다.

  > - #### 일관성은 어떻게 보장될까?
  일관성은 데이터베이스 내의 무결성 규칙을 준수하고, 데이터베이스의 제약 조건을 지키며, 트리거와 같은 메커니즘을 사용하여 보장된다.
  (ex. Movie, Video 테이블이 있을 때 Video 테이블의 PK가 외래 키로 존재한다면 Movie 테이블의 제약 조건이 바뀌면 Video 테이블에서도 PK가 변경되어야 함.)

  | 용어       | 설명                                                                      |
      | ----------- | --------------------------------------------------------------------------- |
  | **트리거 (Trigger)** | 데이터베이스 시스템이 자동적으로 수행할 동작을 명시할 때 사용, 어떤 행위의 시작을 알리는 것 |


- ### 독립성(Isolation)
    - 둘 이상의 트랜잭션이 동시에 병행 실행되는 경우 어느 하나의 트랜잭션 실행중에 다른 트랜잭션의 연산이 끼어들 수 없다.
    - 수행중인 트랜잭션은 완전히 완료될 때까지 다른 트랜잭션에서 수행 결과를 참조할 수 없다.

  > - #### 독립성은 어떻게 보장될까?
  독립성은 데이터베이스에서 사용하는 락(lock) 메커니즘과 트랜잭션 격리 수준(isolation level)을 통해 보장됩니다.
  단, lock과 unlock을 잘못 사용하면 데드락(deadlock) 상태가 되어 모든 트랜잭션이 아무 것도 수행할 수 없는 상태가 될 수 있음

  | 용어               | 설명                                                |
      | ------------------- | --------------------------------------------------- |
  | **공유 잠금 (shared_lock)** | 트랜잭션에서 데이터를 읽을 때, 여러 트랜잭션이 읽을 수는 있도록 허용 (읽기 전용) |
  | **배타적 잠금 (exclusive_lock)** | 트랜잭션에서 데이터를 변경할 때(쓰기 등), 해당 트랜잭션이 완료될 때까지 다른 트랜잭션이 읽을 수도 쓸 수도 없도록 함


- ### 지속성(Durability)
    -  트랜잭션이 완료되면 그 결과는 반드시 지속 가능해야 하며, 시스템에 어떠한 문제가 생기더라도 복구가 가능하도록 해야 한다.
  > - #### 지속성은 어떻게 보장될까?
  지속성은 데이터베이스 시스템이 트랜잭션 결과를 안정적으로 저장하고, 필요한 경우 데이터베이스의 로그를 사용하여 복구(recovery)를 수행함으로써 보장된다.

<br></br>
## 트랜잭션의 상태
***
<p align="center">
  <img src="https://velog.velcdn.com/images/nyoung215/post/3a792bf4-5d05-48ca-95b1-01150b9b8e34/image.png"
 style="max-width: 80%; height: auto;">
</p>


- ### 활동 (Active)
트랜잭션의 연산들을 수행 중인 상태
- ### 부분 완료 (Partially Committed)
트랜잭션의 마지막 연산까지 완료되었고, Commit 연산을 수행하기 직전인 상태
- ### 실패 (Failed)
트랜잭션의 연산 수행이 오류 발생으로 인해 중단된 상태
- ### 철회 (Aborted)
트랜잭션이 비정상적으로 종료되어 롤백 연산을 수행한 상태
- ### 완료 (Committed)
트랜잭션의 수행이 완료되어 Commit 연산을 수행한 상태
> **커밋(commit)** : 트랜잭션 내에서 수행된 모든 작업이 성공적으로 끝났을 때, 트랜잭션의 결과를 데이터베이스에 영구적으로 저장하는 과정.
**롤백(rollback)** : 트랜잭션 도중에 오류가 발생하거나 트랜잭션의 일부를 취소해야 할 때 트랜잭션을 이전 상태로 되돌리는 과정.

<br></br>
## 트랜잭션 격리 수준 (Isolation Level)
___

```
트랜잭션의 독립성(Isolation)은 "실행 중인 트랜잭션의 중간결과를 다른 트랜잭션이 접근할 수 없다." 라는 정의를 가지고 있었다.
그러나 막연하게 접근할 수 없다기 보다는 일반적으로 접근 레벨이 있으며 DB에 따라 설정이 가능하다.
```

> ### Isolation Level 이란?
동시에 여러 트랜잭션이 처리될 때, 특정 트랜잭션이 다른 트랜잭션에서 변경하거나 조회하는 데이터를 볼 수 있도록 허용할지 말지를 결정하는 수준. Isolation Level 조정은 동시성이 증가되는데 반해 데이터 무결성에 문제가 발생할 수 있고, 데이터의 무결성을 유지하는 데 반해 동시성 떨어질 수 있다.
일반적인 온라인 서비스에서는 READ COMMITTED나 REPEATABLE READ 중 하나를 사용한다.

![](https://velog.velcdn.com/images/nyoung215/post/fd6f8ea0-3c2a-4558-90f2-8bac49599b8e/image.png)

```
Dirty Read : 아직 커밋(Commit)되지 않은 다른 트랜잭션의 데이터를 읽는 것.

Non-repeatable Read : 다른 트랜잭션이 커밋(Commit)한 데이터를 읽을 수 있는 것.
					  한 트랜잭션에서 같은 쿼리로 2번이상 조회했을 때 결과가 다른 상황.
					  (데이터의 수정/삭제가 발생했을 경우 발생)

Phantom Read : 다른 트랜잭션이 커밋(Commit)한 데이터가 있더라도 자신의 트랜잭션에서 읽었던 내용만 사용하는 것.
			   한 트랜잭션에서 같은 쿼리를 2번이상 조회했을 때 없던 결과가 조회되는 상황.
			   (데이터의 삽입이 발생했을 경우 발생)
```

- ### Isolation Level 의 필요성
  격리수준은 트랜잭션의 ACID 특성을 보장하기 위해서 사용한다.
  Locking을 통해 이를 해결할 수 있지만, 무조건적인 Locking은 성능저하를 가져올 수 있다.
  반대로 느슨한 Locking은 데이터 무결성에 큰 문제를 가져온다다. 효율적인 Locking의 사용을 위해 적절한 격리수준은 반드시 필요하다.


- ### Isolation Level 의 종류
    - **Read Uncommitted (레벨 0) : SELECT 문장이 수행되는 동안 해당 데이터에 Shared Lock이 걸리지 않는 계층**
        - RDBMS 표준에서는 격리수준으로 인정하지 않는 레벨.
        - 각 트랜잭션에서의 변경 내용이 COMMIT이나 ROLLBACK 여부에 상관 없이 다른 트랜잭션에서 값을 읽을 수 있다.
        - 아래의 그림과 같이 Commit이 되지 않는 상태지만 Update된 값을 다른 트랜잭션에서 읽을 수 있다.

  <p align="center">
    <img src="https://velog.velcdn.com/images/nyoung215/post/6221190e-d064-4f95-8201-7d0ffbbde458/image.png" style="max-width: 60%; height: auto;">
  </p>

    - **Read Committed (레벨 1) : SELECT 문장이 수행되는 동안 해당 데이터에 Shared Lock이 걸리는 계층**
        - 대부분의 RDBMS에서 기본적으로 사용하는 격리수준.
        - 각 읽기 연산은 최신의 데이터를 보장하지만, 두 번의 동일한 읽기 연산 사이에 다른 트랜잭션에 의한 변경이 발생할 수 있다.
        - 다른 트랜잭션에서 변경 중인 데이터를 읽을 수 없어서, Dirty Read와 같은 현상은 발생하지 않는다.

  <p align="center">
    <img src="https://velog.velcdn.com/images/nyoung215/post/a3d2f381-2ba4-469b-bffa-148053c0c4c6/image.png" style="max-width: 60%; height: auto;">
  </p>

    - **Repeatable Read (레벨 2) : 트랜잭션이 완료될 때까지 SELECT 문장이 사용하는 모든 데이터에 Shared Lock이 걸리는 계층**
        - 동일한 쿼리를 실행하면 결과가 항상 동일하다다. 즉, 트랜잭션이 시작할 때 읽은 데이터는 트랜잭션이 종료할 때까지 변경되지 않는다. (다른 트랜잭션에서 쓰기 연산이 일어나도 읽은 데이터는 변경되지 않는다.)
        - 따라서, 다른 사용자는 그 영역에 해당되는 데이터 대한 수정이 불가능하다.

  <p align="center">
    <img src="https://velog.velcdn.com/images/nyoung215/post/228c2d25-8136-41b7-bc87-5fd371073524/image.png" style="max-width: 60%; height: auto;">
  </p>

    - **Serializable (레벨 3) : 트랜잭션이 완료될 때까지 SELECT 문장이 사용하는 모든 데이터에 Shared Lock이 걸리는 계층**
        - 트랜잭션 간 완전한 격리를 제공하여 동시성 문제를 최소화한다.
        - 한 트랜잭션에서 변경하는 동안 다른 트랜잭션에서 해당 데이터에 수정 및 입력이 불가능하다.

  <p align="center">
    <img src="https://velog.velcdn.com/images/nyoung215/post/01c5b8a3-554a-4d5a-b9b2-355dbaabecd6/image.png" style="max-width: 60%; height: auto;">
  </p>



___
**레퍼런스**
https://it-eldorado.tistory.com/65
https://d2.naver.com/helloworld/407507
https://jeong-pro.tistory.com/94
https://nesoy.github.io/articles/2019-05/Database-Transaction-isolation
https://akasai.space/db/about_isolation/