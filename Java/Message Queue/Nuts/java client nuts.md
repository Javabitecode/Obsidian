https://github.com/nats-io/nats.java/blob/main/src/examples/java/io/nats/examples/README.md#other-examples -> классы java-client для Nats

https://github.com/nats-io/nats.java -> Ниже есть все взаимодействие от коннекта до отправки сообщения.

## Рабочий вариант:
https://www.vinsguru.com/nats-messaging-cloud-native/
Самое главное => рабочие сервер docker) 
```
docker run -p 4222:4222 nats:alpine
```

```java
try(Connection nats = Nats.connect()) {  
    Dispatcher dispatcher = nats.createDispatcher(msg -> {  
    });  
    nats.request("nats.demo.service", "Hello From sender".getBytes())  
            .thenApply(Message::getData)  // gets executed when we get response from receiver  
            .thenApply(String::new)  
            .thenAccept(s -> System.out.println("Response from Receiver: " + s));  
    // nats.publish("nats.demo.service", "Hello NATS three".getBytes());  
} catch (IOException | InterruptedException e) {  
    throw new RuntimeException(e);  
}
```

```java
Connection nats = Nats.connect();  
  
Dispatcher dispatcher = nats.createDispatcher(msg -> {  
});  
dispatcher.subscribe("nats.demo.service", msg -> {  
            System.out.println("Received : " + new String(msg.getData()));  
            nats.publish(msg.getReplyTo(), "Hello from Publisher!".getBytes());  
        });
```

# Example from my dev (reply):
### Producer:
``` java
import com.fasterxml.jackson.core.JsonProcessingException;  
import com.fasterxml.jackson.databind.ObjectMapper;  
import com.zuzex.collector.dto.CommonCarDto;  
import com.zuzex.collector.service.NatsProducerService;  
import io.nats.client.Connection;  
import io.nats.client.Message;  
import io.nats.client.Nats;  
import lombok.RequiredArgsConstructor;  
import lombok.extern.slf4j.Slf4j;  
import org.springframework.stereotype.Service;  
  
import java.io.IOException;  
  
@Slf4j  
@Service  
@RequiredArgsConstructor  
public class NatsProducerServiceImpl implements NatsProducerService {  
    private static final String TOPIC_CAR = "nats.car.service";  
    private final ObjectMapper mapper;  
  
    @Override  
    public CommonCarDto sendMessageCar(CommonCarDto commonCarDto) {  
        try (Connection nats = Nats.connect()) {  
            String sendObject = mapper.writeValueAsString(commonCarDto);  
            return nats.request(TOPIC_CAR, sendObject.getBytes())  
                    .thenApply(Message::getData)  
                    .thenApply(String::new)  
                    .thenApply(str -> {  
                        try {  
                            log.info("Object from nats:{}", str);  
                            return mapper.readValue(str, CommonCarDto.class);  
                        } catch (JsonProcessingException e) {  
                            throw new RuntimeException(e);  
                        }  
                    }).join();  
        } catch (IOException | InterruptedException e) {  
            throw new RuntimeException(e);  
        }  
    }  
}
```

### Consumer:
```java
import com.fasterxml.jackson.databind.ObjectMapper;  
import com.zuzex.car.dto.CommonCarDto;  
import io.nats.client.Connection;  
import io.nats.client.Dispatcher;  
import io.nats.client.Message;  
import io.nats.client.Nats;  
import lombok.RequiredArgsConstructor;  
import lombok.extern.slf4j.Slf4j;  
import org.springframework.boot.context.event.ApplicationReadyEvent;  
import org.springframework.context.event.EventListener;  
import org.springframework.stereotype.Service;  
  
import java.io.IOException;    
import static com.zuzex.car.constants.KafkaConstant.TOPIC_CAR;

@Service  
@Slf4j  
@RequiredArgsConstructor  
public class NatsConsumer {  
    private final NatsHandler natsHandler;  
    private final ObjectMapper objectMapper;  
    private Connection connection;  
  
    @EventListener(ApplicationReadyEvent.class)  
    public void NatsSubscribe() {  
        getConnectionNats();  
        Dispatcher dispatcher = connection.createDispatcher(msg -> {  
        });  
        dispatcher.subscribe(TOPIC_CAR, this::NatsReplyListener);  
    }  
  
    private void NatsReplyListener(Message msg) {  
        try {  
            CommonCarDto fromPublisher = objectMapper.readValue(msg.getData(), CommonCarDto.class);  
            log.info("Received msg : {}", fromPublisher);  
            CommonCarDto replyObject = natsHandler.natsHandler(fromPublisher);  
            String out = objectMapper.writeValueAsString(replyObject);  
            connection.publish(msg.getReplyTo(), out.getBytes());  
        } catch (IOException e) {  
            throw new RuntimeException(e);  
        }  
    }  
  
    public void getConnectionNats() {  
        if (connection == null) {  
            try {  
                connection = Nats.connect();  
            } catch (IOException | InterruptedException e) {  
                throw new RuntimeException(e);  
            }  
        }  
    }  
}
```