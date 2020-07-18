# 프로젝트 마무리

1. 프로젝트 빌드
   백엔드 서버를 통해 리액트 앱을 제공할 수 있도록 빌드해줘야 한다.

   [blog-frontend] 에서 다음 명령어 실행

   ```
   yarn build
   ```

   `blog-frontend/build` 디렉터리 생성 여부 확인

2. koa-static으로 정적 파일 제공
   서버를 통해 blog-frontend/build 디렉터리 안의 파일을 사용할 수 있도록 koa-static을 사용하여 정적 파일 제공 기능을 구현한다.

   [blog-backend] 에서 다음 명령어 실행

   ```
   yarn add koa-static
   ```

   `src/main.js` 수정

3. [blog-backend] 실행 후 http://localhost:4000 으로 접속

## 더 할 수 있는 작업

- 코드 스플리팅
  - 프로젝트 규모가 커졌을 때 도입하는 것이 바람직하지만, 프로젝트를 장기적으로 유지 보수할 것 같다면 초반부터 도입하는 것을 추천한다.
- 서버 호스팅
  - AWS EC2 <https://aws.amazon.com/ko/ec2/>
  - Google Cloud Compute Engine <https://cloud.google.com/compute/all-pricing>
  - NCloud Compute <https://www.ncloud.com/product/compute>
  - Vultr <https://www.vultr.com/products/cloud-compute/>
- 서버 사이드 렌더링
  - 서버 사이드 렌더링 시 서버 엔트리 코드에서 우리가 만든 axios 클라이언트 client 인스턴스에 baseURL을 설정해 주어야 한다.
    ```javascript
    import client from "./lib/api/client";
    client.defaults.baseURL = "http://localhost:4000";
    ```
  - 서버 사이드 렌더링을 적용하면, 서버 컴퓨터에서 두 종류의 서버를 구동해야 한다. 하나는 API 서버이고, 다른 하나는 서버 사이드 렌더링 전용 서버이다. nginx을 사용하여 사용자가 요청한 경로에 따라 다른 서버에서 처리하게끔 하면 된다. 또한, nginx를 사용하는 경우에는 정적 파일 제공을 Node.js 서버가 아닌 nginx 자체적으로 처리하는 것이 성능상 빠르다.

## 정리

1. 기능 설계하기: 어떤 컴포넌트가 필요할지 생각하기
2. UI 만들기: 사용자에게 보이는 UI를 먼저 만든다.
3. API 연동하기: API 연동이 필요할 경우 필요한 코드를 준비한다.
4. 상태 관리하기: 리덕스, 컴포넌트 자체 상태 등을 통해 상태를 관리하고, 필요하면 컨테이너 컴포넌트를 새로 만든다.

위와 같은 흐름으로 개발을 진행하는 과정에서 반복되는 코드가 있을 경우, 함수로 분리하거나 재사용할 수 있는 컴포넌트로 분리하면 좋다. 성능상 문제가 되는 부분이 있다면 shouldComponentUpdate 또는 React.memo를 사용하여 최적화를 시도해볼 수 있다.

## 커뮤니티 및 참고 링크

- <https://www.reddit.com/r/reactjs/>
- <https://www.reactiflux.com/>
- <https://velog.io/@velopert>
