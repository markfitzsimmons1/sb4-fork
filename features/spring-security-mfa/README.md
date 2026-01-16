# Spring Security Multi-Factor Authentication

Spring Security 7 introduces `@EnableMultiFactorAuthentication`, a declarative way to require multiple factors with
automatic routing and one-time token (OTT) support.

## Overview

The framework tracks satisfied factors per session and redirects users to complete missing factors before granting
access. You can plug in a custom `OneTimeTokenService` for PINs or magic links.

## Key Concepts

- **`@EnableMultiFactorAuthentication`**: Enable required factors
- **FactorGrantedAuthority**: `PASSWORD_AUTHORITY`, `OTT_AUTHORITY`
- **One-Time Token (OTT)**: Time-limited second factor
- **Custom token service**: Swap UUIDs for PINs or custom flows
- **Automatic routing**: Spring Security handles factor completion

## Example

### Security Configuration
```java
@Configuration
@EnableWebSecurity
@EnableMultiFactorAuthentication(authorities = {
        FactorGrantedAuthority.PASSWORD_AUTHORITY,
        FactorGrantedAuthority.OTT_AUTHORITY
})
public class SecurityConfig {

    @Bean
    public SecurityFilterChain securityFilterChain(HttpSecurity http) throws Exception {
        return http
            .authorizeHttpRequests(auth -> auth
                .requestMatchers("/admin/**").hasRole("ADMIN")
                .anyRequest().authenticated()
            )
            .formLogin(Customizer.withDefaults())
            .oneTimeTokenLogin(Customizer.withDefaults())
            .build();
    }

    @Bean
    public UserDetailsService userDetailsService() {
        UserDetails user = User.builder()
            .username("user")
            .password("{noop}password")
            .roles("USER")
            .build();

        UserDetails admin = User.builder()
            .username("admin")
            .password("{noop}password")
            .roles("ADMIN", "USER")
            .build();

        return new InMemoryUserDetailsManager(user, admin);
    }
}
```

### Custom One-Time Token Service
```java
@Component
public class CustomOneTimeTokenService implements OneTimeTokenService {

    private final Map<String, OneTimeToken> tokens = new ConcurrentHashMap<>();

    @Override
    public OneTimeToken generate(GenerateOneTimeTokenRequest request) {
        String token = String.format("%05d", new Random().nextInt(100000));
        Instant expiresAt = Instant.now().plus(5, ChronoUnit.MINUTES);

        OneTimeToken ott = new DefaultOneTimeToken(token, request.getUsername(), expiresAt);
        tokens.put(token, ott);

        System.out.println("OTT Login Link: http://localhost:8080/login/ott?token=" + token);

        return ott;
    }

    @Override
    public Authentication consume(ConsumeOneTimeTokenRequest request) {
        OneTimeToken token = tokens.remove(request.getToken());
        if (token == null || token.getExpiresAt().isBefore(Instant.now())) {
            throw new InvalidOneTimeTokenException("Invalid or expired token");
        }
        return new OneTimeTokenAuthenticationToken(token.getUsername());
    }
}
```

## Resources

- GitHub: https://github.com/markfitzsimmons1/mfa
- Video: https://youtu.be/KmNAqlaKwjw
- Blog: https://www.danvega.dev/blog/spring-security-7-multi-factor-authentication
- MFA docs: https://docs.spring.io/spring-security/reference/servlet/authentication/mfa.html
- OTT docs: https://docs.spring.io/spring-security/reference/servlet/authentication/onetimetoken.html
