# Spring Boot 4 Feature Discovery

A discovery hub for exploring new features in **Spring Boot 4** and **Spring Framework 7**. 
Each feature has its own dedicated repository with complete implementations, tutorials, and documentation.

## Features

| Feature | Description | Details |
|---------|-------------|---------|
| [Null Safety](src/main/java/dev/danvega/sb4/null_safety/) | JSpecify annotations for compile-time null safety | Package-level `@NullMarked` annotation |
| [HTTP Interface Clients](src/main/java/dev/danvega/sb4/http_interface_clients/) | Declarative HTTP clients with `@HttpExchange` | Replace RestTemplate/WebClient boilerplate |
| [Bean Registration](src/main/java/dev/danvega/sb4/bean_registration/) | Programmatic bean registration | `BeanRegistrar` interface |
| [API Versioning](src/main/java/dev/danvega/sb4/api_versioning/) | First-class API versioning support | Media type parameter versioning |
| [JMS Client](src/main/java/dev/danvega/sb4/jms_client/) | JMS messaging with Apache Artemis | Producer/Consumer patterns |
| [Resilience](src/main/java/dev/danvega/sb4/resilience/) | Built-in resilience without external dependencies | `@Retryable` and `@ConcurrencyLimit` |
| [Jackson 3](src/main/java/dev/danvega/sb4/jackson3/) | Jackson 3 JSON processing support | ISO-8601 defaults, `hint()` method, JSON Views |
| [REST Test Client](src/main/java/dev/danvega/sb4/rest_test_client/) | Unified REST API testing | One API for unit, integration, and E2E tests |
| [Spring Data AOT](src/main/java/dev/danvega/sb4/spring_data_aot/) | Ahead-of-time repository compilation | 50-70% faster startup, compile-time validation |
| [OpenTelemetry](src/main/java/dev/danvega/sb4/otel/) | Official observability starter | Auto-instrumentation, log correlation, OTLP export |
| [Auto-Configuration](src/main/java/dev/danvega/sb4/auto_configuration/) | Modular auto-configuration changes | Migration from Spring Boot 3.x |

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
