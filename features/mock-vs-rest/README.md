# MockMvcTester vs RestTestClient

Spring Framework 7 provides two powerful approaches for testing REST controllers: **MockMvcTester** for server-side
testing with AssertJ integration, and **RestTestClient** for a unified API that works across mock and real HTTP scenarios.

## Overview

This demo compares MockMvcTester and RestTestClient side-by-side, helping you understand when to use each approach.
Both tools are part of Spring Framework 7's modernized testing infrastructure, offering fluent APIs and improved
developer experience for controller testing.

### Key Concepts

- **MockMvcTester**: Server-side testing with native AssertJ integration and handler inspection
- **RestTestClient**: Unified API that works across mock containers and real HTTP servers
- **Binding Flexibility**: RestTestClient supports multiple binding modes (controller, mockMvc, context, server)
- **AssertJ Integration**: Both tools leverage AssertJ for fluent, readable assertions
- **JSON Path Support**: Extract and assert on JSON response content with ease
- **Content Type Flexibility**: RestTestClient supports any HttpMessageConverter, not just JSON

## Resources

### GitHub Repository
https://github.com/danvega/mock-vs-rest

### Video Tutorial
https://youtu.be/xWcqvrpj2PM

### Blog Post
https://www.danvega.dev/blog/mock-vs-rest

### Official Documentation
- [MockMvcTester Reference](https://docs.spring.io/spring-framework/reference/testing/spring-mvc-test-framework/server-performing-requests.html)
- [RestTestClient Reference](https://docs.spring.io/spring-framework/reference/testing/spring-mvc-test-framework/vs-end-to-end-integration-tests.html)

## Example Usage

### MockMvcTester - Basic GET Request
```java
@WebMvcTest(BookController.class)
class BookControllerMockMvcTesterTest {

    @Autowired
    private MockMvcTester mockMvcTester;

    @Test
    void shouldReturnBook() {
        assertThat(mockMvcTester.get().uri("/api/books/1"))
            .hasStatusOk()
            .bodyJson()
            .extractingPath("$.title")
            .isEqualTo("Clean Code");
    }

    @Test
    void shouldReturnAllBooks() {
        assertThat(mockMvcTester.get().uri("/api/books"))
            .hasStatusOk()
            .bodyJson()
            .extractingPath("$.length()")
            .isEqualTo(3);
    }
}
```

### RestTestClient - Basic GET Request
```java
@WebMvcTest(BookController.class)
class BookControllerRestTestClientTest {

    @Autowired
    private MockMvc mockMvc;

    private RestTestClient client;

    @BeforeEach
    void setUp() {
        client = RestTestClient.bindTo(mockMvc).build();
    }

    @Test
    void shouldReturnBook() {
        client.get().uri("/api/books/1")
            .exchange()
            .expectStatus().isOk()
            .expectBody()
            .jsonPath("$.title").isEqualTo("Clean Code");
    }

    @Test
    void shouldReturnAllBooks() {
        client.get().uri("/api/books")
            .exchange()
            .expectStatus().isOk()
            .expectBody()
            .jsonPath("$.length()").isEqualTo(3);
    }
}
```

### RestTestClient Binding Options
```java
// Unit test - bind directly to controller
RestTestClient.bindToController(new BookController()).build();

// Slice test - bind to MockMvc
RestTestClient.bindTo(mockMvc).build();

// Integration test - bind to application context
RestTestClient.bindToApplicationContext(context).build();

// E2E test - bind to running server
RestTestClient.bindToServer("http://localhost:8080").build();
```

## When to Use Each

| Scenario | Recommended Tool |
|----------|------------------|
| File uploads / multipart requests | MockMvcTester |
| Server-side handler inspection | MockMvcTester |
| Need to inspect exceptions | MockMvcTester |
| Multiple content types (XML, etc.) | RestTestClient |
| Same tests for mock and real HTTP | RestTestClient |
| Typed response body handling | RestTestClient |
| Fine-grained HttpServletRequest access | MockMvcTester |

## Running Tests
```bash
# Run all tests
./mvnw test

# Run MockMvcTester tests only
./mvnw test -Dtest=BookControllerMockMvcTesterTest

# Run RestTestClient tests only
./mvnw test -Dtest=BookControllerRestTestClientTest

# Run application
./mvnw spring-boot:run
```
