1. https://www.baeldung.com/reactive-streams-step-verifier-test-publisher

# Example test

``` java
@ExtendWith(SpringExtension.class)  
@WebFluxTest(controllers = CarController.class)  
public class CarControllerTest extends TestVariables - класс с перменными {  
    @Autowired  
    private BusinessExceptionHandler exceptionHandler;  
    @Autowired  
    private CarController carController;  
    @MockBean  
    private CarService carService;  
  
    @BeforeAll  
    public static void init() {  
  
    }  
  
    @Test  
    public void testGetCarByIdSuccess() {  
        when(carService.getCarById(CAR_ID)).thenReturn(carDtoMono);  
        WebTestClient.bindToController(carController).build()  
                .get()  
                .uri(PATH_CAR + SLASH + CAR_ID)  
                .accept(MediaType.APPLICATION_JSON)  
                .exchange()  
                .expectStatus().isOk()  
                .expectBody(CarDto.class)  
                .consumeWith(serverResponse ->  
                        assertNotNull(serverResponse.getResponseBody()))  
                .value(cars -> cars, equalTo(carDto));  
    }  
  
    @Test  
    public void testGetCarByIdBadRequest() {  
        when(carService.getCarById(CAR_ID)).thenReturn(  
                Mono.error(new EntityNotFoundException(CAR_NOT_FOUND))  
        );  
  
        WebTestClient.bindToController(carController)  
                .controllerAdvice(exceptionHandler).build()  
                .get()  
                .uri(PATH_CAR + SLASH + CAR_ID)  
                .accept(MediaType.APPLICATION_JSON)  
                .exchange()  
                .expectStatus().isBadRequest()  
                .expectBody(ApiError.class)  
                .consumeWith(serverResponse ->  
                        assertNotNull(serverResponse.getResponseBody()))
                .value(ApiError::getMessage, equalTo(CAR_NOT_FOUND));
        }
    }
```