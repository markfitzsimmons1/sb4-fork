# API Versioning

Spring Framework 7 adds first-class API versioning via the `version` attribute on request mappings, so you can evolve
endpoints without breaking older clients.

## Overview

API versioning keeps legacy clients working while new versions ship side-by-side. Spring 7 supports multiple
strategies (such as media type parameters) and includes RFC-compliant deprecation headers to guide clients forward.

## Key Concepts

- **Media type parameter versioning**: `Accept: application/json;version=1.0`
- **Versioned mappings**: `@GetMapping(version = "1.0")`
- **Deprecation headers**: Deprecation, Sunset, Link
- **Backward compatibility**: Serve V1 and V2 in the same controller
- **Central configuration**: `WebMvcConfigurer#configureApiVersioning`

## Example

### Controller with Two Versions
```java
@RestController
@RequestMapping("/api/users")
public class UserController {

    @GetMapping(version = "1.0")
    public UserDTOv1 getUserV1(@PathVariable Long id) {
        return new UserDTOv1(id, user.getName());
    }

    @GetMapping(version = "2.0")
    public UserDTOv2 getUserV2(@PathVariable Long id) {
        return new UserDTOv2(id, user.getFirstName(), user.getLastName());
    }
}
```

### API Versioning Configuration
```java
@Configuration
public class ApiVersioningConfig implements WebMvcConfigurer {
    @Override
    public void configureApiVersioning(ApiVersionConfigurer configurer) {
        configurer.useMediaTypeParameterVersioning();
    }
}
```

### Client Requests
```bash
curl -H "Accept: application/json;version=1.0" http://localhost:8080/api/users/1
curl -H "Accept: application/json;version=2.0" http://localhost:8080/api/users/1
```

### Response Evolution
```json
{"id": 1, "name": "Dan Vega"}
```

```json
{"id": 1, "firstName": "Dan", "lastName": "Vega"}
```

## Resources

- GitHub: https://github.com/danvega/api-users
- Video: https://youtu.be/qjo2tYf01xo
- Blog: https://www.danvega.dev/blog/spring-boot-4-api-versioning
- Docs: https://docs.spring.io/spring-framework/reference/web/webmvc-versioning.html

## Related Features

- [HTTP Interface Clients](../http-interface-clients/) - Consume versioned APIs
- [Resilience](../resilience/) - Add fault tolerance to API endpoints
