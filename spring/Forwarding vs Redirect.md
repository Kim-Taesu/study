#### Forwarding

- Web Container 에서 페이지 이동만 있는 것
- 클라이언트가 최초로 호출한 URL이 바뀌지 않는다.
  - Forwarding하여 이동한 URL은 클라이언트가 확인할 수 없다.
- 서버에서만 처리된다.
  - Redirect보다 성능 측면에서 빠름
- 현재 실행중인 페이지와 Forwarding으로 이동할 새로운 페이지는 `Request`와 `Response` 객체를 공유한다.
- 요청 정보가 그대로 유지된다.
- **시스템(session, db)에 변화가 생기지 않는 단순조회(리스트보기, 검색)의 경우**



#### Redirect

- Redirect 명령이 들어오면 Web Container 는 웹 브라우저에게 다른 페이지로 이동하라고 명령한다.
  1. 클라이언트가 URL-1 로 request를 보냄
  2. URL-1의 response로 Redirect하여 URL-2로 이동하라고 명령
  3. 클라이언트는 URL-1의 response를 받고 URL-2로 이동
  4. URL-2에서 클라이언트의 요청을 받고 response를 클라이언트에게 전송
- 새로운 페이지에서는 Request와 Response 객체가 새롭게 생성된다.
  - 위 예시에서 URL-1과 URL-2는 서로 요청이 다르다.
- 클라이언트는 최초로 호출한 URL에서 Redirect하여 새롭게 바뀐 URL를 받는다.
- **시스템(session, db)에 변화가 생기는 요청(로그인, 회원가입, 글쓰기)의 경우 사용**



---

참초

- https://doublesprogramming.tistory.com/63