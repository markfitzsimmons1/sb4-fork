# Jackson 3 Support

Spring Boot 4 integrates Jackson 3 with improved defaults, immutable configuration, and a cleaner `hint()` API
for JSON view filtering.

## Overview

Jackson 3 modernizes configuration and defaults (ISO-8601 dates, unchecked exceptions), while Spring Boot 4
auto-configures the new `JsonMapper` so you can adopt it with minimal changes.

## Key Concepts

- **Immutable configuration**: Builder-based setup
- **ISO-8601 defaults**: Dates serialize as strings by default
- **Unchecked exceptions**: Better use in lambdas and streams
- **`hint()` method**: Cleaner view filtering compared to `MappingJacksonValue`
- **JSON Views**: Reduce DTO duplication with `@JsonView`

## Example

### JSON View Hierarchy
```java
public class Views {
    public interface Summary {}
    public interface Public extends Summary {}
    public interface Internal extends Public {}
}
```

### Model with Views
```java
public record Donut(
    @JsonView(Views.Summary.class) Long id,
    @JsonView(Views.Summary.class) String name,
    @JsonView(Views.Public.class) String description,
    @JsonView(Views.Public.class) BigDecimal price,
    @JsonView(Views.Internal.class) Integer stockCount,
    @JsonView(Views.Internal.class) LocalDateTime createdAt
) {}
```

### Controller with Views
```java
@RestController
@RequestMapping("/api/donuts")
public class DonutController {

    @GetMapping
    @JsonView(Views.Summary.class)
    public List<Donut> getSummary() {
        return donutService.findAll();
    }

    @GetMapping("/public")
    @JsonView(Views.Public.class)
    public List<Donut> getPublic() {
        return donutService.findAll();
    }

    @GetMapping("/internal")
    @JsonView(Views.Internal.class)
    public List<Donut> getInternal() {
        return donutService.findAll();
    }
}
```

### Configuration Properties
```properties
spring.jackson.use-jackson2-defaults=false
spring.jackson.serialization.indent-output=true
```

## What's New in Jackson 3

| Feature | Jackson 2 | Jackson 3 |
|---------|-----------|-----------|
| Date format | Numeric timestamp | ISO-8601 string |
| Exceptions | Checked | Unchecked |
| Configuration | Mutable | Immutable builders |
| Packages | `com.fasterxml` | `tools.jackson` |

## Resources

- GitHub: https://github.com/danvega/donut-shop
- Video: https://youtu.be/4cvP_qroLH4
- Blog: https://www.danvega.dev/blog/jackson-3-spring-boot-4
- Docs: https://spring.io/blog/2025/10/07/introducing-jackson-3-support-in-spring
- Jackson: https://github.com/FasterXML/jackson
