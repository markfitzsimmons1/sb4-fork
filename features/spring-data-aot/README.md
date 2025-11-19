# Spring Data AOT Repositories

Spring Data AOT moves query processing from runtime to compile-time, delivering **50-70% faster startup**,
reduced memory usage, and catching repository errors before production.

## Overview

Spring Data Ahead-of-Time (AOT) compilation eliminates runtime reflection and query parsing by pre-generating
repository implementations during the build. This provides faster startup, better error detection, and
enables GraalVM native image compilation.

### Key Concepts

- **Compile-time query generation**: SQL generated during build, not at runtime
- **Early error detection**: Catch typos and invalid property references before deployment
- **50-70% faster startup**: No runtime reflection or query parsing
- **Reduced memory**: Pre-compiled implementations are more efficient
- **GraalVM ready**: Foundation for native image compilation
- **Inspectable code**: View pre-generated implementations in `target/spring-aot/`

## Resources

### GitHub Repository
https://github.com/danvega/spring-data-aot

### Video Tutorial

### Blog Post

### Official Documentation
- [Spring Data AOT](https://docs.spring.io/spring-data/commons/reference/aot.html)

## Example Usage

### Repository with Derived Queries
```java
public interface CoffeeRepository extends CrudRepository<Coffee, Long> {

    // AOT generates SQL at compile-time
    List<Coffee> findByNameContainingIgnoreCase(String name);

    List<Coffee> findByRoastLevelAndOrigin(String roastLevel, String origin);

    @Query("SELECT * FROM coffee WHERE price < :maxPrice ORDER BY price")
    List<Coffee> findAffordableCoffees(BigDecimal maxPrice);
}
```

### Repository with Temporal Queries
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

### AOT Validation Test
```java
@SpringBootTest
class AotRepositoryValidationTest {

    @Autowired
    private ApplicationContext context;

    @Test
    void validateAllRepositoryMethodsAreAotProcessed() {
        // Compares method signatures against AOT-generated metadata
        // Fails build if methods are skipped by AOT
        // Provides clear error messages for problematic methods
    }
}
```

### Build with AOT Processing
```bash
# AOT processing happens during package
./mvnw clean package

# View generated code
ls target/spring-aot/main/sources/
```

## How AOT Works

During `./mvnw clean package`:
1. AOT processor scans repository interfaces
2. Parses method names to generate SQL
3. Validates @Query annotations
4. Creates pre-compiled repository implementations
5. Generates JSON metadata for processed methods

## Benefits Summary

| Aspect | Runtime (Traditional) | AOT (Compile-time) |
|--------|----------------------|-------------------|
| Query parsing | Every startup | Once at build |
| Error detection | Runtime exceptions | Build failures |
| Startup time | Slower | 50-70% faster |
| Memory usage | Higher (reflection) | Lower (pre-compiled) |
| Native images | Complex setup | Ready to go |