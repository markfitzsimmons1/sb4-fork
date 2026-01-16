# Programmatic Bean Registration

Spring Framework 7 introduces `BeanRegistrar`, a concise API for registering beans programmatically at runtime.

## Overview

`BeanRegistrar` replaces the verbose `BeanDefinitionRegistryPostProcessor` pattern with a cleaner, type-safe
registration API. It works with AOT and lets you vary beans based on environment or configuration.

## Key Concepts

- **Runtime flexibility**: Register beans based on properties or environment
- **Modern API**: Lambda-friendly registration
- **Type-safe**: Compile-time checking and IDE support
- **AOT compatible**: Works with native images
- **Environment access**: Read config to drive registrations

## Example

### BeanRegistrar Implementation
```java
public class MessageServiceRegistrar implements BeanRegistrar {
    @Override
    public void register(BeanRegistry registry, Environment env) {
        String messageType = env.getProperty("app.message-type", "email");

        switch (messageType.toLowerCase()) {
            case "email" -> registry.registerBean("messageService",
                EmailMessageService.class,
                spec -> spec.description("Email service via BeanRegistrar"));
            case "sms" -> registry.registerBean("messageService",
                SmsMessageService.class,
                spec -> spec.description("SMS service via BeanRegistrar"));
        }
    }
}
```

### Import the Registrar
```java
@Configuration
@Import(MessageServiceRegistrar.class)
public class ModernConfig {
}
```

### Comparison

| Approach | Complexity | Flexibility |
|----------|------------|-------------|
| `@Component` / `@Bean` | Low | Low |
| `BeanDefinitionRegistryPostProcessor` | High | High |
| **BeanRegistrar** (Spring 7) | Low | High |

## Resources

- GitHub: https://github.com/markfitzsimmons1/sb4-bean-registrar
- Video: https://youtu.be/yh760wTFL_4
- Blog: https://www.danvega.dev/blog/programmatic-bean-registration
- Docs: https://docs.spring.io/spring-framework/reference/core/beans/java/programmatic-bean-registration.html
