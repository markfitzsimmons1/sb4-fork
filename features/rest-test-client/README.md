# REST Test Client

Spring Framework 7 introduces `RestTestClient`, a single API for unit, slice, integration, and end-to-end REST tests.

## Overview

`RestTestClient` replaces `MockMvc`, `WebTestClient`, and `TestRestTemplate` switches with one fluent API that can bind
to a controller, MockMvc, the application context, or a live server.

## Key Concepts

1. **bindToController**: Unit testing without Spring context
2. **bindToMockMvc**: MVC features such as validation and security
3. **bindToApplicationContext**: Full application context testing
4. **bindToServer**: Full HTTP stack end-to-end tests
5. **bindToRouterFunction**: Functional endpoints

## Example

### bindToController (Unit Test)
```java
@ExtendWith(MockitoExtension.class)
class TodoControllerTest {

    @Mock
    private TodoService todoService;

    private RestTestClient client;

    @BeforeEach
    void setUp() {
        client = RestTestClient.bindToController(new TodoController(todoService)).build();
    }

    @Test
    void shouldGetAllTodos() {
        when(todoService.findAll()).thenReturn(List.of(
            new Todo(1L, "Learn Spring", false)
        ));

        client.get()
            .uri("/api/todos")
            .exchange()
            .expectStatus().isOk()
            .expectBody()
            .jsonPath("$[0].title").isEqualTo("Learn Spring");
    }
}
```

### bindToMockMvc (Slice Test)
```java
@WebMvcTest(TodoController.class)
class TodoControllerMvcTest {

    @Autowired
    private MockMvc mockMvc;

    @MockBean
    private TodoService todoService;

    @Test
    void shouldValidateInput() {
        RestTestClient client = RestTestClient.bindToMockMvc(mockMvc).build();

        client.post()
            .uri("/api/todos")
            .contentType(MediaType.APPLICATION_JSON)
            .bodyValue(new Todo(null, "", false))
            .exchange()
            .expectStatus().isBadRequest();
    }
}
```

### bindToServer (E2E)
```java
@SpringBootTest(webEnvironment = SpringBootTest.WebEnvironment.RANDOM_PORT)
class TodoIntegrationTest {

    @LocalServerPort
    private int port;

    @Test
    void shouldCreateAndRetrieveTodo() {
        RestTestClient client = RestTestClient.bindToServer()
            .baseUrl("http://localhost:" + port)
            .build();

        client.post()
            .uri("/api/todos")
            .contentType(MediaType.APPLICATION_JSON)
            .bodyValue(new Todo(null, "Integration test", false))
            .exchange()
            .expectStatus().isCreated();
    }
}
```

## Test Pyramid Guidance

| Test Type | Approach | Speed | Realism |
|-----------|----------|-------|---------|
| Unit | bindToController | Fast | Low |
| Integration | bindToMockMvc / bindToApplicationContext | Medium | Medium |
| E2E | bindToServer | Slow | High |

## Resources

- GitHub: https://github.com/markfitzsimmons1/rest-test-client
- Video: https://youtu.be/dPM8n0uNhes
- Blog: https://www.danvega.dev/blog/spring-framework-7-rest-test-client
- Docs: https://docs.spring.io/spring-framework/reference/testing/resttestclient.html
