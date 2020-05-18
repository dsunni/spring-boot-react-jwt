# 🔓Simple Login App Using JWT

> ### 개발환경
>
> - Frontend : React
> - Backend : Spring Boot 2.3.0
> - JWT, Spring Security
>
> <br>
>
> ### 공부 목표
>
> 1. JWT의 개념과 구현 방법
> 2. React로 기본적인 Component 생성
> 3. Spring Boot로 Restful Server 구축
> 4. Frontend와 Backend를 연결해 JWT의 Token 값 처리
>
> <br>
>
> ### 참고
>
> - https://www.udemy.com/course/full-stack-application-with-spring-boot-and-react/
> - https://www.javainuse.com/spring/boot-jwt
> - https://medium.com/swlh/handling-access-and-refresh-tokens-using-axios-interceptors-3970b601a5da

<br>

#### 보다 자세한 기록은 :notebook_with_decorative_cover:

#### https://velog.io/@dsunni

<br>

# JWT (Json Web Token)

## 1. JWT란?

- JWT(Json Web Token)는 웹표준 ([RFC 7519](https://tools.ietf.org/html/rfc7519))으로 JSON 포맷을 이용해 정보를 가볍고 안전하게 전송하기 위한 **Claim 기반의 Web Token**이다. 
- 서버만 알고 있는 Secret Key로 디지털 서명화되어있기 때문에 신뢰할 수 있다
- 보통 **Authorization** (로그인, SSO) 또는 **안전한 정보 교환**을 위해 사용된다.
- JWT에서는 토큰 자체에 유저 정보를 담아서 HTTP 헤더에 전달하기에 *유저 세션을 유지할 필요가 없다*.

<br>

### 클레임(Claim)
  - 클레임(Claim)이란 사용자 정보나 데이터 속성 등을 의미한다.
  - 클레임 기반 토큰 안에는 사용자의 id, pw 등의 개인 정보가 들어있다.
      - `Self-contained` : 자체 포함, 토큰 자체가 정보
  - JWT는 가장 대표적인 클레임 기반 토큰이다.

<br>

### JWT의 필요성

- **Session**의 한계
  - Cookie는 정보를 클라이언트 측에 저장하고 Session은 정보를 서버측에 저장한다.
  - 따라서 유저의 수가 너무 많으면 서버 과부하

- **Scale Out**의 한계
  - 서버 확장(scale out)시 세션 정보 동기화 문제
- REST API는 **Stateless**를 지향
  - 사용자의 상태 정보를 저장하지 않는 형태 ex) 세션, 쿠키

<br>

<br>

## 2.  JWT 형식

### header.payload.signature
<br>

![Structure of JWT](https://www.javainuse.com/63_6-min.JPG)

- Encoded
  - Base64로 인코딩 되어 .을 구분자로 3가지의 문자열로 구성되어있다.
- Decoded
  1. Header : type, algorithm
  2. Payload : not mandatory / Additional information
  3. Signature
     - Base64 Encoded header + payload
     - **512 bit secret key** (base64 Encoded)

<br>

<br>

## 3. 동작 과정

![Spring Boot JWT Workflow](https://www.javainuse.com/62-12-min.JPG)

1. 클라이언트 로그인 요청 POST(id, pw)
2. 서버는 (id, pw)가 맞는지 확인 후 맞다면 **JWT**를 Secret Key로 **생성** 후 전달
3. 클라이언트는 Token을 **Local Storage** / Cookie에 **저장**
4. 클라이언트는 서버에 요청할 때 항상 **헤더에 Token을 포함**시킴
   - axios의 Interceptor 사용
5. 서버는 요청을 받을 때마다 Secret Key를 사용해**Token이 유효한지 검증**
   - 서버만이 Secret Key를 가지고 있기 때문에 검증 가능
   - Token이 검증되면 따로 username, pw를 검사하지 않아도 user identification 가능
6. 서버의 **Response**

<br>

## 4. 검증 과정

1. 클라이언트의 요청 (Header : Token)
2. Spring의 Interceptor에 의해 요청이 Intercept됨
3. 클라이언트에게 제공되었던 Token과 클라이언트의 Header에 담긴 Token 일치 확인
4. `auth0 JWT`를 이용해 `issuer, expire` 검증

<br>

<br>

# Simple Login App의 흐름

> - Making use of **hard coded user values** for User Authentication
>   - id : user_id
>   - password : user_pw

<br>

## 1. JWT 생성

- **POST** API with mapping **/authenticate**
- id: user_id, pw: user_pw 입력시 사용자 확인 후 JWT 생성

<br>

![Spring Boot JWT Generate Token](https://www.javainuse.com/62-2-min.JPG)

<br>

<br>

## 2. JWT 검증

- GET API with mapping /hello
- user가 유효한 JWT를 가지고 있을 때 접근이 허용됨

<br>

![Spring Boot JWT Validate Token](https://www.javainuse.com/62-3-min.JPG)

