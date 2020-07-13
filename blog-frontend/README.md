# blog-frontend

2020-07-13 ~

## Setting

```
yarn create react-app blog-frontend
```

### 설정 파일 만들기

- .prettierrc
- jsconfig.json

### 라우터 적용

```
yarn add react-router-dom
```

- `<Route>`의 `exact` 옵션: 지정한 경로와 정확히 일치해야만 설정한 컴포넌트를 보여줌
- path에 `/@:username` 설정: `http://localhost:3000/@ydhwa`에서 `ydhwa`를 username 파라미터로 읽을 수 있게 해준다.
