# JWT (Json Web Token)  

## JWT 구조  

JWT 구조는 **Header, Payload, Signature** 세 부분으로 이루어진다.  

각 부분은 JSON 형태로 되어있으며 **Base64**로 인코딩되어 표현된고, 각 부분은 **점(.)** 구분자를 사용해 구분된다.  

<img src="/Http/image/jwt.png" width="700" height="400">  

#### - HEADER  

JWT의 헤더는 **typ**과 **alg** 두 가지 정보로 구성된다.  

- alg: Signature를 해싱하기 위한 알고리즘을 지정 `ex) HS256 or RSA`
- typ: 토큰의 타입을 지정 `ex) JWT`  

```json
{
    "alg": "HS256",
    "typ": "JWT"
}
```

#### - PAYLOAD  

JWT의 페이로드는 토큰에서 사용할 정보가 담겨있다.  

이를 **클레임(Claim)** 이라하고 클레임은 총 3가지로 나누어지며 Json과 유사한 형태이기 때문에 다수의 정보를 넣을 수 있다.  

###### # 등록된 클레임 (Registered Claim)  

**토큰 정보를 표현하기 위해 이미 정해진 종류의 데이터**로 선택적으로 작성이 가능하며 사용하기를 권장한다.  

JWT를 간결하게 표현하기 위해 모두 길이가 **3**인 **String**으로 이루어져 있다.  

- **iss (issure):** 토큰 발급자
- **sub (subject):** 토큰 제목, 주로 unique한 값을 사용한다.
- **aud (audience):** 토큰 대상자
- **exp (expiration):** 토큰 만료 시간, NumericDate 형식으로 되어 있어야 한다.
- **nbf (not before):** 이 날이 지나기 전의 토큰은 활성화하지 않는다.
- **iat (issued at):** 토큰 발급 이후의 경롸 시간을 알 수 있다.
- **jti (JWT ID):** 중복 방지를 위해 사용하며, 일화용 토큰 (Access Token) 등에 사용된다.

###### # 공개 클레임 (Public Claim)  

**사용자 정의 클레임으로, 공개용 정보**를 위해 사용된다.  

서로 충돌이 일어나지 않는 이름을 가지고 있어야 하며 주로 URL 형태로 작성한다.  

```json
{
    "https://foo.net": true
}
```

###### # 비공개 클레임 (Private Claim)  

**사용자 정의 클레임으로, 서버와 클라이언트 사이에 임의로 지정한 정보**를 저장한다.  

```json
{
    "token_type": "access"
}
```

#### - SIGNATURE  

위에서 만든 HEADER와 PAYLOAD의 값을 BASE64로 인코딩 하고, 해당 값을 비밀키를 이용해 헤더에서 정의한 알고리즘으로 해싱을 하고 이 값을 다시 BASE64로 인코딩해 생성한다.  

## JWT 인증 절차  

<img src="/Http/image/jwt-process.png" width="600" height="300">  

1. 사용자가 id와 password를 입력하여 로그인을 시도한다.
2. 서버는 요청을 확인하고 secret key를 통해 Access token을 발급한다.
3. 인증 이후 사용자가 API를 요청할 때 Authorization header에 Access token을 담아서 요청한다.
4. 서버는 JWT Signature를 체크하고 Payload로부터 사용자 정보를 확인하고 원하는 자원을 반환한다.


## 사용 시 주의점  

JWT 페이로드에는 BASE64로 인코딩만 될 뿐 이것을 다시 디코딩하여 정보를 얻을 수 있다.  

때문에 가급적 토큰에는 중요한 정보를 담지 않는 것이 좋다.  

---

#### 📌 Reference  

- <https://jwt.io/>
- <https://mangkyu.tistory.com/56>
- <https://elfinlas.github.io/2018/08/12/whatisjwt-01/>