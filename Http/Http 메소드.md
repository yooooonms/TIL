# Http Method  

클라이언트가 웹 서버에게 요청하는 목적 및 그 종류를 알리는 수단으로 사용된다.  

Http Method는 HTTP 요청 메시지의 첫 줄(start line)에 작성된다.  

```text
GET /hello HTTP/1.1
```

주로 사용하는 Http 메소드는 다음과 같다.  

- GET: 리소스 조회
- POST: 요청 데이터 처리 주로 등록에 사용한다.
- PUT: 리소스를 대체 해당 리소스가 없다면 생성
- PATCH: 리소스 부분 변경
- DELETE: 리소스 삭제

## GET  

리소스를 조회할 때 사용한다.  

서버에 전달하고 싶은 데이터를 다음처럼 query(query parameter, query string)를 통해 전달한다.  

```text
~/hello?name=yoon&age=99
```

메시지 바디를 통해 데이터를 전달할 수 있지만 지원하지 않는 곳도 존재하기 때문에 권장하는 방법은 아니다.  

## POST  

메시지 바디를 통해 서버로 데이터를 전달한다.  

서버는 메시지 바디를 통해 들어온 데이터를 처리하는 기능을 수행한다. 주로 신규 리소스 등록, 프로세스 처리등에 사용된다.  

## PUT  

리소스를 현재 메시지의 값으로 생성하거나 만약 존재한다면 기존 리소스를 삭제하고 덮어쓰기 한다.  

#### - POST와 PUT의 구분  

PUT은 POST와는 다르게 클라이언트가 **리소스의 위치를 알고 URI를 지정**해 주어야 한다.  

## PATCH  

리소스를 부분적으로 변경한다.  

지원하지 않는 경우 POST로 대체하여 사용한다.  

## DELETE  

리소스의 삭제를 요청하는데 사용한다.  

## HTTP 메소드 속성  

|메소드|안전|멱등|캐시 가능|
|:----:|:----:|:---:|:-------:|
|GET| O | O | O |
|HEAD| O | O | O |
|POST| X | X | O |
|PUT| X | O | X |
|DELETE| X | O | X |
|CONNECT| X | X | X |
|OPTIONS| O | O | X |
|TRACE| O | O | X |
|PATCH| X | X | O |

#### - 안전 (Safe)  

호출해도 리소스를 변경하지 않는다.  

안전은 해당 리소스만 고려한다. 만약 계속 호출해서 로그가 쌓여 장애가 발생해도 리소스만 고려한다.  

#### - 멱등 (Idempotent)  

한 번 호출하든 두 번 호출하든 몇 번을 호출하든 결과는 항상 동일하다.  

외부 요인으로 중간에 리소스가 변경되는 것을 고려하지 않고 해당 요청을 기준으로 고려한다.  

올바르게 구현한 GET, PUT, DELETE 메소드는 멱등성을 지녀야 한다.  

#### - 캐시 가능 (Cacheable)  

응답 결과 리소스를 캐시해서 사용해도 되는 메소드이다.  

GET, HEAD, POST, PATCH가 캐시 가능이지만 실제로는 GET, HEAD 정도만 캐시로 사용한다.  

POST, PATCH는 본문 내용까지 캐시 키로 고려해야하기 때문에 구현이 쉽지 않다.  

---

#### 📌 Reference  

- <https://www.inflearn.com/course/http-%EC%9B%B9-%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC/dashboard>