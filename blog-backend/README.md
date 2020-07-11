# blog-backend

## Koa 프레임워크

Express 개발팀이 개발한 프레임워크로, 미들웨어 기능만 갖추고 있어 필요한 기능들만 붙여 서버를 개발할 수 있기 때문에 Express보다 훨씬 가볍다. 또한, async/await 문법을 정식으로 지원하기 때문에 비동기 작업을 더 편하게 관리할 수 있다.

## 프로젝트 생성

```bash
mkdir blog
cd blog
mkdir blog-backend
cd blog-backend
yarn init -y

# Koa 설치
yarn add koa

# ESLint 설치 (이 작업 이후 .eslintrc.json 파일 생성 확인)
# yarn add에서 --dev 옵션은 개발용 의존 모듈로 설치한다는 의미이다.
# 이렇게 설치하면 package.json에서 devDependencies 쪽에 모듈의 버전 정보가 입력된다.
yarn add --dev eslint
yarn run eslint --init
---
yarn run v1.22.4
$ E:\workspace\study\react\blog\blog-backend\node_modules\.bin\eslint --init
√ How would you like to use ESLint? · problems
√ What type of modules does your project use? · commonjs
√ Which framework does your project use? · none
√ Does your project use TypeScript? · No / Yes
√ Where does your code run? · node  # 선택할 때 Space를 눌러서 Node 활성화
√ What format do you want your config file to be in? · JSON
Successfully created .eslintrc.json file in E:\workspace\study\react\blog\blog-backend
Done in 33.67s.
---
```

Prettier 설정 (.prettierrc)

```json
{
  "singleQuote": true,
  "semi": true,
  "useTabs": false,
  "tabWidth": 2,
  "trailingComma": "all",
  "printWidth": 80
}
```

Prettier에서 관리하는 코드 스타일은 ESLint에서 관리하지 않도록 eslint-config-prettier 설치하여 적용

```bash
yarn add eslint-config-prettier
```

설치 후 .eslintrc.json 파일 수정

```json
{
  "env": {
    "commonjs": true,
    "es2020": true,
    "node": true
  },
  "extends": ["eslint:recommended", "prettier"],
  "globals": {
    "Atomics": "readonly",
    "SharedArrayBuffer": "readonly"
  },
  "parserOptions": {
    "ecmaVersion": 11
  },
  "rules": {
    "no-unused-vars": "warn",
    "no-console": "off"
  }
}
```

## 실행

```
node src/
```

## 미들웨어

Koa 애플리케이션은 미들웨어의 배열로 구성되어 있다. `app.use` 함수는 미들웨어 함수를 애플리케이션에 등록한다.

미들웨어 함수는 다음과 같은 구조로 이루어져 있다.

```javascript
(ctx, next) => {};
```

Koa 미들웨어 함수는 ctx와 next라는 두 개의 파라미터를 받는다.

`ctx`는 Context의 줄임말로 웹 요청과 응답에 관한 정보를 지니고 있다. `next`는 현재 처리 중인 미들웨어의 다음 미들웨어를 호출하는 함수이다. 미들웨어를 등록하고 next 함수를 호출하지 않으면, 그 다음 미들웨어를 처리하지 않는다.

만약 미들웨어에서 next를 사용하지 않으면 `ctx => {}`와 같은 형태로 파라미터에 next를 설정하지 않아도 괜찮다. 주로 다음 미들웨어를 처리할 필요가 없는 라우트 미들웨어를 나중에 설정할 때 이러한 구조로 next를 생략하여 미들웨어를 작성한다.

미들웨어는 `app.use`를 사용하여 등록되는 순서대로 처리된다. next를 호출하지 않으면 다음 미들웨어를 호출하지 않는다. 이런 속성을 사용하여 조건부로 다음 미들웨어 처리를 무시하게 만들 수 있다.

### next 함수는 Promise를 반환

next 함수를 호출하면 Promise를 반환한다. 이는 Koa가 Express와 차별화되는 부분이다. next 함수가 반환하는 Promise는 다음에 처리해야 할 미들웨어가 끝나야 완료된다.

### async/await 사용하기

Koa는 async/await를 정식으로 지원한다.

## nodemon 사용하기

nodemon을 사용하면 코드를 변경할 때마다 서버를 자동으로 재시작해 준다.

```
yarn add --dev nodemon
```

`package.json`에 'start', 'start:dev' script를 추가해준다.

```json
...
  "scripts": {
    "start": "node src",
    "start:dev": "nodemon --watch src/ src/index.js"
  }
...
```

`start` 스크립트에는 서버를 시작하는 명령어를 넣고, `start:dev` 스크립트에는 nodemon을 통해 서버를 실행해 주는 명령어를 넣었다. 여기서 nodemon은 src 디렉터리를 주시하고 있다가 해당 디렉터리 내부의 어떤 파일이 변경되면, 이를 감지하여 src/index.js 파일을 재시작해 준다.

```bash
# 재시작이 필요하지 않을 때
yarn start

# 재시작이 필요할 때
yarn start:dev
```

## koa-router 사용하기

```
yarn add koa-router
```

라우트를 설정할 때, router.get의 첫 번째 파라미터에는 라우트의 경로를 넣고, 두 번째 파라미터에는 해당 라우트에 적용할 미들웨어 함수를 넣는다. 여기서 get 키워드는 해당 라우트에서 사용할 HTTP 메서드를 의미하는데, get 대신에 post, put, delete 등을 넣을 수 있다.

```bash
# 라우트의 파라미터 설정할 때
/about/:name

# 파라미터가 있을 수도, 없을 수도 있는 경우
/about/:name?
```

위와 같이 설정한 파라미터는 함수의 ctx.params 객체에서 조회할 수 있다.

URL 쿼리의 경우, ctx.query에서 조회할 수 있다. 쿼리 문자열을 자동으로 객체 형태로 파싱해 주므로 별도로 파싱 함수를 돌릴 필요가 없다. 문자열 형태의 쿼리 문자열을 조회해야 할 때는 `ctx.querystring`을 사용한다.

파라미터와 쿼리는 둘 다 주소를 통해 특정 값을 받아올 때 사용하지만, 용도가 서로 조금씩 다르다. 정해진 규칙은 따로 없지만, 일반적으로 파라미터는 처리할 작업의 카테고리를 받아 오거나, 고유 ID 혹은 이름으로 특정 데이터를 조회할 때 사용한다. 반면, 쿼리는 옵션에 관련된 정보를 받아 온다. 예를 들어 여러 항목을 리스팅하는 API라면, 어떤 조건을 만족하는 항목을 보여 줄지 또는 어떤 기준으로 정렬할지를 정해야 할 때 쿼리를 사용한다.

### 컨트롤러 파일 작성

라우트를 작성하는 과정에서 특정 경로에 미들웨어를 등록할 때는 다음과 같이 두 번째 인자에 함수를 선언해서 바로 넣어줄 수 있다.

```javascript
router.get('/', (ctx) => {});
```

하지만 각 라우트 처리 함수의 코드가 길면 라우터 설정을 한눈에 보기 힘드므로, 라우트 처리 함수들을 다른 파일로 따로 분리해서 관리할 수 있다. 이 라우트 처리 함수만 모아놓은 파일을 컨트롤러라고 한다.

API 기능을 본격적으로 구현하기 전에 먼저 `koa-bodyparser` 미들웨어를 적용해야 한다. 이 미들웨어는 POST/PUT/PATCH같은 메서드의 request body에 JSON 형식으로 데이터를 넣어 주면, 이를 파싱하여 서버에서 사용할 수 있게 한다.

```bash
yarn add koa-bodyparser
```

**router를 적용하는 코드의 윗부분에서 적용해야 한다.**

컨트롤러를 만들면서 `exports.이름 = ...`형식으로 함수를 내보내 주었는데, 이렇게 내보낸 코드는 다음 형식으로 불러올 수 있다.

```javascript
const 모듈이름 = require('파일이름');
모듈이름.이름();
```

### PUT과 PATCH 비교

- PATCH: request body에 담긴 필드만 수정됨
- PUT: 전체 필드가 수정됨

PUT으로 구현 시 모든 필드가 다 있는지 검증하는 작업이 필요하다.
