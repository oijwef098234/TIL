## Spring Security란?

> http요청이 들어오면 보안적인 모든 철차를 통해 보안 검색대의 역할을 함

- **Security Filter Chain을 통해 요청들이 걸러짐**

---

## **SecurityFilterChain**

> 보안의 입구. “어떤 URL은 허용/인증 필요/권한 필요” 같은 정책을 선언.

- 인가에 관한 부분을 많이 담당

## **Filter란?**

> 한 줄로 서서 요청을 검사. (예: `UsernamePasswordAuthenticationFilter`, 커스텀 `JwtTokenFilter` 등)

- 여기 있는 필터들은 종류가 많고 하는일이 다양함

## **AuthenticationManager / AuthenticationProvider**

> 자격을 검증하는 심장부. (아이디/비번, 토큰 등 판정)

- 토큰이나, 비밀번호 해시를 맞춰보면서 검증

## **UserDetailsService**

> 사용자 정보 로딩 표준 인터페이스 (DB에서 사용자/권한 조회).

- security계의 repository같은 느낌

## **PasswordEncoder**

> 비밀번호 해시/검증(보통 `BCryptPasswordEncoder`).

- 비밀번호가 그냥 db에 저장되면 털리는 순간 다 털리니까 한번 암호화 해주는 친구임

## **SecurityContext & Authentication**

> “현재 요청의 사용자/권한”을 보관하는 컨텍스트와 결과 객체.

- 요청 사용자에 대한 정보를 담고 있음
- 요청 사용자가 누구인가?, 어떤 권한을 가지고 있는가?, 인증에 성공했는가?
- 약간 **UserDetailsService와 비슷하지만 느낌이 다름**

## **GrantedAuthority**

> 권한 단위(예: `ROLE_ADMIN`, `READ_POST`).

- 약간 여러 권한들을 담고있는 느낌이다.

## **Method Security**

> 컨트롤러/서비스 메서드에 `@PreAuthorize("hasRole('ADMIN')")` 같은 인가 규칙.

- 권한이 있는 사용자만 메서드를 이용할 수 있게끔 인가 설정을 부여함