# JMS Client API

Spring Framework 6.1+ introduces `JmsClient`, a fluent, type-safe alternative to `JmsTemplate`.

## Overview

`JmsClient` simplifies JMS messaging with a readable API, automatic conversion, and flexible QoS configuration.
This demo highlights common messaging patterns in an order-processing workflow.

## Key Concepts

1. **Basic send**: Fire-and-forget delivery
2. **Quality of service**: Priority, TTL, delivery delay
3. **Custom headers**: Metadata for routing and filtering
4. **Synchronous receive**: Pull messages with timeout
5. **Receive and convert**: Type-safe payloads
6. **Request-reply**: RPC-style communication
7. **Reusable operations**: Preconfigured handles for performance

## Example

### Basic Send
```java
@Service
public class OrderMessagingService {

    private final JmsClient jmsClient;

    public OrderMessagingService(JmsClient jmsClient) {
        this.jmsClient = jmsClient;
    }

    public void sendOrder(Order order) {
        jmsClient.send("orders.queue")
            .withBody(order);
    }
}
```

### Quality of Service
```java
public void sendPriorityOrder(Order order) {
    jmsClient.send("orders.queue")
        .withPriority(9)
        .withTimeToLive(Duration.ofMinutes(5))
        .withBody(order);
}
```

### Request-Reply
```java
public OrderConfirmation processOrder(Order order) {
    return jmsClient.requestAndReceive("orders.queue")
        .withBody(order)
        .convertTo(OrderConfirmation.class);
}
```

### Consumer
```java
@Component
public class OrderConsumer {

    @JmsListener(destination = "orders.queue")
    public void processOrder(Order order) {
        // Process the order
    }
}
```

### Run with Docker
```bash
docker-compose up -d
./mvnw spring-boot:run
```

## Resources

- GitHub: https://github.com/markfitzsimmons1/jms-orders
- Video: https://youtu.be/91xVrWlzIe4
- Blog: https://www.danvega.dev/blog/jms-client
- Docs: https://docs.spring.io/spring-boot/reference/messaging/jms.html
- Artemis: https://activemq.apache.org/components/artemis/
