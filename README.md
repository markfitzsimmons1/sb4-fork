# Spring Boot 4 Feature Discovery

A discovery hub for exploring new features in **Spring Boot 4** and **Spring Framework 7**.
Each feature has its own dedicated repository with complete implementations, tutorials, and documentation.

## Features

| Feature | Description | Details |
|---------|-------------|---------|
| [Null Safety](features/null-safety/) | JSpecify annotations for compile-time null safety | Package-level `@NullMarked` annotation |
| [HTTP Interface Clients](features/http-interface-clients/) | Declarative HTTP clients with `@HttpExchange` | Replace RestTemplate/WebClient boilerplate |
| [Bean Registration](features/bean-registration/) | Programmatic bean registration | `BeanRegistrar` interface |
| [API Versioning](features/api-versioning/) | First-class API versioning support | Media type parameter versioning |
| [JMS Client](features/jms-client/) | JMS messaging with Apache Artemis | Producer/Consumer patterns |
| [Resilience](features/resilience/) | Built-in resilience without external dependencies | `@Retryable` and `@ConcurrencyLimit` |
| [Jackson 3](features/jackson3/) | Jackson 3 JSON processing support | ISO-8601 defaults, `hint()` method, JSON Views |
| [REST Test Client](features/rest-test-client/) | Unified REST API testing | One API for unit, integration, and E2E tests |
| [Spring Data AOT](features/spring-data-aot/) | Ahead-of-time repository compilation | 50-70% faster startup, compile-time validation |
| [OpenTelemetry](features/otel/) | Official observability starter | Auto-instrumentation, log correlation, OTLP export |
| [Auto-Configuration](features/auto-configuration/) | Modular auto-configuration changes | Migration from Spring Boot 3.x |

## About

This repository serves as an index to find detailed implementations and tutorials for Spring Boot 4 features. Click on any feature above to find links to:

- Dedicated GitHub repositories with complete code
- Video tutorials
- Blog posts
- Official documentation

## Technology

- **Spring Boot**: 4.0.0-M2
- **Spring Framework**: 7
- **Java**: 24

## Author

**Dan Vega**
- Website: [danvega.dev](https://www.danvega.dev)
- YouTube: [@danvega](https://www.youtube.com/@danvega)
- GitHub: [@danvega](https://github.com/danvega)
