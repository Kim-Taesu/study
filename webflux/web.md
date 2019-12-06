### Servlet

- 웹 환경에서 자바 프로그램이 구현
- 자바 코드 안에 HTML을 포함 (JSP는 HTML 문서 안에 Java 코드를  포함)



### Servlet Container

- 서블릿 생명 주기를 관리하고 요청에 따른 쓰레드 생성
- HTTP request를 받아 Servlet을 실행시키고 실행 결과를 브라우저에게 전달
- 멀티 쓰레딩 지원
- ex : Tomcat, jetty, jboss, ...



### Web Server

- 웹서버에서 클라이언트의 request를 받아 Servlet Container로 전송하고 Servlet Container 수행한 결과를 클라이언트로 전송한다.
- 정적인 data를 처리
- ex : Apache



### Web Application Server

- Web Server + Web Container
- 다양한 기능을 container에 구현하여 서버가 다양한 기능을 수행
- 동적인 data를 처리
- ex : Apache Tomcat



### Web Server + WAS

- 대용량의 Data가 자주 전송될 때는 web server와 was를 동시에 사용한다.
- 정적인 데이터는 web server가, 동적인 데이터는 was가 처리한다.