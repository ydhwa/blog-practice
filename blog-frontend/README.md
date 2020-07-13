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

### 스타일 설정

styled-components를 사용하여 스타일링한다.

```
yarn add styled-components
```

### 리덕스 적용

```
yarn add redux react-redux redux-actions immer redux-devtools-extension
```

### VS Code snippet 적용
<https://snippet-generator.app/>

1. 위 링크에서 스니펫을 만든다.
2. [파일]-[기본 설정]-[사용자 코드 조각] 에서 스니펫 설정

<https://code.visualstudio.com/docs/editor/userdefinedsnippets> - Snippet에서 사용할 수 있는 동적 값에 대한 참고 링크