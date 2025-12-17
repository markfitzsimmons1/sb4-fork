# Spring Security Multi-Factor Authentication

Spring Security 7 introduces **@EnableMultiFactorAuthentication**, a declarative approach to requiring
multiple authentication factors with automatic factor routing and One-Time Token (OTT) support.

## Overview

The new `@EnableMultiFactorAuthentication` annotation simplifies implementing multi-factor authentication
by declaratively specifying required authentication factors. Spring Security automatically manages the
authentication flow, redirecting users to complete any missing factors before granting access to
protected resources.

### Key Concepts

- **@EnableMultiFactorAuthentication**: Annotation to enable and configure required authentication factors
- **FactorGrantedAuthority**: Defines available factor types (PASSWORD_AUTHORITY, OTT_AUTHORITY)
- **One-Time Token (OTT)**: Second factor authentication using time-limited tokens
- **Custom OneTimeTokenService**: Override default UUID tokens with custom implementations (e.g., 5-digit PINs)
- **Automatic Factor Routing**: Spring Security redirects users to complete incomplete authentication chains
- **Factor Completion Tracking**: Framework tracks which factors have been satisfied per session

## Resources

### GitHub Repository
https://github.com/danvega/mfa

### Video Tutorial
https://youtu.be/KmNAqlaKwjw

### Blog Post
https://www.danvega.dev/blog/spring-security-7-multi-factor-authentication

### Official Documentation
- [Spring Security MFA](https://docs.spring.io/spring-security/reference/servlet/authentication/mfa.html)
- [One-Time Token Authentication](https://docs.spring.io/spring-security/reference/servlet/authentication/onetimetoken.html)

## Example Usage

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
        // Generate 5-digit PIN instead of UUID
        String token = String.format("%05d", new Random().nextInt(100000));
        Instant expiresAt = Instant.now().plus(5, ChronoUnit.MINUTES);

        OneTimeToken ott = new DefaultOneTimeToken(token, request.getUsername(), expiresAt);
        tokens.put(token, ott);

        // Log the magic link for demo purposes
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

## Getting Started

### Running the Application
```bash
./mvnw spring-boot:run
```

### Test Credentials
| Username | Password | Roles |
|----------|----------|-------|
| user | password | USER |
| admin | password | ADMIN, USER |

### Authentication Flow
1. Navigate to `http://localhost:8080/admin`
2. Enter admin credentials (admin/password)
3. Check console output for the PIN-based magic link
4. Complete OTT verification at `/login/ott?token=YOUR_PIN`

Spring Security automatically redirects users through each required factor until all are satisfied.
