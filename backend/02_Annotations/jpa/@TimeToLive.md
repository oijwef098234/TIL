## @TimeToLive

> 저장될 데이터의 유효기간을 설정하는 어노테이션

해당 필드에 저장되는 값을 보고 유효기간을 판단하여 자동으로 만료기간이 되면 레코드가 삭제됨

### 예시 코드 - RefreshToken

```java
package com.example.gujeuck_server.domain.admin.entity;

import lombok.AllArgsConstructor;
import lombok.Builder;
import lombok.Getter;
import lombok.NoArgsConstructor;
import org.springframework.data.annotation.Id;
import org.springframework.data.redis.core.RedisHash;
import org.springframework.data.redis.core.TimeToLive;
import org.springframework.data.redis.core.index.Indexed;

@RedisHash
@Builder
@Getter
@NoArgsConstructor
@AllArgsConstructor
public class RefreshToken {

    @Id
    private String password;

    @Indexed
    private String token;

    @TimeToLive
    private Long timeToLive;
}
```

### 예시 코드 - JwtTokenProvider

```java
package com.example.gujeuck_server.global.security.jwt;

import com.example.gujeuck_server.domain.admin.entity.Admin;
import com.example.gujeuck_server.domain.admin.exception.AdminNotFoundException;
import com.example.gujeuck_server.domain.admin.repository.AdminRepository;
import com.example.gujeuck_server.domain.admin.entity.RefreshToken;
import com.example.gujeuck_server.domain.user.repository.RefreshTokenRepository;
import com.example.gujeuck_server.domain.user.exception.ExpiredTokenException;
import com.example.gujeuck_server.domain.user.exception.InvalidTokenException;
import com.example.gujeuck_server.domain.admin.dto.response.TokenResponse;
import com.example.gujeuck_server.domain.user.repository.UserRepository;
import com.example.gujeuck_server.global.security.auth.CustomUserDetailsService;
import io.jsonwebtoken.Claims;
import io.jsonwebtoken.ExpiredJwtException;
import io.jsonwebtoken.Jwts;
import io.jsonwebtoken.SignatureAlgorithm;
import jakarta.servlet.http.HttpServletRequest;
import lombok.RequiredArgsConstructor;
import org.springframework.security.authentication.UsernamePasswordAuthenticationToken;
import org.springframework.security.core.Authentication;
import org.springframework.security.core.userdetails.UserDetails;
import org.springframework.stereotype.Component;
import org.springframework.transaction.annotation.Transactional;
import org.springframework.util.StringUtils;

import java.util.Date;

@Component
@RequiredArgsConstructor
public class JwtTokenProvider {
    private final JwtProperties jwtProperties;
    private final UserRepository userRepository;
    private final AdminRepository adminRepository;
    private final CustomUserDetailsService customUserDetailsService;
    private final RefreshTokenRepository refreshTokenRepository;

    //access token 생성
    public String createAccessToken(String password) {
        Date now = new Date(); //코드를 실행한 시점의 현재 날짜와 시간이 저장(일시적)

        return Jwts.builder()
                .setSubject(password) //토큰의 소유자
                .claim("type", "access") //액세스 토큰임을 나타냄
                .setIssuedAt(now) //토큰 발행 시간 정보
                .setExpiration(new Date(now.getTime() + jwtProperties.getAccessExpiration() * 1000)) //토큰의 만료 시간 설정
                .signWith(SignatureAlgorithm.HS512, jwtProperties.getSecretKey()) //HS512 알고리즘, 비밀 키를 Jwtproperties에서 가져옴
                .compact();

    }

    //refresh token 생성
    @Transactional
    public String createRefreshToken(String password) {
        Date now = new Date();

        String refreshToken = Jwts.builder()
                .claim("type", "refresh")  //refresh 토큰임을 나타냄
                .setIssuedAt(now)
                .setExpiration(new java.sql.Timestamp(now.getTime() + jwtProperties.getRefreshExpiration() * 1000))
                .signWith(SignatureAlgorithm.HS512, jwtProperties.getSecretKey()) //
                .compact();

        refreshTokenRepository.save(
                RefreshToken.builder()
                        .password(password)
                        .token(refreshToken)
                        .timeToLive((jwtProperties.getRefreshExpiration())) // 이 부분이 바로 만료시간을 정해주는 부분이다.
                        .build()
        );

        return refreshToken;
    }

    // 토큰에 담겨 있는 userId로 SpringSecurity Authentication 정보를 반환 하는 메서드
    public Authentication getAuthentication(String token) {
        Claims claims = getClaims(token);
        UserDetails userDetails = customUserDetailsService.loadUserByUsername(claims.getSubject());
        return new UsernamePasswordAuthenticationToken(userDetails, "", userDetails.getAuthorities());
    }

    public Claims getClaims(String token) {
        try {
            return Jwts
                    .parser() //JWT parser 생성
                    .setSigningKey(jwtProperties.getSecretKey())
                    .parseClaimsJws(token)
                    .getBody();
        }
        catch (ExpiredJwtException E) {
            throw ExpiredTokenException.EXCEPTION;
        }
        catch (Exception E) {
            throw InvalidTokenException.EXCEPTION;
        }
    }

    public TokenResponse receiveToken(String password) {

        Date now = new Date();

        Admin admin = adminRepository.findByPassword(password)
                .orElseThrow(() -> AdminNotFoundException.EXCEPTION);

        return TokenResponse
                .builder()
                .accessToken(createAccessToken(password))
                .refreshToken(createRefreshToken(password))
                .accessExpiredAt(new Date(now.getTime() + jwtProperties.getAccessExpiration()))
                .refreshExpiredAt(new Date(now.getTime() + jwtProperties.getRefreshExpiration()))
                .build();
    }

    //HTTP 요청 헤더에서 토큰을 가져오는 메서드
    public String resolveToken(HttpServletRequest request) {
        String bearerToken = request.getHeader(jwtProperties.getHeader());

        if (StringUtils.hasText(bearerToken) && bearerToken.startsWith(jwtProperties.getPrefix())
                && bearerToken.length() > jwtProperties.getPrefix().length() + 1) {
            return bearerToken.substring(7);
        }

        return null;
    }

}
```