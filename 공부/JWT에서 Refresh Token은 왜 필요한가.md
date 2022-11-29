# JWT에서 Refresh Token은 왜 필요한가?

## JWT란?

비밀키를 이용하여 서명된 JSON 형태의 데이터.

헤더, 페이로드, 서명. 이 세 가지 정보를 base64로 인코딩한 값을 `.`을 사이에 두고 이어붙인 형태로 생성된다.

- 헤더: JWT 서명에 사용된 알고리즘을 담는다.

- 페이로드: 토큰에 담긴 주체(subject), 만료일(exp), 생성자(iss) 등을 담는다.

- 시그니처: 헤더의 인코딩 값과 페이로드의 인코딩 값을 합친 후 주어진 비밀키로 해쉬하여 생성한 일련의 문자열로, 토큰 변조 여부를 확인할 수 있다.

## Access Token, Refresh Token란?

JWT는 stateless한 방식이기 때문에, 서버측에서는 이 토큰을 갖고있는 클라이언트가 정말 본인이 맞는지 확인할 수 없다는 문제가 있다.

그래서 이에 대한 보안 대책으로 Refresh Token이라는 추가적인 토큰을 활용할 수 있다. 이 Refresh Token은 사용자 인증용이 아닌, 새 Access Token 생성용으로만 사용된다.

- Access  Token의 유효기간은 짧게 설정한다.

- Refresh Token의 유효기간은 길게  설정한다.

- 사용자는 Access Token과 Refresh Token을 둘 다 서버에 전송하여 전자로 인증하고, 만료될 시 후자로 새 Access  Token을 발급받는다.

- 공격자는 Access Token을 탈취하더라도 짧은 유효기간이 지나면 사용할 수 없다.

즉, OTP 인증처럼 짧은 시간 동안에만 사용할 수 있도록 하고, 주기적으로 재발급 받도록 하여, 토큰이 유출되더라도 그 피해를 최소화한다는 방식이다.

하지만, 정상적인 클라이언트도 짧은 주기마다 다시 로그인해서 Access Token을 발급받아야 한다는 단점이 있다. 그래서 여기서 유효 기간이 긴 Refresh Token을 사용하는데, 정상적인  사용자는 Access Token이 만료됐다면 서버측에 Refresh Token을 전송하여 다시 로그인할 필요 없이 Access Token을 발급받을 수 있다.

또, 같은 사용자가 여러 디바이스(스마트폰, PC 등)에서 접근하는 경우 각 디바이스 타입에 맞는 Access Token, Refresh Token 쌍이 필요할 것이다.

## Refresh Token의 탈취

그런데 이 Refresh Token마저 탈취당한다면 어떻게 될까?

공격자는 이 토큰의 유효기간 만큼 다시 Access Token을 생성해서 다시 정상적인 사용자로 위장할 수 있다. 그렇게 때문에 서버측의 검증 로직이 필요한데, 스택오버플로우는 이렇게 답변한다.

- DB에 각 사용자에 1:1로 대응되는 Access Token, Refresh Token 쌍을 저장한다.

- 정상적 사용자는 기존 Access Token으로 접근하여 서버측에서는 데이터베이스에 저장된 Access Token과 비교하여 검증한다.

- 공격자는 Refresh Token으로 새 Access Token을 생성해도, DB에 저장된 Access Token과 다름을 확인할 수 있게된다.

- 만약 DB에 저장된 토큰이 아직 만료되지 않은 경우, 즉 굳이 Access Token을 새로 생성할 이유가 없는 경우라면, 서버는 Refresh Token이 탈취당했다고 가정하고 두 토큰을 모두 만료시킨다.

- 이 경우, 정상적 사용자도 재로그인을 해야한다. 하지만 공격자가 탈취한 토큰이 만료되기 때문에 정상적 리소스에 접근할 수 없게된다.

JWT는 그냥 문자열로 존재하기 때문에, 클라이언트나 서버측에서 전역적으로 만료시킬 수 없다. 때문에 만료된 토큰을 NoSQL같은 DB에 저장하여 관리할 필요가 있다.

ietf문서에서는 아예 Refresh Token도 Access Token과 같은 유효기간을 가지도록 하여, 사용자가 한 번 Refresh Token으로 Access Token을 발급받았으면 Refresh Token도 다시 발급받도록 하는 것을 권장하고 있다.

그렇다면... Access Token과 Refresh Token을 둘 다 탈취한다면? 방법이 없다... FE, BE에서 로직을 강화하여 토큰 유출을 방지하도록 보완하는 수 밖에 없을 것이다.

> But then again there is nothing like 100% security.

## 토큰의 저장 장소

서버에서는 NoSQL이나 기타 DB에 저장할 수 있다.

클라이언트에서는 쿠키, 로컬 스토리지 등 다양하게 있지만, 스택오버플로우에서는 http-only 속성이 부여된 쿠키에 저장하는 것을 권장하고 있다.

왜냐면 해당 속성이 부여된 쿠키는 JS환경에서 접근할 수 없기 때문이다. 그래서 XSS나 CSRF가 발생하더라도 토큰이 누출되지 않는다. 일반 쿠키나 브라우저 로컬 스토리지는 JS로 자유롭게 접근할 수 있기 때문에 보안 측면에서 권장되지 않는다.


