# 22장. mongoose를 이용한 MongoDB 연동 실습

2020-07-12

## 소개하기

### 관계형 데이터베이스의 한계

1. 데이터 스키마가 고정적임
   1. 스키마: 데이터베이스에 어떤 형식의 데이터를 넣을 지에 대한 정보
   2. 새로 등록하는 데이터 형식이 기존에 있던 데이터와 다르다면 기존 데이터를 모두 수정해야 새 데이터를 등록할 수 있다.
2. 확장성이 좋지 않음
   1. RDBMS는 저장하고 처리해야 할 데이터양이 늘어나면 여러 컴퓨터에 분산시키는 것이 아니라, 해당 데이터베이스 서버의 성능을 업그레이드하는 방식으로 확장해 주어야 한다.

### MongoDB

RDBMS의 한계를 극복한 문서 지향적 NoSQL 데이터베이스이다. 이 데이터베이스에 등록하는 데이터들은 유동적인 스키마를 지닐 수 있다. 종류가 같은 데이터라고 하더라도, 새로 등록해야 할 데이터 형식이 바뀐다고 하더라도 기존 데이터까지 수정할 필요는 없다.

서버의 데이터 양이 늘어나도 한 컴퓨터에서만 처리하는 것이 아니라 여러 컴퓨터로 분산하여 처리할 수 있도록 확장하기 쉽게 설계되어 있다.

### 상황에 따라 DB 선택하기

| 상황                                          | RDBMS | NoSQL |
| --------------------------------------------- | ----- | ----- |
| 데이터의 구조가 자주 바뀐다면?                |       | O     |
| 까다로운 조건으로 데이터를 필터링해야 한다면? | O     |       |
| ACID 특성을 지켜야 한다면?                    | O     |       |

\* ACID: 원자성(Atomicity), 일관성(Consistency), 고립성(Isolation), 지속성(Durability). 데이터베이스 트랜잭션이 안전하게 처리되는 것을 보장하기 위한 성질

### 문서란?

NoSQL의 문서(document)란 RDBMS의 레코드(record)와 개념이 비슷하다. 문서의 데이터 구조는 한 개 이상의 키-값 쌍으로 되어 있다.

문서는 BSON(바이너리 형태의 JSON) 형태로 저장한다. 그렇기 때문에 나중에 JSON 형태의 객체를 데이터베이스에 저장할 때, 큰 공수를 들이지 않고도 데이터를 데이터베이스에 등록할 수 있어 편하다.

새로운 문서를 만들면 \_id라는 고윳값을 자동으로 생성하는데, 이 값은 시간, 머신 아이디, 프로세스 아이디, 순차 번호로 되어 있어 값이 고유함을 보장한다.

여러 문서가 들어 있는 곳을 컬렉션이라고 한다. RDBMS에서는 테이블 개념을 사용하므로 각 테이블마다 같은 스키마를 가지고 있어야 하는 반면, MongoDB는 다른 스키마를 가지고 있는 문서들이 한 컬렉션에서 공존할 수 있다.

### MongoDB 구조

서버 하나에 데이터베이스를 여러 개 가지고 있을 수 있다. 각 데이터베이스에는 여러 개의 컬렉션이 있으며, 컬렉션 내부에는 문서들이 들어 있다.

### 스키마 디자인

댓글, 포스트와 관련된 스키마 디자인을 한다고 하자.

RDBMS에서는 포스트, 댓글마다 테이블을 만들어 필요에 따라 JOIN해서 사용하는 게 일반적이나, NoSQL에서는 모든 것을 문서 하나에 넣는다.

MongoDB에서는 보통 댓글을 포스트 문서 내부에 넣는다. 문서 내부에 또 다른 문서가 위치할 수 있는데, 이를 서브다큐먼트(subdocument)라고 한다. 서브다큐먼트 또한 일반 문서를 다루는 것처럼 쿼리할 수 있다.

문서 하나에는 최대 16MB만큼 데이터를 넣을 수 있는데, 100자 댓글 데이터라면 대략 0.24KB를 차지한다. 16MB는 16384KB이므로 문서 하나에 댓글 데이터를 약 68000개 넣을 수 있다. 서브 다큐먼트에서 이 용량을 초과할 가능성이 있다면 컬렉션을 분리시키는 것이 좋다.

## MongoDB 서버 준비

<https://www.mongodb.com/try/download/community>

Complete로 설치. MongoDB Compass도 함께 설치.

```bash
# 설치 확인
C:\Program Files\MongoDB\Server\4.2\bin>cd "C:\Program Files\MongoDB\Server\4.2\bin"

C:\Program Files\MongoDB\Server\4.2\bin>mongo
MongoDB shell version v4.2.8
connecting to: mongodb://127.0.0.1:27017/?compressors=disabled&gssapiServiceName=mongodb
Implicit session: session { "id" : UUID("b1997b67-b5e3-48c5-b6ef-efe933d0fbf0") }
MongoDB server version: 4.2.8
Server has startup warnings:
2020-07-12T14:35:57.180+0900 I  CONTROL  [initandlisten]
2020-07-12T14:35:57.180+0900 I  CONTROL  [initandlisten] ** WARNING: Access control is not enabled for the database.
2020-07-12T14:35:57.180+0900 I  CONTROL  [initandlisten] **          Read and write access to data and configuration is unrestricted.
2020-07-12T14:35:57.180+0900 I  CONTROL  [initandlisten]
---
Enable MongoDB's free cloud-based monitoring service, which will then receive and display
metrics about your deployment (disk utilization, CPU, operation statistics, etc).

The monitoring data will be available on a MongoDB website with a unique URL accessible to you
and anyone you share the URL with. MongoDB may use this information to make product
improvements and to suggest MongoDB products and deployment options to you.

To enable free monitoring, run the following command: db.enableFreeMonitoring()
To permanently disable this reminder, run the following command: db.disableFreeMonitoring()
---

> version()
4.2.8
> exit
bye
```

## mongoose의 설치 및 적용

## esm으로 ES 모듈 import/export 문법 사용하기

## 데이터베이스의 스키마와 모델

## MongoDB Compass의 설치 및 사용

## 데이터 생성과 조회

## 데이터 삭제와 수정

## 요청 검증

## 페이지네이션 구현

## 정리
