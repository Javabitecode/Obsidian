1. https://howtodoinjava.com/spring-webflux/webfluxtest-with-webtestclient/

# Example test
``` java
@ExtendWith(SpringExtension.class)  
@WebFluxTest(controllers = CarController.class)  
@Import(CarServiceImpl.class)  
public class CarControllerIT extends TestVariables это класс с переменными{  
    @MockBean  
    private CarRepository repository;  
    @MockBean  
    private CarMapper carMapper;  
    @MockBean  
    private CarLogOcService carLogOcService;  
    @Autowired  
    private WebTestClient webClient;  
  
    @Test  
    void testSaveCarSuccess() {  
        Mockito.when(repository.save(car)).thenReturn(carMono);  
        Mockito.when(carMapper.toCar(carDto)).thenReturn(car);  
        Mockito.when(carMapper.toCarDto(car)).thenReturn(carDto);  
  
        webClient.post()  
                .uri(PATH_CAR)  
                .contentType(MediaType.APPLICATION_JSON)  
                .bodyValue(carDto)  
                .exchange()  
                .expectStatus().isCreated();  
  
        Mockito.verify(repository, times(1)).save(car);  
        Mockito.verify(carMapper, times(1)).toCarDto(car);  
        Mockito.verify(carMapper, times(1)).toCar(carDto);  
    }  
  
    @Test  
    void testGetCarByIdSuccess() {  
        Mockito.when(repository.findById(CAR_ID)).thenReturn(carMono);  
        Mockito.when(carMapper.toCarDto(car)).thenReturn(carDto);  
  
        webClient.get()  
                .uri(PATH_CAR + SLASH + CAR_ID)  
                .accept(MediaType.APPLICATION_JSON)  
                .exchange()  
                .expectStatus().isOk()  
                .expectBody(CarDto.class)  
                .isEqualTo(carDto);  
  
        Mockito.verify(repository, times(1)).findById(CAR_ID);  
        Mockito.verify(carMapper, times(1)).toCarDto(car);  
    }  
  
    @Test  
    void testGetCarByIdThrowEx() {  
        Mockito.when(repository.findById(CAR_ID)).thenReturn(Mono.empty());  
  
        webClient.get()  
                .uri(PATH_CAR + SLASH + CAR_ID)  
                .accept(MediaType.APPLICATION_JSON)  
                .exchange()  
                .expectStatus().isBadRequest()  
                .expectBody(ApiError.class)  
                .value(ApiError::getMessage, equalTo(CAR_NOT_FOUND));  
  
        Mockito.verify(repository, times(1)).findById(CAR_ID);  
    }  
  
    @Test  
    void testAllCarsSuccess() {  
        Mockito.when(repository.findAll()).thenReturn(carFlux);  
        Mockito.when(carMapper.toCarDto(car)).thenReturn(carDto);  
  
        webClient.get()  
                .uri(PATH_CAR)  
                .accept(MediaType.APPLICATION_JSON)  
                .exchange()  
                .expectStatus().isOk()  
                .expectBodyList(CarDto.class)  
                .contains(carDto);  
  
        Mockito.verify(repository, times(1)).findAll();  
        Mockito.verify(carMapper, times(1)).toCarDto(car);  
    }  
  
    @Test  
    void testDeleteCarSuccess() {  
        Mockito.when(repository.deleteById(CAR_ID)).thenReturn(Mono.empty());  
  
        webClient.delete()  
                .uri(PATH_CAR + SLASH + CAR_ID)  
                .exchange()  
                .expectStatus().isOk();  
  
        Mockito.verify(repository, times(1)).deleteById(CAR_ID);  
    }  
  
    @Test  
    void testUpdateCarSuccess() {  
        Mockito.when(repository.findById(CAR_ID)).thenReturn(carMono);  
        Mockito.when(repository.save(car)).thenReturn(carMono);  
        Mockito.when(carMapper.toCarDto(car)).thenReturn(carDto);  
  
        webClient.put()  
                .uri(PATH_CAR + SLASH + CAR_ID)  
                .bodyValue(carDto)  
                .accept(MediaType.APPLICATION_JSON)  
                .exchange()  
                .expectStatus().isOk()  
                .expectBody(CarDto.class);  
  
        Mockito.verify(repository, times(1)).findById(CAR_ID);  
        Mockito.verify(repository, times(1)).save(car);  
        Mockito.verify(carMapper, times(1)).toCarDto(car);  
    }
}    
```