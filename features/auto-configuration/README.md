# Modular Auto-Configuration

Spring Boot 4 splits auto-configuration into focused modules. This reduces footprint but requires you to include
specific auto-config modules or starters for the features you use.

## Overview

In Spring Boot 3.x, auto-configuration lived in one large JAR. In Spring Boot 4, each capability lives in its own
module. If a feature is missing, you add its module explicitly or use the classic compatibility artifact to restore
3.x behavior during migration.

## Key Concepts

- **Breaking change**: Some features no longer appear just because a library is on the classpath
- **Modular approach**: Add only the modules you need
- **Classic compatibility**: `spring-boot-autoconfigure-classic` restores legacy behavior
- **Smaller footprint**: Less classpath scanning and fewer auto-configs
- **Explicit dependencies**: Clear ownership of features

## Example

### Quick Migration (Classic Module)
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-autoconfigure-classic</artifactId>
</dependency>
```

### Modular Setup (Preferred)
```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-restclient</artifactId>
</dependency>

<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-autoconfigure-data-jpa</artifactId>
</dependency>
```

### Example Failure and Fix
```text
Symptom: RestClient bean is missing after upgrade to Boot 4
Fix: Add spring-boot-starter-restclient
```

## Common Migration Issues

| Missing Feature | Add This Dependency |
|-----------------|---------------------|
| RestClient | `spring-boot-starter-restclient` |
| JPA/Hibernate | `spring-boot-autoconfigure-data-jpa` |
| Security | `spring-boot-autoconfigure-security` |

## Resources

- Video: https://youtu.be/kTLuhE7_jGU
- Blog: https://www.danvega.dev/blog/spring-boot-4-modularization
- Docs: https://github.com/spring-projects/spring-boot/wiki/Spring-Boot-4.0-Migration-Guide#classic-starters
