# HTTP Interface Clients

Spring Boot 4 adds `@ImportHttpServices`, enabling zero-boilerplate declarative HTTP clients built from
`@HttpExchange` interfaces.

## Overview

HTTP Interface Clients let you model outbound HTTP calls as interfaces. Spring generates the client and wires it
automatically so you can focus on contracts, not plumbing.

## Key Concepts

- **Zero configuration**: `@ImportHttpServices` registers all clients
- **Declarative interfaces**: Define HTTP calls with `@HttpExchange`
- **Type-safe**: Compile-time signatures with records and DTOs
- **Clean architecture**: Keep interfaces near your domain or adapter layer

## Example

### Before (Manual Configuration)
```java
@Bean
public TodoService todoService(RestClient.Builder restClientBuilder) {
    var restClient = restClientBuilder
            .baseUrl("https://jsonplaceholder.typicode.com")
            .build();

    var adapter = RestClientAdapter.create(restClient);
    var factory = HttpServiceProxyFactory.builderFor(adapter).build();
    return factory.createClient(TodoService.class);
}
```

### After (Spring Boot 4)
```java
@Configuration(proxyBeanMethods = false)
@ImportHttpServices(TodoService.class)
public class HttpClientConfig {
}
```

### Interface Definition
```java
@HttpExchange(url = "https://jsonplaceholder.typicode.com", accept = "application/json")
public interface TodoService {
    @GetExchange("/todos")
    List<Todo> getAllTodos();

    @GetExchange("/todos/{id}")
    Todo getTodoById(@PathVariable Long id);

    @PostExchange("/todos")
    Todo createTodo(@RequestBody Todo todo);
}
```

## Resources

- GitHub: https://github.com/danvega/sb4-http-interfaces
- Video: https://youtu.be/TEd5e4Thu7M
- Blog: https://www.danvega.dev/blog/http-interfaces-spring-boot-4
- Docs: https://docs.spring.io/spring-framework/reference/integration/rest-clients.html#rest-http-service-client
