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
