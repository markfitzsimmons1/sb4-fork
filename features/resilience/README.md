# Core Resilience Features

Spring Framework 7 adds built-in resilience annotations so you can retry and limit concurrency without external
libraries.

## Overview

`@Retryable` and `@ConcurrencyLimit` provide essential resilience patterns directly in the framework. You can
apply them to any method and tune backoff or concurrency to protect downstream services.

## Key Concepts

- **Zero external dependencies**: Built into Spring Framework 7
- **`@Retryable`**: Automatic retries with backoff and jitter
- **`@ConcurrencyLimit`**: Cap concurrent executions
- **Exponential backoff**: 500ms, 1s, 2s, 4s...
- **Jitter**: Avoid thundering-herd behavior

## Example

### Retry with Backoff
```java
@Retryable(
    maxAttempts = 4,
    delay = 500,
    multiplier = 2.0,
    maxDelay = 5000,
    jitter = 100
)
public String fetchData(String id) {
    // Method that might fail
}
```

### Concurrency Limit
```java
@ConcurrencyLimit(2)
public String performHeavyOperation(String taskId) {
    // Only 2 of these can run at a time
}
```

### Combined Usage
```java
@ConcurrencyLimit(1)
@Retryable(maxAttempts = 2, delay = 1000)
public String criticalOperation(String operationId) {
}
```

### Configuration
```java
@Configuration
@EnableResilientMethods
public class ResilienceConfig {
}
```

## Resources

- GitHub: https://github.com/markfitzsimmons1/quick-bytes
- Video: https://youtu.be/CT1wGTwOfg0
- Blog: https://www.danvega.dev/blog/spring-boot-4-native-retry-support
- Docs: https://docs.spring.io/spring-framework/reference/core/resilience.html
