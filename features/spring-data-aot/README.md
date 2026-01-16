# Spring Data AOT Repositories

Spring Data AOT moves query parsing and repository generation to build time for faster startup and earlier error
feedback.

## Overview

Ahead-of-Time processing generates repository implementations during the build, reducing reflection and improving
cold-start performance. It also surfaces invalid repository methods sooner.

## Key Concepts

- **Compile-time query generation**: SQL produced during the build
- **Build-time validation**: Catch invalid property names early
- **Faster startup**: Less reflection and parsing at runtime
- **Lower memory usage**: Pre-compiled implementations
- **Native image ready**: Predictable code paths for GraalVM
- **Inspectable output**: Generated sources in `target/spring-aot/`

## Example

### Derived Queries
```java
public interface CoffeeRepository extends CrudRepository<Coffee, Long> {

    List<Coffee> findByNameContainingIgnoreCase(String name);

    List<Coffee> findByRoastLevelAndOrigin(String roastLevel, String origin);

    @Query("SELECT * FROM coffee WHERE price < :maxPrice ORDER BY price")
    List<Coffee> findAffordableCoffees(BigDecimal maxPrice);
}
```

### Complex Query
```java
public interface OrderRepository extends CrudRepository<Order, Long> {

    List<Order> findByOrderDateAfter(LocalDateTime date);

    @Query("""
        SELECT o.* FROM orders o
        JOIN order_item oi ON o.id = oi.order_id
        JOIN coffee c ON oi.coffee_id = c.id
        WHERE c.name = :coffeeName
        """)
    List<Order> findOrdersContainingCoffee(String coffeeName);
}
```

### Validation Test (Recommended)
```java
@SpringBootTest
class AotRepositoryValidationTest {

    @Autowired
    private ApplicationContext context;

    @Test
    void validateAllRepositoryMethodsAreAotProcessed() {
        // Compare repository methods against AOT metadata
        // Fail the build when a method falls back to runtime processing
    }
}
```

## Maven Setup

```xml
<plugin>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-maven-plugin</artifactId>
    <executions>
        <execution>
            <id>process-aot</id>
            <goals>
                <goal>process-aot</goal>
            </goals>
        </execution>
    </executions>
</plugin>
```

## Build Output

```bash
./mvnw clean package
ls target/spring-aot/main/sources/
```

## Known Validation Gap

The AOT processor logs errors for invalid methods (for example `findByNamme`) but does not fail the build. A
validation test that compares repository methods against AOT metadata closes this gap.

## Resources

- GitHub: https://github.com/markfitzsimmons1/spring-data-aot
- Video: https://youtu.be/s_kmDbitE8s
- Blog: https://www.danvega.dev/blog/spring-data-aot-repositories
- Docs: https://docs.spring.io/spring-data/commons/reference/aot.html
