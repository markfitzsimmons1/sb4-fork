# Modularization of Spring Boot

Spring Boot 4 introduces a **modular auto-configuration architecture** - splitting the previously monolithic
`spring-boot-autoconfigure` JAR into smaller, focused modules. This is a breaking change that affects how
dependencies are resolved during migration.

## Overview

When Spring Boot started, the auto-configuration JAR was around 185KB. Over time, it ballooned to 2MB+ containing
auto-configuration for everything: Kafka, Security, MongoDB, Flyway, JPA, and more. Most applications only use
a fraction of these.

In Spring Boot 4, the team split this into modular components. You now only get the auto-configuration for
dependencies you explicitly include.

### Key Concepts

- **Breaking change**: Features that "just worked" before now require explicit dependencies
- **Smaller footprint**: Only include auto-configuration you actually use
- **Explicit is better**: Clearer dependency management
- **Starter changes**: `spring-boot-starter-web` is now `spring-boot-starter-webmvc`
- **Escape hatch available**: `spring-boot-autoconfigure-classic` restores 3.x behavior

## Architecture Comparison

![Spring Boot 4 Modularization](modular_spring_boot_4.png)

### Spring Boot 3.x Architecture

```
Your Application (pom.xml)
    ├── spring-boot-starter-web
    ├── spring-boot-starter-jdbc
    └── h2 (raw dependency)
            │
            ▼
    spring-boot-autoconfigure (one giant JAR)
        ├── Web, JPA, H2 Console, Flyway
        ├── Kafka, Security, MongoDB...
        └── Everything else
```

**How it worked**: All auto-configuration lived in one JAR. With JDBC, Web, and H2 on your classpath,
the H2 Console endpoint was automatically enabled - no extra dependencies needed.

### Spring Boot 4.x Architecture

```
Your Application (pom.xml)
    ├── spring-boot-starter-webmvc
    ├── spring-boot-starter-jdbc
    └── h2 (raw dependency) ← Problem!
            │
            ▼
    spring-boot-webmvc (modular)
    spring-boot-jdbc (modular)
    spring-boot-h2-console ← NOT on classpath!
```

**Why it breaks**: The auto-configuration code is now modular. You have the H2 dependency, but the
auto-configuration for H2 Console lives in `spring-boot-h2-console`, which isn't on your classpath.

## Migration Solutions

### The Fix: Use the Starter

Replace the raw H2 dependency with the starter that includes both the library AND its auto-configuration:

```xml
<!-- Before (3.x) - raw dependency -->
<dependency>
    <groupId>com.h2database</groupId>
    <artifactId>h2</artifactId>
    <scope>runtime</scope>
</dependency>

<!-- After (4.x) - use the starter -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-h2-console</artifactId>
</dependency>
```

### The Escape Hatch

If you want to migrate quickly and maintain 3.x behavior, add the classic auto-configure dependency:

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-autoconfigure-classic</artifactId>
</dependency>
```

This bundles all auto-configuration modules at once, like the old giant JAR. You can then gradually
migrate to specific starters over time.

## Common Migration Issues

| Missing Feature | Solution |
|-----------------|----------|
| H2 Console not working | Use `spring-boot-starter-h2-console` |
| RestClient not configured | Pick an HTTP client from start.spring.io |
| Flyway not running | Include appropriate Flyway starter |
| Web starter renamed | Use `spring-boot-starter-webmvc` instead of `spring-boot-starter-web` |

## Resources

- [Modularizing Spring Boot (Spring Blog)](https://spring.io/blog/2025/10/28/modularizing-spring-boot)
- [Spring Boot 4.0 Migration Guide - Module Dependencies](https://github.com/spring-projects/spring-boot/wiki/Spring-Boot-4.0-Migration-Guide#module-dependencies)
