# Spring Boot 4 Mock App Tasks (Under 10 Minutes Each)

Short, focused tasks tied to Spring Boot 4 and Spring Framework 7 changes in the mock apps.

## api-users (`api-users/README.md`)
- Add a v2 response record that splits `name` into `firstName`/`lastName` and expose it with `version="2.0"`.
- Switch the demo endpoints to header-based versioning and document the required header.
- Make versioning optional with a default latest version and verify `/demo/users/{id}` works without a version.
- Add a 400 error response for unsupported versions and test it.

## donut-shop (`donut-shop/README.md`)
- Inject the auto-configured `JsonMapper` into `DemoDonutService` and remove the manual builder instance.
- Add `@JsonView` annotations to `DemoDonut` and filter fields in the controller using a view `hint()`.
- Add an endpoint that deserializes a JSON payload into `DemoDonut` using `JsonMapper`.
- Add a test that asserts `bakedAt` serializes as ISO-8601 by default.

## jms-orders (`jms-orders/README.md`)
- Add a send method that sets TTL and priority and expose it via `/demo/orders/priority`.
- Add a correlation ID to the outgoing message and verify it on the receive path.
- Add a custom header (e.g., `region`) and surface it in a log or response.
- Add a typed `receiveAndConvert` endpoint and store the message in the repository.

## mfa (`mfa/README.md`)
- Wire `DemoMfaSuccessHandler` into `SecurityConfig` and log which factors are satisfied.
- Wire `DemoOttFailureHandler` into one-time token login and show a friendly error page.
- Use `DemoAuditService` from a success handler to record successful logins.
- Add a test that verifies `/admin/demo/status` requires both password and OTT.

## mock-vs-rest (`mock-vs-rest/README.md`)
- Add a `MockMvcTester` test for `DemoWidgetController` that asserts list size and one field.
- Add a `RestTestClient` test that posts a widget and asserts the response body.
- Add bean validation to `DemoWidget` and test a 400 response using either client.
- Add a test that verifies a missing widget returns 404 with a JSON error body.

## ot (`ot/README.md`)
- Add a child span in `DemoGatewayService.record` and include a custom attribute like `app.userId`.
- Implement `callExternal` using `RestClient` and verify a client span appears.
- Add a log line that prints trace/span IDs during `/demo/trace/{userId}`.
- Add a `@Timed` metric on one demo endpoint and verify it in Prometheus.

## quick-bytes (`quick-bytes/README.md`)
- Add `@Retryable` to `placeOrder` with exponential backoff and verify retry logs.
- Add `@ConcurrencyLimit(2)` to `notifyKitchen` and demonstrate blocked calls.
- Add a `RetryTemplate` path for `placeOrder` and compare success rates.
- Add a test that asserts concurrent requests are limited.

## rest-test-client (`rest-test-client/README.md`)
- Add a `bindToController` unit test for `DemoTodoController`.
- Add a `bindToMockMvc` test that verifies JSON validation or error handling.
- Add a `bindToServer` test that checks a response header for `/demo/todos/{id}`.
- Add a test that uses `expectBodyList` and verifies list size.

## sb4-http-interfaces (`sb4-http-interfaces/README.md`)
- Add a new `UserService` HTTP interface for `/users` and expose `/demo/users/{id}`.
- Add a `/demo/feed` endpoint that fetches a todo and post in parallel and returns a combined DTO.
- Configure a named `@ImportHttpServices(group = "jsonplaceholder", ...)` group and use it in `ModernConfig`.
- Add error handling that maps upstream 404 responses into a clean `/demo` error response.

## sb4-bean-registrar (`sb4-bean-registrar/README.md`)
- Add a new notifier type (e.g., `slack`) to `DemoNotifierRegistrar`.
- Replace the raw property lookup with a `@ConfigurationProperties` class.
- Add a fallback path that throws a custom exception with a clear message.
- Add a test that asserts the correct notifier bean based on `demo.notifier-type`.

## spring-data-aot (`spring-data-aot/README.md`)
- Add a Spring Data JDBC repository interface for `DemoRoast` and wire it into `DemoRoastService`.
- Add `demo_roast` table and sample data to `schema.sql` and `data.sql`.
- Add a derived query method and ensure it is AOT-processed.
- Update `AotRepositoryValidationTest` to validate the new repository.

## coffeeshop (`coffeeshop/README.md`)
- Add `package-info.java` with `@NullMarked` and update nullability errors in `DemoItemController` and `DemoItemService`.
- Convert `DemoItemService.findById` to return `Optional<DemoItem>` and update the controller to use `ResponseEntity.of(...)`.
- Make `promoCode` optional with `@Nullable` and handle `null` safely in `DemoItemService.applyPromo`.
- Add a value object for phone number to replace `@Nullable String phoneNumber` in `DemoCustomer`.
- Add a repository method that returns `@Nullable DemoCustomer` and a controller endpoint that safely handles the missing case.
- Enable and fix the disabled test in `DemoItemServiceTest` after implementing null-safe promo handling.
