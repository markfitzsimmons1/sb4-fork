# JSpecify Null Safety

Spring Framework 7 embraces JSpecify annotations so you can enforce null-safety at compile time with better IDE hints.

## Overview

With JSpecify, you can mark entire packages as non-null by default and explicitly allow nulls where needed. This
reduces runtime null pointer errors and improves API clarity.

## Key Concepts

- **Package-level `@NullMarked`**: Non-null by default
- **`@Nullable` escapes**: Explicitly mark nullable parameters and fields
- **Compile-time safety**: Catch errors earlier
- **IDE support**: Better warnings and autocomplete
- **Kotlin-friendly**: Interoperates cleanly with Kotlin nullability

## Example

### Mark a Package as Non-Null
```java
// package-info.java
@NullMarked
package dev.danvega.demo;

import org.jspecify.annotations.NullMarked;
```

### Explicit Nullable Parameter
```java
public class UserService {

    public User findById(Long id) {
        return userRepository.findById(id)
            .orElseThrow(() -> new UserNotFoundException(id));
    }

    public List<User> search(@Nullable String name) {
        if (name == null) {
            return userRepository.findAll();
        }
        return userRepository.findByNameContaining(name);
    }
}
```

## Resources

- GitHub: https://github.com/danvega/coffeeshop
- Video: https://youtu.be/QlGnaRoujL8
- Blog: https://www.danvega.dev/blog/spring-boot-4-null-safety
- JSpecify: https://jspecify.dev/
- Docs: https://docs.spring.io/spring-framework/reference/core/null-safety.html
