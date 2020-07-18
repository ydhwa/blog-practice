# sanitize-html을 이용하여 HTML 태그 없애기

2020-07-18

```bash
# HTML 필터링 기능을 위한 라이브러리
yarn add sanitize-html
```

<https://www.npmjs.com/package/sanitize-html>

포스트 리스트를 보여줄 때, 내용이 나타나는 부분에 HTML 태그가 그대로 보인다. 이 태그를 없애는 작업은 서버에서 해줘야 한다. 클라이언트에서 처리하는 방법도 있으나, 현재 포스트 리스팅 시 body의 글자 수를 200자로 제한하는 기능이 있기 때문이다. 이 때문에 완성된 HTML이 아니라 HTML의 일부분만 전달되어 HTML 태그를 없애는 작업이 잘 이루어지지 않을 수 있다.

sanitize-html 라이브러리는 HTML을 작성하고 보여 주어야 하는 서비스에서 유용하다. HTML을 제거하는 기능뿐만 아니라 특정 HTML만 허용하는 기능도 있기 때문에 글쓰기 API에서 사용하면 손쉽게 악성 스크립트 삽입을 막을 수 있다.

```
[master 6daab9a] [blog-backend] Add HTML filtering
```
