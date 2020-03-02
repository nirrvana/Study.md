# introduction

## web application framework

- 개발자가 웹앱을 만드는 것을 도와줌
- 웹앱의 보안을 도와주기도 함
- framework의 선택에 따라 보안이 나아지지는 않음; 개발자가 선택한 framework을 잘 사용해야함
- 백엔드 저장소, 웹서버, 웹앱 등이 보안을 좌우함

- user account hijacking
- bypass of access control
- reading or modifying sensitive data
- presenting fraudulent content
- 돈을 목적으로 트로이 목마나 의도치 않은 메일을 보내게하는 소프트웨어 설치
- 브랜드 가치 절하의 목적으로 회사의 자원을 수정

> 공격을 막으려면 완벽히 이해하고 적절한 대비책을 찾아야함

# sessions

- 사용자가 웹앱을 사용하는 동안 각 사용자의 상태값을 유지하기 위한 방법
- 한번 인증하면 웹앱을 사용하는 동안 로그인 상태를 유지
- 장바구니에 물건을 담으면 웹앱을 사용하는 동안 장바구니 상태를 유지
- this kind of user-specific state can be stored in the session
- active session이 있으면 해당 세션을 이용하고 없으면 새로운 세션을 생성

## session hijacking

- 사용자의 세션을 훔친 공격자는 피해자의 이름으로 웹앱을 이용할 수 있음
- 안전하지 않은 네트워크에서 쿠키를 탈취함
  - 암호화되지 않은 무선랜은 특히 모든 트래픽을 도청하기 쉬움
  - 웹앱은 이를 막기 위해 ssl을 강제하여 암호화해야함
- 대부분의 사람들은 공공장소의 컴퓨터를 사용하고 쿠키를 제거하지 않음
  - 해당 사용자가 자리를 뜬 이후 이 사람인양 웹앱을 이용할 수 있음
- session fixation

### authentication system

1. user provides a user name and password
2. web application checks them and stores the corresponding user id in the session hash
3. on every request the application will load the user,
   identified by the user id in the session,
   without the need for new authentication
4. the session ID in the cookie identifies the session

> 쿠키가 웹앱의 임시 인증을 수행하므로 다른사람의 쿠키를 가지면
> 이 사람인양 웹앱을 이용할 수 있으며, 이는 심각한 결과를 초래할 수 있음

## session storage

- 클라이언트의 cookie에 session hash를 저장하면 session ID 불필요
  - 웹앱의 속도를 매우 빠르게 함
  - 쿠키에 세션을 저장하는 것은 논쟁의 여지가 많음
  - 쿠키는 4kB의 사이즈 제한이 있음
  - 클라이언트에 저장하여 심지어 만료된 쿠키라도 복제가능하므로
    민감한 데이터를 저장하면 안됨
  - 서버는 쿠키의 만료 시간을 정할 수 있음 ???
  - 계속되는 재사용을 방지하기 위해 session hash 내에
    timestamp를 저장하여 웹앱이 이를 무효화 할 수 있음
  - 레일즈는 기본적으로 쿠키를 암호화하므로 이를 복호화하지 않으면
    클라이언트는 내용을 읽을 수 없고, 따라서 secret만 잘 관리하면
    쿠키를 안전한 저장소로 사용할 수 있음

## replay attacks for cookiestore sessions

1. 세션에 크레딧 포인트가 저장됨
2. 크레딧을 이용해 사용자가 물건을 구매함
3. 세션에 크레딧 포인트가 차감되어 저장됨
4. 사용자가 미리 복사해둔 1번의 쿠키를 3번의 쿠키위에 덮어씀
5. 사용자는 다시 크레딧으로 물건을 살 수 있음

- 세션에 nonce(a random value)를 포함하여 해결할 수 있음
  - nonce는 한번만 유효한 random value임
  - 어플리케이션 서버가 여러대가 되면 더욱 복잡해짐
  - nonce를 db에 저장하면 cookiestore를 저장소로 사용하는 이유가
    없어짐; db사용에 따른 성능 저하
  - 제일 좋은 방법은 민감한 데이터는 db에 저장하는 것

## session fixation

![session fixation](session_fixation.png)

1. 공격자가 정상적인 로그인을 통해 유효한 session ID를 생성
2. 웹앱에 주기적으로 접근함으로써 세션을 유지
3. XSS를 통해 일반 사용자의 세션을 공격자가 가진 세션으로 강제함
4. 일반 사용자가 공격자의 세션을 가지고 로그인함

- 가장 효과적인 방어책은 로그인 이후에 새로운 session ID를 발급하고
  오래된 session을 무효화하는 것임
- 또 다른 방어책은 session에 ID뿐만 아니라 사용자를 식별할 수 있는
  정보를 넣어 요청이 들어올 때마다 이를 검증하는 것임
- 사용자를 식별하기 위한 정보에는 IP주소나 user agent 등이 있는데
  - IP주소는 Internet service provider가
    사용자를 proxy 뒤에 두면 유저를 특정하기에 부족할 수도 있고,
    세션 도중 바뀔 수 있는 정보이기에 불편함이 존재할 수 있음
  - user agent는 브라우저 이름 및 버전정보이므로
    유저를 특정하기에 덜 특정적임


## session expiry
- 쿠키에 만료 timestamp를 입력할 수 있지만, 클라이언트에서 수정가능하므로
  서버에서 만료 시점을 관리하는 것이 더 안전함
- 세션을 만료하지 않으면 session fixation 공격이 가능하므로
  created_at 정보를 저장하여 너무 오래전에 생성된 세션을 만료시킴

# Cross-Site Request Forgery