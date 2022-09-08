1. https://javarush.ru/groups/posts/3698-chto-takoe-mapstruct-i-kak-praviljhno-nastroitjh-ego-dlja-moduljhnogo-testirovanija-v-springboo

## Что это такое?

Mapstruct — это библиотека, которая помогает сопоставлять (маппить, в целом, так всегда и говорят: маппить, замапить и т.д.) объекты одних сущностей в объекты других сущностей при помощи сгенерированного кода на основе конфигураций, которые описываются через интерфейсы.

## Зачем?

В большинстве своем мы разрабатываем многослойные приложения (слой работы с базой, слой бизнес логики, слой взаимодействия приложения с внешним миром) и каждый слой имеет свои объекты для хранения и обработки данных. И эти данные нужно передавать из слоя в слой путем перевода из одной сущности в другую. Для тех, кто не работал с таким подходом, это может показаться несколько сложным. Например, у нас есть сущность к базе данных Student. Когда данные этой сущности переходят в слой бизнес-логики (сервисов), нам нужно перевести данные из класса Student в класс StudentModel. Далее, после всех манипуляций с бизнес логикой, данные нужно выдать наружу. И для этого у нас есть класс StudentDto. Разумеется, нам нужно передать данные из класса StudentModel в StudentDto. Писать руками каждый раз методы, которые будут переносить, трудоемко. Плюс это лишний код в кодовой базе, который нужно поддерживать. Можно допустить ошибку. А Mapstruct такие методы генерирует на этапе компиляции и хранит в generated-sources.

## Как?

При помощи аннотаций. Нам необходимо просто создать аннотацию, в которой будет главная аннотация Mapper, которая скажет библиотеке, что методы в этом интерфейсе можно использовать для перевода из одних объектов в другие. Как я сказал раньше про студентов, в нашем случае это будет интерфейс StudentMapper, в котором будут несколько методов по перегонке данных из одного слоя в другой:
# Example
```xml
<properties>
   <org.mapstruct.version>1.4.2.Final</org.mapstruct.version>
   <lombok-mapstruct-binding.version>0.2.0</lombok-mapstruct-binding.version>
</properties>


<dependency>
   <groupId>org.projectlombok</groupId>
   <artifactId>lombok-mapstruct-binding</artifactId>
   <version>${lombok-mapstruct-binding.version}</version>
</dependency>
<dependency>
   <groupId>org.mapstruct</groupId>
   <artifactId>mapstruct</artifactId>
   <version>1.4.2.Final</version>
</dependency>


<plugin>
   <groupId>org.apache.maven.plugins</groupId>
   <artifactId>maven-compiler-plugin</artifactId>
   <version>3.5.1</version>
   <configuration>
       <source>1.8</source>
       <target>1.8</target>
       <annotationProcessorPaths>
           <path>
               <groupId>org.mapstruct</groupId>
               <artifactId>mapstruct-processor</artifactId>
               <version>${org.mapstruct.version}</version>
           </path>
           <path>
               <groupId>org.projectlombok</groupId>
               <artifactId>lombok</artifactId>
               <version>${lombok.version}</version>
           </path>
           <path>
               <groupId>org.projectlombok</groupId>
               <artifactId>lombok-mapstruct-binding</artifactId>
               <version>${lombok-mapstruct-binding.version}</version>
           </path>
       </annotationProcessorPaths>
   </configuration>
</plugin>

```
```java
@Mapper(componentModel = "spring")  
@Component  
public interface CarMapper {  
    CarDto toCarDto(Car car);  
  
    Car toCar(CarDto CarDto);  
}
```