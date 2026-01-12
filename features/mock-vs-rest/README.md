# MockMvcTester vs RestTestClient

Spring Framework 7 offers two modern controller testing styles: `MockMvcTester` for server-side inspection and
`RestTestClient` for a single API across mock and real HTTP layers.

## Overview

This demo contrasts the two approaches so you can choose the right tool for each test. Both provide fluent
AssertJ-style assertions and JSON path support.

## Key Concepts

- **MockMvcTester**: Server-side testing with handler inspection
- **RestTestClient**: Single API for mock and real HTTP
- **Binding options**: Controller, MockMvc, context, server
- **AssertJ integration**: Fluent assertions out of the box
- **Content flexibility**: Works with any HttpMessageConverter

## Example

### MockMvcTester - GET Request
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
}
```

### RestTestClient - GET Request
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
}
```

### RestTestClient Binding Options
```java
RestTestClient.bindToController(new BookController()).build();
RestTestClient.bindTo(mockMvc).build();
RestTestClient.bindToApplicationContext(context).build();
RestTestClient.bindToServer("http://localhost:8080").build();
```

## When to Use Each

| Scenario | Recommended Tool |
|----------|------------------|
| Handler inspection or exceptions | MockMvcTester |
| File uploads / multipart | MockMvcTester |
| Shared tests across mock and real HTTP | RestTestClient |
| Multiple content types | RestTestClient |

## Resources

- GitHub: https://github.com/danvega/mock-vs-rest
- Video: https://youtu.be/xWcqvrpj2PM
- Blog: https://www.danvega.dev/blog/mock-vs-rest
- MockMvcTester: https://docs.spring.io/spring-framework/reference/testing/spring-mvc-test-framework/server-performing-requests.html
- RestTestClient: https://docs.spring.io/spring-framework/reference/testing/spring-mvc-test-framework/vs-end-to-end-integration-tests.html
