## Cross Origin Resource Sharing



#### CORS

- 도메인 또는 포트가 다른 서버의 자원을 요청하는 매커니즘
  - 요청을 할때 cross-origin HTTP에 의해 요청된다.
- 웹 브라우저에서 외부 도메인 서버와 통신하기 위한 방식을 표준화한 스펙
- 동일 출처 정책으로 CORS 상황이 발생 하면 외부서버에 요청한 데이털르 브라우저에서 보안목적으로 차단한다.
  - 동일 출처 정책(same-origin policy) 
    - 현재 문서나 스크립트가 다른 출처에서 가져온 리소스와 상호작용하는 것을 제한하는 중요한 보안 방식
    - 동일 출처 : 프로토콜, 호스트명, 포트가 같아야 함



#### CORS 사용 이유?

- same-origin policy의 규칙에 의해 구현
  - AJAX 요청이 POST, PUT, DELETE 등과 같은 고급 요청을 수행하여 웹 사이트의 보안에 치명적인 문제가 될 수있다.
  - same-origin policy를 사용 = 웹 보안을 견고히하고 사용자의 악용을 막는다



![Complete Guide to Cross-Origin Resource Sharing (CORS)](https://www.keycdn.com/img/support/cors.png)



#### CORS 작동 방식

- 요청 URL이 외부 도메인일 경우 웹 브라우저는 외부 도메인 서버에 preflight 요청을 먼저 날린다.

  - preflight 요청 : 실제 요청하려는 경로와 같은 URL에 대해 OPTIONS 메서드로 요청을 미리 날려보고 요청을 할 수 있는 권한이 있는지 확인

  - preflight의 요청 헤더

    - Origin
    - Access-Control-Request-Method
    - Access-Control-Request-Headers

  - 예시 (domainx --> domainy)

    ```
    Origin: http://domainx.com
    Access-Control-Request-Method: POST
    Access-Control-Request-Headers: X-Custom-Header
    ```

    

- 외부 서버에서 preflight의 요청을 받으면 서버의 HTTP Response Headers에는 특정 option을 추가한다.

  - Access-Control-Allow-Origin
  - Access-Control-Allow-Credentials
  - Access-Control-Expose-Headers
  - Access-Control-Max-Age
  - Access-Control-Allow-Methods
  - Access-Control-Allow-Headers

  - 예시 (domainy --> domainx)

    ```shell
    Access-Control-Allow-Origin: http://domainx.com
    Access-Control-Allow-Methods: GET, POST
    Access-Control-Allow-Headers: X-Custom-Header
    ```



#### 해결방법

1. 서버측에서 response에 cross-origin HTTP 요청을 허가해주는 헤더를 추가한다.
2. node.js의 경우 미들웨어를 사용하여 전부 허용하거나 옵션값을 설정하여 일부만 허용
3. 스프링은 `@CrossOrigin(origin='http://domain:port')`와 같은 방법을 이용하여 허용

---

#### 참조

- https://brunch.co.kr/@adrenalinee31/1
- https://www.keycdn.com/support/cors
- https://velog.io/@wlsdud2194/cors