# Spring Boot 3 vs 4: Feature Differences

This file mirrors the feature set in `sb4/features` and calls out what changes when moving from Spring Boot 3 to 4.
Each section includes what changed, a practical example, and migration-focused notes.

## API Versioning
What changed:
- Spring Boot 3: Spring MVC has no first-class versioning model. Teams implement URL versioning (`/v1`), custom
  headers, or media-type parameters with custom resolution code.
- Spring Boot 4: Spring Framework 7 adds a `version` attribute on request mappings and centralized configuration via
  `WebMvcConfigurer#configureApiVersioning`, plus RFC-style Deprecation/Sunset/Link headers.

Boot 3 example (URL versioning):
```java
@RestController
@RequestMapping("/api/v1/users")
class UserControllerV1 {
    @GetMapping("/{id}")
    UserDtoV1 getUser(@PathVariable Long id) { ... }
}
```

Boot 4 example (versioned mappings):
```java
@RestController
@RequestMapping("/api/users")
class UserController {
    @GetMapping(version = "1.0")
    UserDtoV1 getUserV1(@PathVariable Long id) { ... }

    @GetMapping(version = "2.0")
    UserDtoV2 getUserV2(@PathVariable Long id) { ... }
}
```

Migration notes:
- Existing URL or header versioning still works, but versioned mappings remove custom routing logic.
- Consider adding deprecation headers where you phase out old versions.

## Modular Auto-Configuration
What changed:
- Spring Boot 3: Auto-configuration lives in a single `spring-boot-autoconfigure` JAR; features appear when a
  library is on the classpath, even without explicit starters.
- Spring Boot 4: Auto-configuration is split into modules; you must include the exact auto-config module or starter
  for each feature. `spring-boot-autoconfigure-classic` restores 3.x behavior.

Boot 3 example (implicit auto-config):
```text
Classpath: h2 + spring-boot-starter-jdbc
Result: H2 console auto-configured via monolithic autoconfigure
```

Boot 4 example (explicit module):
```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-h2-console</artifactId>
</dependency>
```

Migration notes:
- Missing beans usually mean a missing starter or auto-config module.
- Use `spring-boot-autoconfigure-classic` temporarily, then remove it as you add explicit modules.

## Programmatic Bean Registration
What changed:
- Spring Boot 3: Dynamic registration uses `BeanDefinitionRegistryPostProcessor` or conditional `@Bean` methods.
- Spring Boot 4: Spring Framework 7 adds `BeanRegistrar` for concise, type-safe registration compatible with AOT.

Boot 3 example (post-processor):
```java
class MessageRegistry implements BeanDefinitionRegistryPostProcessor {
    @Override
    public void postProcessBeanDefinitionRegistry(BeanDefinitionRegistry registry) {
        RootBeanDefinition def = new RootBeanDefinition(EmailService.class);
        registry.registerBeanDefinition("messageService", def);
    }
}
```

Boot 4 example (BeanRegistrar):
```java
class MessageServiceRegistrar implements BeanRegistrar {
    @Override
    public void register(BeanRegistry registry, Environment env) {
        registry.registerBean("messageService", EmailService.class);
    }
}
```

Migration notes:
- Existing registry post-processors still work, but `BeanRegistrar` is easier to read and test.
- AOT builds benefit from the explicit, type-safe registration model.

## HTTP Interface Clients
What changed:
- Spring Boot 3: Declarative clients require manual wiring with `HttpServiceProxyFactory` and adapters.
- Spring Boot 4: `@ImportHttpServices` auto-registers all `@HttpExchange` interfaces.

Boot 3 example (manual wiring):
```java
@Bean
TodoService todoService(RestClient.Builder builder) {
    RestClient client = builder.baseUrl("https://example.com").build();
    var adapter = RestClientAdapter.create(client);
    var factory = HttpServiceProxyFactory.builderFor(adapter).build();
    return factory.createClient(TodoService.class);
}
```

Boot 4 example (auto import):
```java
@Configuration(proxyBeanMethods = false)
@ImportHttpServices(TodoService.class)
class HttpClientsConfig {}
```

Migration notes:
- Remove proxy factory boilerplate and keep only the interface definitions.
- Works cleanly with modular auto-configuration; ensure the HTTP client starter is present.

## Jackson 3 Support
What changed:
- Spring Boot 3: Jackson 2 (`com.fasterxml`) with mutable configuration and timestamp-heavy defaults.
- Spring Boot 4: Jackson 3 (`tools.jackson`) with immutable builder configuration, ISO-8601 defaults, and `hint()`.

Boot 3 example (timestamps and MappingJacksonValue):
```java
@GetMapping("/donuts")
MappingJacksonValue list() {
    MappingJacksonValue wrapper = new MappingJacksonValue(service.findAll());
    wrapper.setSerializationView(Views.Summary.class);
    return wrapper;
}
```

Boot 4 example (views via hint):
```java
@GetMapping("/donuts")
List<Donut> list() {
    return RestResponse.ok(service.findAll()).hint(Views.Summary.class);
}
```

Migration notes:
- Check date/time serialization; defaults shift to ISO-8601 strings.
- Imports and coordinates change from `com.fasterxml` to `tools.jackson`.

## JMS Client API
What changed:
- Spring Boot 3: `JmsTemplate` is common but verbose for QoS, headers, and request-reply patterns.
- Spring Boot 4: `JmsClient` introduces a fluent, type-safe API with built-in conversion and QoS chaining.

Boot 3 example (JmsTemplate):
```java
jmsTemplate.convertAndSend("orders.queue", order, message -> {
    message.setJMSPriority(9);
    return message;
});
```

Boot 4 example (JmsClient):
```java
jmsClient.send("orders.queue")
    .withPriority(9)
    .withBody(order);
```

Migration notes:
- `JmsTemplate` still works; adopt `JmsClient` for cleaner QoS and request-reply flows.

## MockMvcTester vs RestTestClient
What changed:
- Spring Boot 3: `MockMvc`, `WebTestClient`, and `TestRestTemplate` are separate APIs with different idioms.
- Spring Boot 4: `MockMvcTester` adds fluent AssertJ assertions; `RestTestClient` unifies mock and real HTTP.

Boot 3 example (MockMvc):
```java
mockMvc.perform(get("/api/books/1"))
    .andExpect(status().isOk())
    .andExpect(jsonPath("$.title").value("Clean Code"));
```

Boot 4 example (RestTestClient):
```java
client.get().uri("/api/books/1")
    .exchange()
    .expectStatus().isOk()
    .expectBody()
    .jsonPath("$.title").isEqualTo("Clean Code");
```

Migration notes:
- You can keep existing tests, but new tests can standardize on `RestTestClient` for consistency.

## Modularization of Spring Boot
What changed:
- Spring Boot 3: Monolithic autoconfigure leads to implicit feature activation.
- Spring Boot 4: Starters and auto-config modules are more granular, and some starters are renamed.

Common starter changes:
- `spring-boot-starter-web` -> `spring-boot-starter-webmvc`
- `spring-boot-autoconfigure` -> module-specific artifacts (for example, `spring-boot-autoconfigure-data-jpa`)

Migration notes:
- If a feature "disappears," add its module or starter explicitly.
- Use the classic compatibility artifact only as a short-term bridge.

## JSpecify Null Safety
What changed:
- Spring Boot 3: Nullability uses `@Nullable` annotations only; no package-level non-null defaults.
- Spring Boot 4: JSpecify `@NullMarked` allows non-null-by-default packages with explicit escapes.

Boot 4 example (package default):
```java
@NullMarked
package com.example.demo;

import org.jspecify.annotations.NullMarked;
```

Migration notes:
- Start with a single package to ease adoption.
- IDE and compiler warnings become more useful with explicit null contracts.

## OpenTelemetry Integration
What changed:
- Spring Boot 3: Micrometer tracing is common, but exporters and correlation often require manual setup.
- Spring Boot 4: `spring-boot-starter-opentelemetry` provides OTLP export, auto-instrumentation, and log correlation.

Boot 3 example (manual exporter wiring):
```text
Micrometer tracing + custom OTLP exporter configuration + logging MDC hooks
```

Boot 4 example (starter + properties):
```yaml
management:
  tracing:
    sampling:
      probability: 1.0
  otlp:
    tracing:
      endpoint: http://localhost:4318/v1/traces
```

Migration notes:
- Remove custom exporter beans and rely on starter auto-config.
- Keep any custom spans via `@Observed` or manual tracer usage.

## Core Resilience Features
What changed:
- Spring Boot 3: Retry and concurrency limits come from external libraries (Spring Retry, Resilience4j).
- Spring Boot 4: Spring Framework 7 adds built-in `@Retryable` and `@ConcurrencyLimit`.

Boot 3 example (external retry):
```java
@Retryable(maxAttempts = 3)
public String fetchData(String id) { ... }
```

Boot 4 example (framework retry):
```java
@Retryable(maxAttempts = 4, delay = 500, multiplier = 2.0, jitter = 100)
public String fetchData(String id) { ... }
```

Migration notes:
- For circuit breakers or bulkheads, you may still want Resilience4j.
- Simple retry/concurrency can be migrated to the built-in annotations.

## REST Test Client
What changed:
- Spring Boot 3: Tests use multiple APIs, which makes reuse harder across unit/slice/integration levels.
- Spring Boot 4: `RestTestClient` offers one API for controller, MockMvc, application context, or live server tests.

Boot 4 example (controller-level unit test):
```java
RestTestClient client = RestTestClient.bindToController(new TodoController(service)).build();
client.get().uri("/api/todos")
    .exchange()
    .expectStatus().isOk();
```

Migration notes:
- Start by switching new tests to `RestTestClient` to avoid touching existing suites.

## Spring Data AOT Repositories
What changed:
- Spring Boot 3: Repository parsing and implementation generation happen at runtime.
- Spring Boot 4: Spring Data AOT moves query parsing and repository generation to build time.

Boot 4 example (build-time AOT):
```xml
<execution>
  <id>process-aot</id>
  <goals>
    <goal>process-aot</goal>
  </goals>
</execution>
```

Migration notes:
- Build output appears in `target/spring-aot/`.
- Consider adding a validation test to fail the build when a repository method is not AOT-processed.

## Spring Security MFA
What changed:
- Spring Boot 3: No built-in MFA; flows are custom or delegated to external providers.
- Spring Boot 4: Spring Security 7 includes `@EnableMultiFactorAuthentication` and one-time token support.

Boot 4 example (MFA enablement):
```java
@EnableMultiFactorAuthentication(authorities = {
    FactorGrantedAuthority.PASSWORD_AUTHORITY,
    FactorGrantedAuthority.OTT_AUTHORITY
})
class SecurityConfig { ... }
```

Migration notes:
- Built-in factor routing replaces custom filter chains for basic MFA.
- You can plug in a custom `OneTimeTokenService` if you need PINs or magic links.
