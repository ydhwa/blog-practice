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

### API 연동

```
yarn add axios redux-saga
```

API 요청 시 사용할 axios 인스턴스를 만들면 나중에 API 클라이언트에 공통된 설정을 쉽게 넣어줄 수 있다. 인스턴스를 만들지 않아도 이러한 작업이 가능하나, 인스턴스를 만들지 않으면 애플리케이션에서 발생하는 모든 요청에 대해 설정하게 되므로, 또 다른 API 서버를 사용하려 할 때 곤란해질 수 있다. 따라서 처음 개발할 때부터 인스턴스를 만들어서 작업하는 것을 권장한다.

추가로 나중에 axios를 사용하지 않는 상황이 왔을 때 쉽게 클라이언트를 교체할 수 잇는 것 또한 장점이다.

### 프록시 설정

현재 백엔드 서버는 4000 포트, 리액트 개발 서버는 3000 포트로 열려있으므로 별도의 설정 없이 API를 호출하려고 하면 CORS(Cross Origin Request) 오류가 발생한다. CORS 에러는 네트워크 요청을 할 때 네트워크 요청을 할 때 주소가 다른 경우에 발생한다. 이 오류를 해결하려면 다른 주소에서도 API를 호출할 수 있도록 서버 쪽 코드를 수정해야 한다. 그런데 최종적으로 프로젝트를 다 완성하고 나면 결국 리액트 앱도 같은 호스트에서 제공할 것이기 때문에 이러한 설정을 하는 것은 불필요하다.

이를 위해 proxy 기능을 사용한다. 웹팩 개발 서버에서 지원하는 기능으로, 개발 서버로 요청하는 API들을 우리가 프록시로 정해 둔 서버로 그대로 전달해 주고 그 응답을 웹 애플리케이션에서 사용할 수 있게 해 준다.

CRA로 만든 프로젝트에서 프록시를 설정할 때는 package.json 파일을 수정해 주면 된다.

```
...
  "proxy": "http://localhost:4000/"
...
```

프록시 설정을 하고 나면 리액트 애플리케이션에서 `client.get('/api/posts')`를 했을 때, 웹팩 개발 서버가 프록시 역할을 해서 http://localhost:4000/api/posts에 대신 요청한 뒤 결과물을 응답해 준다.

## 작업 로그

### 회원가입과 로그인 구현

2020-07-15

```
[master b9cc3e2][blog-frontend] Add register, login
```

### 헤더 컴포넌트 생성 및 로그인 유지

2020-07-16

페이지 새로고침 시에도 로그인 상태를 유지하려면, 리액트 앱이 브라우저에서 맨 처음 렌더링될 때 localStorage에서 값을 불러와 리덕스 스토어 안에 넣도록 구현해 주어야 한다.

이 작업은 App 컴포넌트에서 useEffect를 사용하여 처리하거나, App 컴포넌트를 클래스형 컴포넌트로 변환하여 componentDidMount 메서드를 만들고 그 안에서 처리해도 된다. 하지만 여기서는 프로젝트의 엔트리 파일인 index.js에서 처리해 준다.

이유는 componentDidMount와 useEffect는 컴포넌트가 한 번 렌더링된 이후에 실행되기 때문이다. 이 경우에는 사용자가 아주 짧은 깜박임 현상(로그인이 나타났다가 로그아웃이 나타나는 현상)을 경험할 수도 있다. index.js에서 사용자 정보를 불러오도록 처리하고 컴포넌트를 렌더링하면 이러한 깜박임 현상이 발생하지 않는다.

`src/index.js` 코드 수정 시 sagaMiddleware.run이 호출된 이후에 loadUser 함수를 호출하는 것이 중요하다. loadUser 함수를 먼저 호출하면 CHECK 액션을 디스패치했을 때 사가에서 이를 제대로 처리하지 않는다.

- Cookie 초기화하는 방법: [개발자 도구]-[Application]-[Cookies]에서 clear 아이콘 클릭

```
[master 6c08a3b] [blog-frontend] Add header component, save login status in localStorage
```

### 로그아웃 기능 구현

2020-07-16

- 로그아웃 프로세스
  1. LOGOUT 액션 디스패치 됨
  2. 로그아웃 API 호출
  3. localStorage의 user값 삭제
  4. 리듀서에서 스토어의 user값 null로 설정

```
[master 7373b63] [blog-frontend] Add logout
```

### 글쓰기 기능 - 에디터 구현

2020-07-16 ~ 2020-07-17

```
yarn add quill
```

- 외부 라이브러리 연동 시 useRef와 useEffect를 적절하게 사용하면 된다. 만약 클래스형 컴포넌트 작성 시 createRef와 componentDidMount를 사용하면 된다.
- 각 컴포넌트의 역할에 따라 컨테이너 컴포넌트를 따로 만드는 것이 이후 유지보수에 좋다.
- Quill 에디터는 일반 input이나 textarea가 아니므로 onChange와 value 값을 사용하여 상태를 관리할 수 없다. 따라서 리덕스 스토어에 값을 넣고, 리덕스 스토어의 값이 바뀔 때 에디터 값이 바뀌도록 해야 한다.
- 사용자가 WritePage에서 벗어난 경우 데이터를 초기화해야 한다. 컴포넌트가 언마운트될 때 useEffect로 INITIALIZE 액션을 발생시켜 리덕스의 write 관련 상태를 초기화해준다.

```
[master 9b979f6] [blog-frontend] Add editor UI
[master 0c0d476] [blog-frontend] Add editor tag UI
[master 5b21d70] [blog-frontend] Manage post status with redux
```

### 포스트 조회 기능 구현

2020-07-17 ~ 2020-07-18

```bash
# 쿼리 값 생성 편하게 해주는 라이브러리
yarn add qs
```

- dangerouslySetInnerHTML: 리액트에서는 HTML을 그대로 렌더링하는 형태로 JSX를 작성하면 HTML 태그가 적용되지 않고 일반 텍스트 형태로 나타난다. 따라서 HTML을 적용하고 싶다면 해당 props를 설정해 주어야 한다.
- 포스트 페이지를 벗어날 때 데이터를 비우지 않으면, 나중에 사용자가 특정 포스트를 읽은 뒤 목록으로 돌아가서 또 다른 포스트를 읽을 때 아주 짧은 시간 동안 이전에 불러왔던 포스트가 나타나는 깜박임 현상이 발생한다.
- API를 사용할 때 파라미터로 문자열들을 받아 와서 직접 조합해도 되지만, 여기서는 qs 라이브러리를 사용하여 쿼리 값을 생성할 것이다. 이 라이브러리를 사용하면 쿼리 값을 더 편하게 생성하고 JSON으로 변환할 수 있다.
- 포스트 리스트를 보여줄 때, 내용이 나타나는 부분에 HTML 태그가 그대로 보인다. 이 태그를 없애는 작업은 서버에서 해줘야 한다. 클라이언트에서 처리하는 방법도 있으나, 현재 포스트 리스팅 시 body의 글자 수를 200자로 제한하는 기능이 있기 때문이다. 이 때문에 완성된 HTML이 아니라 HTML의 일부분만 전달되어 HTML 태그를 없애는 작업이 잘 이루어지지 않을 수 있다.

```
[master f4b7bca] [blog-frontend] Add post viewer
[master db49394] [blog-frontend] Add post list(incomplete)
[master 9c87c30] [blog-frontend] Add pagination
```

### 포스트 수정/삭제 기능 구현 및 마무리

2020-07-18

```

```

- 컴포넌트를 거쳐서 props를 전달하는 것이 싫다면 고려할 수 있는 방법
  - 상황) PostActionButton 컴포넌트를 PostViewer의 PostHead 하단에서 보여 주어야 하는데, 이 컴포넌트를 PostViewer에서 직접 렌더링하면 PostActionButton에 onEdit, onRemove 등의 props를 전달할 때 무조건 PostViewer를 거쳐서 전달해야 함. 정작 PostViewer 내부에서 사용하지는 않지만 내부 컴포넌트에서 필요하므로 한 번 거쳐서 전달해야 한다.
  1. PostActionButtons의 컨테이너 컴포넌트를 만들고 PostViewer 내부에서 바로 렌더링
  2. props를 JSX 형태로 받아 와서 렌더링
  ```javascript
  <PostViewer
    post={post}
    loading={loading}
    error={error}
    actionButtons={<PostActionButtons onEdit={onEdit} onRemove={onRemove} />}
  />
  ```

```

```
