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

mongoose란 Node.js 환경에서 사용하는 MongoDB 기반 ODM(Object Data Modeling) 라이브러리이다. 이 라이브러리는 데이터베이스 문서들을 자바스크립트 객체처럼 사용할 수 있게 해준다.

```
yarn add mongoose dotenv
```

dotenv란 환경변수들을 파일에 넣고 사용할 수 있게 하는 개발 도구이다. mongoose를 사용하여 MongoDB에 접속할 때, 서버에 주소나 계정 및 비밀번호가 필요할 경우도 있는데, 이런 민감하거나 환경별로 달라질 수 있는 값은 코드 안에 직접 작성하지 않고 환경변수로 설정하는 것이 좋다. 프로젝트를 GitHub, GitLab 등의 서비스에 올릴 경우, 환경변수가 들어 있는 파일은 .gitignore 파일에 넣어 제외시켜 주어야 한다.

### .env 환경변수 파일 생성

`.env`

```
PORT=4000
MONGO_URI=mongodb://localhost:27017/blog
```

blog는 사용할 데이터베이스 이름이다. 지정한 데이터베이스가 서버에 없다면 자동으로 만들어 준다.

`src/index.js`에는 dotenv를 불러와서 `config()` 함수를 호출해 준다.

.env 파일은 변경해도 nodemon에서 자동으로 재시작하지 않으므로 직접 재시작해야 한다.

## esm으로 ES 모듈 import/export 문법 사용하기

기존 리액트 프로젝트에서 사용해 오던 ES 모듈 import/export 문법은 Node.js에서 아직 정식으로 지원되지 않는다. Node.js에 해당 기능이 구현되어 있기는 하지만 아직 실험적인 단계이므로 기본 옵션으로는 사용할 수 없으며, 확장자를 .mjs로 사용하고 node를 실행할 떄 `--experimental-modules`라는 옵션을 넣어줘야 한다.

`import/export` 문법을 반드시 사용해야 할 필요는 없으나, 이 문법을 사용하면 코드가 더욱 깔끔해진다. 그래서 이 문법을 사용하기 위해 esm 라이브러리를 사용할 것이다.

```
yarn add esm
```

## 데이터베이스의 스키마와 모델

스키마를 만들 때는 mongoose 모듈의 Schema를 사용하여 정의한다. 그리고 각 필드 이름과 필드의 데이터 타입 정보가 들어 있는 객체를 작성한다. 필드의 기본값으로는 default 값을 설정해 주면 된다.

Schema에서 기본적으로 지원하는 타입은 다음과 같다.

| 타입                            | 설명                                         |
| ------------------------------- | -------------------------------------------- |
| String                          | 문자열                                       |
| Number                          | 숫자                                         |
| Date                            | 날짜                                         |
| Buffer                          | 파일을 담을 수 있는 버퍼                     |
| Boolean                         | true 또는 false 값                           |
| Mixed(Schema.Types.Mixed)       | 어떤 데이터도 넣을 수 있는 형식              |
| ObjectId(Schema.Types.ObjectId) | 객체 아이디. 주로 다른 객체를 참조할 떄 넣음 |
| Array                           | 배열 형태의 값으로 []로 감싸서 넣음          |

스키마 내부에 다른 스키마를 내장 시킬 수도 있다.

### 모델 생성

`mongoose.model` 함수를 사용하여 모델을 만든다.

model 함수는 기본적으로 2개의 파라미터가 필요한데, 첫 번째 파라미터는 **스키마 이름** 이고, 두 번째 파라미터는 **스키마 객체** 이다. 데이터베이스는 스키마 이름을 정해 주면 그 이름의 복수 형태로 데이터베이스에 컬렉션 이름을 만든다.

예를 들어 스키마 이름을 Post로 설정하면, 실제 데이터베이스에 만드는 컬렉션 이름은 posts이다. BookInfo로 입력하면 bookinfos를 만든다.

MongoDB에서 컬렉션 이름을 만들 때, 권장되는 **컨벤션(convention)** 은 구분자를 사용하지 않고 복수 형태로 사용하는 것이다. 이 컨벤션을 따르고 싶지 않다면 세 번째 파라미터에 원하는 이름을 넣으면 된다. 이 경우 첫 번째 파라미터로 넣어 준 이름은 나중에 다른 스키마에서 현재 스키마를 참조해야 하는 상황에서 사용한다.

## MongoDB Compass의 설치 및 사용

## 데이터 생성과 조회

- 생성
  - save()
- 조회
  - find(): 목록 조회. 이후 exec() 호출 필요
  - findById(): 특정 id를 가진 데이터 조회

## 데이터 삭제와 수정

- 삭제
  - remove(): 특정 조건을 만족하는 데이터 모두 지움
  - findByIdAndRemove(): id를 찾아서 지움. 이후 exec() 호출 필요
  - findOneAndRemove(): 특정 조건을 만족하는 데이터 하나를 찾아서 제거
- 수정
  - findByIdAndUpdate({id}, {업데이트 내용}, {업데이트의 옵션}): 이후 exec() 호출 필요

## 요청 검증

### ObjectId 검증

```javascript
import mongoose from 'mongoose';

const { objectId } = mongoose.Types;
ObjectId.isValid(id);
```

검증을 위해 각 함수 내부에 위 코드를 삽입하면 코드 중복 문제가 생길 것이다. 미들웨어를 만들면 코드를 중복해 넣지 않고 한 번만 구현한 다음 여러 라우트에 쉽게 적용할 수 있다.

### Request body 검증

객체 검증을 위해 각 값을 If문으로 비교하는 방법도 있지만, 여기서는 이를 수월하게 해 주는 라이브러리인 Joi(<https://github.com/hapijs/joi>)를 설치하여 사용할 것이다.

```
yarn add @hapi/joi
```

## 페이지네이션 구현

## 정리
