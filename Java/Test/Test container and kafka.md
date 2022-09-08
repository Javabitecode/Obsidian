```java
@Testcontainers
@SpringBootTest
@DirtiesContext
@TestInstance(Lifecycle.PER_CLASS)
public class BaseTest {
    @Autowired
    private KafkaAdmin admin;
    @MockBean
    protected CarRepository carRepository;
    protected KafkaProducer<String, Object> producer;
    protected KafkaConsumer<String, String> consumer;
    protected static final KafkaContainer kafka;
    protected static final String CAR_TEST_TOPIC = "test.car-event";
    protected static final String RESIDENT_TEST_TOPIC = "test.resident-event";
    static {
        kafka = new KafkaContainer(DockerImageName.parse("confluentinc/cp-kafka:6.2.1"));
        kafka.start();
    }
    @DynamicPropertySource
    public static void properties(DynamicPropertyRegistry registry) {
        registry.add("fixed.delay.minute.lottery", () -> String.valueOf(Long.MAX_VALUE));
        registry.add("com.zuzex.kafka.server", kafka::getBootstrapServers);
        registry.add("app.kafka-topic.car-event", () -> CAR_TEST_TOPIC);
        registry.add("app.kafka-topic"
            + ".resident-event", () -> RESIDENT_TEST_TOPIC);
    }
    @BeforeAll
    public void beforeAll(){
        // creating topics
        NewTopic carTopic =  TopicBuilder.name(CAR_TEST_TOPIC).build();
        NewTopic residentTopic =  TopicBuilder.name(RESIDENT_TEST_TOPIC).build();
        AdminClient client = AdminClient.create(admin.getConfigurationProperties());
        client.createTopics(List.of(residentTopic, carTopic));
        // config producer
        Map<String, Object> props = new HashMap<>();
        props.put(ProducerConfig.BOOTSTRAP_SERVERS_CONFIG, kafka.getBootstrapServers());
        props.put(ProducerConfig.KEY_SERIALIZER_CLASS_CONFIG, StringSerializer.class);
        props.put(ProducerConfig.VALUE_SERIALIZER_CLASS_CONFIG, JsonSerializer.class);
        producer = new KafkaProducer<>(props);
        // config consumer
        Map<String, Object> consumerProperties = Map.of(
            ConsumerConfig.BOOTSTRAP_SERVERS_CONFIG, kafka.getBootstrapServers(),
            ConsumerConfig.GROUP_ID_CONFIG, "test.group",
            ConsumerConfig.KEY_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class,
            ConsumerConfig.VALUE_DESERIALIZER_CLASS_CONFIG, StringDeserializer.class,
            ConsumerConfig.AUTO_OFFSET_RESET_CONFIG, "earliest");
        consumer = new KafkaConsumer<>(consumerProperties);
        consumer.subscribe(List.of(CAR_TEST_TOPIC, RESIDENT_TEST_TOPIC));
    }
    @BeforeEach
    public void beforeEach(){
        Mockito.reset(carRepository);
    }
    protected ResidentEvent generateResidentEvent(Long residentId, Event event){
        return ResidentEvent.builder()
            .event(event)
            .id(residentId)
            .residentDto(ResidentDto.builder()
                .gender(Gender.MALE)
                .firstName("FistName")
                .secondName("SecondName")
                .workingPlace(null)
                .build())
            .build();
    }
}

```