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
