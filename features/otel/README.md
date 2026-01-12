# OpenTelemetry Integration

Spring Boot 4 ships an official OpenTelemetry starter for production-ready observability with automatic
instrumentation and OTLP export.

## Overview

The starter wires Micrometer tracing to OpenTelemetry and exports telemetry through OTLP to any compatible
backend (Grafana, Jaeger, Zipkin). You get HTTP, JDBC, and log correlation out of the box.

## Key Concepts

- **Single dependency**: `spring-boot-starter-opentelemetry`
- **Automatic instrumentation**: HTTP server/client, JDBC, more
- **Log correlation**: Trace/span IDs injected into logs
- **OTLP export**: Works with any OpenTelemetry backend
- **Production ready**: Official Spring support

## Example

### Dependency
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-opentelemetry</artifactId>
</dependency>
```

### Configuration
```yaml
spring:
  application:
    name: my-service

management:
  tracing:
    sampling:
      probability: 1.0
  otlp:
    tracing:
      endpoint: http://localhost:4318/v1/traces
```

### Custom Spans with @Observed
```java
@Service
public class OrderService {

    @Observed(name = "order.process", contextualName = "processOrder")
    public Order processOrder(OrderRequest request) {
        return orderRepository.save(createOrder(request));
    }
}
```

### Controller Example
```java
@RestController
public class GreetingController {

    @GetMapping("/greet/{name}")
    public String greet(@PathVariable String name) throws InterruptedException {
        Thread.sleep(50);
        return "Hello, " + name + "!";
    }
}
```

## Automatic Instrumentation

| Component | What's Traced |
|-----------|--------------|
| HTTP Server | Incoming requests |
| RestTemplate | Outgoing HTTP |
| WebClient | Reactive HTTP |
| JDBC | SQL queries |
| Logs | Trace/span IDs |

## Resources

- GitHub: https://github.com/danvega/ot
- Video: https://www.youtube.com/watch?v=6_Y41z7OIv8
- Blog: https://www.danvega.dev/blog/opentelemetry-spring-boot
- Spring blog: https://spring.io/blog/2025/11/18/opentelemetry-with-spring-boot
- OpenTelemetry: https://opentelemetry.io/
- Micrometer Tracing: https://micrometer.io/docs/tracing
