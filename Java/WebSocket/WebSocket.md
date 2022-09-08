# Example

``` java
@Configuration
@RequiredArgsConstructor
public class ClientWebSocketConfig {
    private final AppProperties properties;
    @Bean
    public WebSocketStompClient webSocketStompClient(StompSessionHandler sessionHandler) {
        WebSocketStompClient webSocketStompClient = new WebSocketStompClient(
            new StandardWebSocketClient());
        webSocketStompClient.setMessageConverter(new CompositeMessageConverter(List.of(
            new StringMessageConverter(), new MappingJackson2MessageConverter(),
            new SimpleMessageConverter())));
        webSocketStompClient.connect(properties.getUrl().getOc(), sessionHandler);
        return webSocketStompClient;
    }
}
```

```java
@Component
@RequiredArgsConstructor
@Slf4j
public class WebSocketTemplate {
    private final SessionProvider sessionProvider;
    @Async
    public synchronized void send(String destination, Object object) {
        sessionProvider.getSession().send(destination, object);
    }
}
-------------
public interface SessionProvider {
    StompSession getSession();
}
```

```java
@Component
@Slf4j
@RequiredArgsConstructor
public class ClientStompSessionHandler extends StompSessionHandlerAdapter implements SessionProvider {
    private StompSession stompSession;
    @Lazy
    private final WebSocketStompClient webSocketStompClient;
    @Override
    public void afterConnected(StompSession session, StompHeaders headers) {
        log.info("Client connected: headers {}", headers);
        session.subscribe("/queue/request", this);
        this.stompSession = session;
        String message = "one-time message from car service";
        log.info("Client sends: {}", message);
        session.send("/app/request", message);
    }
    @Override
    public void handleFrame(StompHeaders headers, Object payload) {
        log.info("Client received: payload {}, headers {}", payload, headers);
    }
    @Override
    public void handleException(StompSession session, StompCommand command,
        StompHeaders headers, byte[] payload, Throwable exception) {
        log.error("Client error: exception {}, command {}, payload {}, headers {}",
            exception.getMessage(), command, payload, headers);
    }
    @Override
    public void handleTransportError(StompSession session, Throwable exception) {
        webSocketStompClient.connect("ws://localhost:8093/oc", this)
            .addCallback(x -> log.info("Connection opens"),
                ex -> log.error("Client transport error: error {}", exception.getMessage()));
    }
    @Override
    public StompSession getSession() {
        if (stompSession == null) {
            throw new WebSocketNoConnectionException();
        }
        return stompSession;
    }
}
```

```java
	//для отправки сообщения
    @Override
    public void logEvent(Object ob, String command) {
        webSocketTemplate.send(LOGS.concat(CAR).concat(command), ob.toString());
    }
```