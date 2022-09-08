1. https://medium.com/@BPandey/writing-unit-test-in-reactive-spring-boot-application-32b8878e2f57

# Example test
```java
@ExtendWith(SpringExtension.class)  
public class CarServiceImplTest extends TestVariables - класс с перменными {  
    @Mock  
    private CarRepository carRepository;  
    @Mock  
    private CarMapper carMapper;  
    @Mock  
    private CarLogOcService carLogOcService;  
    @InjectMocks  
    CarServiceImpl carService;  
  
    @Test  
    public void testSaveCarSuccess() {  
        when(carService.saveCarBase(carDto)).thenReturn(carMono);  
        when(carMapper.toCarDto(car)).thenReturn(carDto);  
        StepVerifier  
                .create(carService.saveCar(carDto))  
                .consumeNextWith(carDb -> assertEquals(carDb, carDto))  
                .verifyComplete();  
    }  
  
    @Test  
    public void testSaveCarThrowEx() {  
        when(carService.saveCarBase(carDto)).thenReturn(carMono);  
        when(carService.saveCar(carDto)).thenReturn(  
                Mono.error(new LotteryIsNotOverYetException(LOTTERY_IS_GOING_ON_EX))  
        );  
        StepVerifier  
                .create(carService.saveCar(carDto))  
                .expectErrorMatches(ex -> ex instanceof LotteryIsNotOverYetException)  
                .verify();  
    }  
  
    @Test  
    public void testSaveCarForLotterySuccess() {  
        when(carService.saveCarBase(carDto)).thenReturn(carMono);  
        Car carFromDb = carService.saveCarForLottery(carDto);  
        assertEquals(carFromDb, car);  
    }  
  
    @Test  
    public void testGetAllCarsSuccess() {  
        when(carRepository.findAll()).thenReturn(carFlux);  
        when(carMapper.toCarDto(car)).thenReturn(carDto);  
        Flux<CarDto> carFluxDb = carService.getAllCars();  
        StepVerifier  
                .create(carFluxDb)  
                .consumeNextWith(carDb -> assertEquals(carDb, carDto))  
                .verifyComplete();  
    }  
  
    @Test  
    public void testDeleteCarSuccess() {  
        when(carRepository.deleteById(CAR_ID)).thenReturn(Mono.empty());  
        StepVerifier  
                .create(carService.deleteCar(CAR_ID))  
                .verifyComplete();  
    }
}    
```