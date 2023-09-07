# DB_study
DB_study


## TIL

| [노신희](https://github.com/shinhee-rebecca) | [손찬영](https://github.com/chaaaany) | [이송희](https://github.com/songhee-lee) | [조찬영](https://github.com/cotchan) |
| ------------------------------------------------------------ | ------ | ------ | ------ |
| - | [gitRepo](https://github.com/chaaaany/2022_TIL)       | [gitRepo](https://songhee-lee.github.io/)       |  [gitRepo](https://cotchan.github.io)      |



## Index

- [무결성 제약 조건](#무결성-제약-조건)

  개체 무결성 제약 조건 / 참조 무결성 제약 조건

- [트랜잭션](#트랜잭션)

  트랜잭션 / 특성 / 주요 연산 / 상태 / Lock / 격리수준 / 주의할 점

- [교착상태](#교착상태)

  교착상태 / 예시 / 빈도 낮추기

- [References](#References)



## 무결성 제약 조건

`데이터의 무결성` : 데이터에 오류가 없이 정확성, 일관성, 유효성이 유지되는 상태를 의미한다.

`제약 조건` : 데이터의 무결성을 지키기 위한 조건으로 정해놓은 규칙에 맞는 데이터만 입력받고 규칙에 어긋나는 데이터는 거부하는 방식으로 작동한다.

DBMS에서 데이터의 무결성을 지키기 위한 제약 조건은 아래의 2가지가 있으며 2가지 `모두` 지켜져야 한다.

### 개체 무결성 제약조건

1. 릴레이션의 기본키에는 `NULL 값`이 올 수 없다.

   → 릴레이션에서 레코드에 쉽게 접근하게 해주는 기본키의 역할을 가능하게 한다.

2. 릴레이션의 기본키에는 `중복된 값`이 올 수 없다.

   → 릴레이션에서 레코드를 유일하게 구분해주는 기본키의 역할을 가능하게 한다.

- 기본키에 NULL 값이나 중복된 값이 들어가게 하는 SQL문은 거부함으로써 개체 무결성 제약조건을 준수한다.

### 참조 무결성 제약조건

두 릴레이션의 연관된 레코드들 사이의 일관성을 유지하기 위해 필요하다.

1. 외래키의 값은 NULL이거나 참조 릴레이션의 기본키 값과 동일해야 한다.
2. 외래키 값은 참조할 수 없는 값을 가질 수 없다.

<img width="977" alt="Screen Shot 2022-10-12 at 4 50 49 PM" src="https://user-images.githubusercontent.com/59877415/195282939-cb62c475-ae8e-4dc2-8dd5-600fbf99a3f3.png">
<img width="977" alt="Screen Shot 2022-10-12 at 4 51 06 PM" src="https://user-images.githubusercontent.com/59877415/195282948-6801a7bc-431c-46ed-9710-2f165be65240.png">

위의 예시는 참조 무결성 제약 조건이 지켜지고 있다. 만약 몽쉘의 참조하는 과자<외래키>의 값이 양파링이라면 외래키가 참조할 수 없는 값을 가지게 되어 참조 무결성 제약조건이 깨지게 된다.

---

## 트랜잭션

### 트랜잭션이란

`하나의 작업`을 수행하기 위해 필요한 `데이터베이스의 연산(SQL문)들`을 모아놓은 것으로 `논리적인 작업의 단위`이다.

- 데이터베이스를 변경하는 `INSERT, DELETE, UPDATE` 문의 실행을 주로 관리한다.
- 장애 발생시 복구 작업이나 병행 제어 작업의 중요한 단위로 사용된다.
- 데이터베이스의 무결성을 보장하기 위해 작업 수행에 필요한 연산들을 하나의 트랜잭션으로 제대로 정의하고 관리해야 한다.

<img width="703" alt="Screen Shot 2022-10-12 at 3 36 28 PM" src="https://user-images.githubusercontent.com/59877415/195267872-360bdb9d-c466-49b2-9d4d-d055e0786999.png">

트랜잭션은 데이터베이스의 회복과 동시성 제어를 가능하게 하여 데이터 베이스가 일관된 상태를 유지하도록 돕는다.

> `회복` : 데이터베이스를 처리하는데 장애가 발생하면 장애 발생 전의 상태로 되돌리는 것을 의미한다.
> `동시성 제어` : 다수의 트랜잭션이 동시에 처리되는 것을 의미한다.

- 하나의 트랜잭션에 포함된 SQL문들은 모두 실행되거나 모두 실행되지 않는다. 이러한 특성으로 인해 트랜잭션이 수행되던 중 장애가 발생하면 트랜잭션이 수행되기 전으로 돌아가게 되어 데이터베이스의 장애가 회복된다.
- 위의 그림에서 1(성호 계좌에서 5000원 인출)과 2(은경 계좌에 5000원 입금)가 하나의 트랜잭션으로 묶여 있다면 데이터 베이스가 1 수행후 장애가 발생하면 1 수행 이전의 상태로 되돌아가게 된다.

### 트랜잭션의 특성

트랜잭션은 4가지 특성을 가진다. 각각의 특성의 앞글자를 따서 `ACID 특성`이라고 불린다.

1. `원자성 (Atomicity)`

   - 트랜잭션의 연산들이 모두 정상적으로 실행되거나 하나도 실행되지 않아야 한다. `all-or-nothing`
   - 원자성 보장을 위해 `장애 발생시 회복 기능`이 필요하다.

2. `일관성 (Consistency)`

   - 트랜잭션이 성공적으로 수행된 후에도 데이터베이스가 `일관성 있는 상태`를 유지해야 한다.

   (이전 예시에서 계좌이체 전후에 계좌 잔액 합이 1만원을 유지하는 것)

3. `격리성 (Isolation)`

   - 수행 중인 트랜잭션이 `완료될 때까지` 다른 트랜잭션들이 중간 연산 결과에 접근할 수 없다.

4. `지속성 (Durability)`

   - 트랜잭션이 성공적으로 완료된 후 데이터베이스에 반영된 `수행 결과는 영구적`이어야 한다.

### 트랜잭션의 주요 연산

트랜잭션에서는 각각 작업의 완료와 실패를 의미하는 commit과 rollback 연산이 주로 사용된다.

1. `commit` 연산

- 트랜잭션의 수행이 `성공`적으로 완료되었음을 선언하는 연산이다.
- commit 연산이 수행되면 트랜잭션의 수행 결과가 데이터베이스에 `반영`되고 일관된 상태를 지속적으로 유지하게 된다.

→ 일반적으로는 개발자가 추가적인 작업을 하지 않아도 트랜잭션 내부의 SQL문들의 수행이 모두 끝나면 `auto-commit`이 된다.

2. `rollback`

- 트랜잭션의 수행이 `실패`했음을 선언하는 연산이다.
- rollback 연산이 수행되면 지금까지 트랜잭션이 실행한 연산의 결과가 `취소`되고 데이터베이스가 수행 전의 일관된 상태로 되돌아간다.

### 트랜잭션의 상태

<img width="684" alt="Screen Shot 2022-10-12 at 3 53 31 PM" src="https://user-images.githubusercontent.com/59877415/195271102-fc9441eb-d204-4d43-81b9-0b74fddcbf95.png">

- `활동` : 트랙잭션이 수행을 시작하여 `수행중`인 상태이다.
- `부분 완료` : 트랜잭션의 `마지막 연산이 실행을 끝낸` 직후의 상태이다.
- `완료` : 트랜잭션이 성공적으로 완료되어 `commit` 연산을 실행한 상태로 데이터 베이스가 새로운 일관된 상태가 되면서 트랜잭션이 종료된다.
- `실패` : 장애가 발생하여 트랜잭션의 수행이 `중단`된 상태이다.
- `철회` : 트랜잭션의 수행 실패로 `rollback` 연산을 실행한 상태로 지금까지 실행한 트랜잭션의 연산을 모두 취소하고 데이터베이스를 되돌리면서 트랜잭션이 종료된다.

### 트랜잭션과 LOCK

- 트랜잭션 : `데이터의 정합성`을 보장하기 위한 기능

  → 하나의 논리적인 작업 세트가 100% 적용되거나 아무것도 적용되지 않음을 보장한다.

  → Lock을 함께 사용한다.

- Lock : `동시성을 제어`하기 위한 기능

  → 여러 커넥션에서 동시에 동일한 자원(레코드, 테이블)을 요청할 경우 순서대로 한 시점에는 하나의 커넥션만 변경할 수 있게 해준다.

  → lock과 unlock 연산을 이용해서 제어한다.

  - `lock` 연산 : 트랜잭션이 데이터에 대한 `독점권을 요청`하는 연산
  - `unlock` 연산 : 트랜잭션이 데이터에 대한 `독점권을 반환`하는 연산

### 트랜잭션의 격리 수준

> 👆 여기서 잠깐!
> `DIRTY READ` 어떤 트랜잭션에서 처리한 작업이 완료되지 않았음에도 다른 트랜잭션에서 볼 수 있게 되는 현상
> `NON-REAPEATABLE READ` 동일한 SELECT 쿼리를 실행했을때 다른 결과가 나오는 현상 
> `PHANTOM READ` 한 트랜잭션 내에서 동일한 쿼리를 두 번 수행했는데 첫번째 쿼리에서 존재하지 않았던 레코드가 두번째 쿼리에서 나타나는 현상
> `UNDO RECORD` 데이터 갱신으로 테이블을 변경할 때 변경하기 전의 데이터를 보관하는 곳

- 트랜잭션에서 일관성 없는 데이터를 허용하는 수준

- Level 0) `Read Uncommitted`

  <img width="741" alt="Screen Shot 2022-10-12 at 4 02 44 PM" src="https://user-images.githubusercontent.com/59877415/195273131-432eabbd-32d4-49e9-be1d-b500dcc5a9d1.png">

  특정 트랜잭션의 변경 내용을 Commit과 Rollback에 상관 없이 다른 트랜잭션이 접근할 수 있다.

  → Dirty Read, Non-Repeatable Read, Phantom Read

- Level1) `Read Committed`

  <img width="714" alt="Screen Shot 2022-10-12 at 4 02 58 PM" src="https://user-images.githubusercontent.com/59877415/195273164-8e2d383c-7591-493b-b8fe-6fccd9be3218.png">

  특정 트랜잭션의 변경 내용이 Commit되어야만 다른 트랜잭션에서 접근할 수 있다. (변경중인 table을 조회하면 테이블이 아니라 `UNDO 영역`의 백업된 레코드에서 데이터를 가져온다.) 특정 트랜잭션의 Commit 전후의 테이블이 달라서 발생하는 문제이다.

  → Non-Repeatable Read, Phantom Read

  → Orable DB, SQl Server에서 Default로 사용하는 격리 수준

  - 아래와 같이 NON-REPEATABLE READ의 문제가 발생한다. 다른 트랜잭션의 Commit의 전후로 하여 같은 SQL 문이 다른 결과를 출력하는 것이다.

<img width="710" alt="Screen Shot 2022-10-12 at 4 03 32 PM" src="https://user-images.githubusercontent.com/59877415/195273222-927327ef-f419-487d-af8d-7c4d3d1a4d9a.png">

- Level2) `Reapeatable Read`

  <img width="624" alt="Screen Shot 2022-10-12 at 4 03 55 PM" src="https://user-images.githubusercontent.com/59877415/195273245-f145ed6e-b618-433d-9c6f-25ec3d07d4ab.png">

  트랜잭션이 `시작되기 전`에 Commit된 내용에 대해서만 조회할 수 있다.

  → Phantom Read 발생

  → MySQL에서 Default로 사용하는 격리 수준

  - 아래와 같이 Phantom Read의 문제가 발생한다. (SELECT … FOR UPDATE의 경우 잠금을 걸어야 하는데 언두 레코드에는 잠금을 걸 수 없어서 현재 레코드의 값을 가져오게 된다.)

  <img width="591" alt="Screen Shot 2022-10-12 at 4 04 43 PM" src="https://user-images.githubusercontent.com/59877415/195273281-114f53f8-3d58-4b50-a260-994603f70065.png">

  

- Level3) `Serializable Read`

  한 트랜잭션에서 읽고 쓰는 레코드를 다른 트랜잭션에서는 절대 접근할 수 없다.

  → `동시 처리 성능`은 많이 떨어지고 완벽한 `읽기 일관성`을 제공한다.

- 일반적으로 데이터베이스의 트랜잭션들은 Level1 ~ Level2의 격리 수준을 사용한다.

### 트랜잭션을 사용할 때 주의할 점

트랜잭션은 `최소한의 SQL문`에만 적용하는 것이 좋다.

→ 데이터 베이스의 `커넥션의 개수는 제한적`인데, 하나의 프로그램이 커넥션을 소유하는 시간이 길어지면 여유 커넥션의 개수가 줄어들이 때문이다. 트랜잭션을 과하게 적용할 경우 프로그램에서 커넥션을 가져가기 위해 기다려야 하는 상황이 발생할 수도 있다.

---

## 교착상태

### 교착상태란 무엇인가

두개 이상의 트랜잭션이 특정 자원의 lock을 획득한 채 다른 트랜잭션이 소유하고 있는 lock을 요구하여 실행하지 못하고 서로 무한정 기다리는 상태이다.

→ `lock`은 `데이터의 무결성`은 지켜주지만 `교착상태`를 일으킬 수 있다는 부작용이 있다.

> 👆 여기서 잠깐!
> `읽기 잠금 모드` : LOCK IN SHARE OPTION → 데이터를 읽을 때 사용하는 Read Lock의 개념으로 공유 잠금끼리는 충돌이 없다. 
> `쓰기 잠금 모드` : FOR UPDATE → 데이터를 쓸 때 사용하는 Write Lock의 개념으로 공유 잠금과 배타 잠금 모두에게 충돌이 발생한다.

### 교착상태의 예

트랜잭션에서 갱신 연산(Insert, Update, Delete)를 실행하면 `잠금을 획득`한다. 이때 트랜잭션1이 테이블A의 첫번째 행의 잠금을 얻고 트랜잭션2가 테이블B의 첫번째 행의 잠금을 얻은 상태에서 각각 서로의 자원을 요청하게 되면 교착상태에 빠지게 된다.

### 교착 상태의 빈도를 낮추는 방법

- `예방기법`

  : 트랜잭션이 실행되기 전에 필요한 데이터를 `모두 잠근다`.

  → 데이터가 많이 필요한 경우 모든 데이터를 잠금해야 하므로 `트랜잭션의 병행성`이 떨어지게 되고 `starvation`이 발생할 수도 있다.

- `회피기법`

  : 자원을 할당할 때 `Time Stamp`를 사용한다.

  방법1) `Wait - Die` 방식

  트랜잭션의 TS 우선순위가 높은 경우(선행 트랜잭션, 숫자가 작은 경우)에만 `기다리는 것`이 허용되고 낮은 경우(후행 트랜잭션)에는 `롤백`한다.

  방법2) `Wound-Wait` 방식

  선행 트랜잭션의 경우 자원을 `빼앗고` 후행 트랜잭션의 경우 `대기`한다(=기다린다).

- `낙관적 병행 기법`

  : 트랜잭션이 실행되는 동안에는 아무런 검사를 하지 않고, 트랜잭션이 다 실행된 이후에 `검사` 후 `문제`가 있다면 되돌린다.

- `빈도 낮추기`

  - 트랜잭션을 자주 `commit`한다.

    (트랜잭션의 크기를 `작게` 한다.)

  - 트랜잭션들이 테이블에 접근하는 순서를 동일하게 한다.

    - A → B & B → A (X)
    - A → B & A → B (O)

  - 읽기 잠금의 사용을 줄인다.

  - 트랜잭션이 `테이블 단위`의 잠금을 획득하도록 한다. (`동시성`은 떨어지지만 교착상태는 줄어든다.)

---

## References

[KOCW 데이터베이스설계 김미선 교수님](http://www.kocw.or.kr/home/cview.do?cid=485196f2ed6fa411)

[KOCW 데이터베이스의 원리와 응용 백현미 교수님](http://www.kocw.or.kr/home/cview.do?cid=9c591659f017851e)

[Part 1-5 Database](https://github.com/JaeYeopHan/Interview_Question_for_Beginner/tree/master/Database)

[Integrity&Transaction](https://github.com/workhardslave/cs-study/blob/main/Database/Integrity%26Transaction.md)

[[관계형 데이터 모델(Relational model) - 무결성 제약조건(Integrity Constraints)](https://singular-point.tistory.com/15)](https://singular-point.tistory.com/15)

[[MySQL] - 트랜잭션의 격리 수준(Isolation level)](https://zzang9ha.tistory.com/381)

[Database - 교착상태 문제를 해결하는 방법!](https://jaehoney.tistory.com/162?category=887684)

---



# 미션 - 온보딩

## 🔍 진행 방식

- 미션은 **기능 요구 사항, 프로그래밍 요구 사항, 과제 진행 요구 사항** 세 가지로 구성되어 있다.
- 세 개의 요구 사항을 만족하기 위해 노력한다. 특히 기능을 구현하기 전에 기능 목록을 만들고, 기능 단위로 커밋 하는 방식으로 진행한다.
- 기능 요구 사항에 기재되지 않은 내용은 스스로 판단하여 구현한다.

## 📮 미션 제출 방법

- 미션 구현을 완료한 후 GitHub을 통해 제출해야 한다.
  - GitHub을 활용한 제출 방법은 [프리코스 과제 제출](https://github.com/woowacourse/woowacourse-docs/tree/master/precourse) 문서를 참고해
    제출한다.
- GitHub에 미션을 제출한 후 [우아한테크코스 지원](https://apply.techcourse.co.kr) 사이트에 접속하여 프리코스 과제를 제출한다.
  - 자세한 방법은 [제출 가이드](https://github.com/woowacourse/woowacourse-docs/tree/master/precourse#제출-가이드) 참고
  - **Pull Request만 보내고 지원 플랫폼에서 과제를 제출하지 않으면 최종 제출하지 않은 것으로 처리되니 주의한다.**

## 🚨 과제 제출 전 체크 리스트 - 0점 방지

- 기능 구현을 모두 정상적으로 했더라도 **요구 사항에 명시된 출력값 형식을 지키지 않을 경우 0점으로 처리**한다.
- 기능 구현을 완료한 뒤 아래 가이드에 따라 테스트를 실행했을 때 모든 테스트가 성공하는지 확인한다.
- **테스트가 실패할 경우 0점으로 처리**되므로, 반드시 확인 후 제출한다.

### 테스트 실행 가이드

- 테스트 패키지 설치를 위해 `Node.js` 버전 `14` 이상이 필요하다.
- 다음 명령어를 입력해 패키지를 설치한다.

```bash
npm install
```

- 설치가 완료되었다면, 다음 명령어를 입력해 테스트를 실행한다.

```bash
npm test
```

---

## 🚀 기능 요구 사항

아래의 7가지 기능 요구 사항을 모두 해결해야 한다.

1. [문제 1](docs/PROBLEM1.md)
2. [문제 2](docs/PROBLEM2.md)
3. [문제 3](docs/PROBLEM3.md)
4. [문제 4](docs/PROBLEM4.md)
5. [문제 5](docs/PROBLEM5.md)
6. [문제 6](docs/PROBLEM6.md)
7. [문제 7](docs/PROBLEM7.md)

---

## 🎯 프로그래밍 요구 사항

- Node.js 14 버전에서 실행 가능해야 한다. **Node.js 14에서 정상적으로 동작하지 않을 경우 0점 처리한다.**
- `package.json`을 변경할 수 없고 외부 라이브러리(jQuery, Lodash 등)를 사용하지 않는다. 순수 Vanilla JS로만 구현한다.
- 프로그램 종료 시 `process.exit()`를 호출하지 않는다.
- 프로그램 구현이 완료되면 `ApplicationTest`의 모든 테스트가 성공해야 한다. **테스트가 실패할 경우 0점 처리한다.**
- 프로그래밍 요구 사항에서 달리 명시하지 않는 한 파일, 패키지 이름을 수정하거나 이동하지 않는다.

---

## ✏️ 과제 진행 요구 사항

- 미션은 [javascript-onboarding](https://github.com/woowacourse-precourse/javascript-onboarding) 저장소를 Fork & Clone해 시작한다.
- 과제 진행 및 제출 방법은 [프리코스 과제 제출](https://github.com/woowacourse/woowacourse-docs/tree/master/precourse) 문서를 참고한다.
