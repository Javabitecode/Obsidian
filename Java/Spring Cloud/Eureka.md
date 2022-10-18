https://betacode.net/11733/understanding-spring-cloud-eureka-server-with-example

![[Pasted image 20221010094849.png]]

Щаг 1:

-   Создать **Service Registration** (Регистрация услуги) используя **Spring Cloud Eureka Server**.
-   Запустить данное приложение напрямую на **Eclipse** и просмотреть **Eureka Monitor** (Eureka монитор).

Шаг 2:

-   Создать реплики (replica) для приложения, чтобы развернуть на разных серверах, каждая реплика (replica) будет работать на отдельном доменном имени. Здесь мы предполагаем, что система имеет много пользователей поэтому нужно чтобы много реплик работали на разных серверах для уменьшения нагрузки (Reduce overload).
-   Объяснение механизма статуса деления между **Eureka Server**.

![[Pasted image 20221010094959.png]]

## 3- @EnableEurekaServer 
Создать сервис с этой аннотацией в @SpringBootAplication

Шаг 4:

``` 
--- # This default profile is used when running a single instance completely standalone: 
spring: 
	profiles: default 
server: 
	port: 9000 
eureka: 
	instance: hostname: my-eureka-server.com client: registerWithEureka: false fetchRegistry: false serviceUrl: defaultZone: http://${eureka.instance.hostname}:${server.port}/eureka/ # united-states, france, and vietnam illustrate running 3 intercommunicating instances. # This example has them running side-by-side on localhost # -- which is unrealistic in production # -- but does illustrate how multiple instances collaborate. # # Run by opening 3 separate command prompts: # java -jar -Dspring.profiles.active=united-states SpringCloudServiceRegistrationEurekaServer.jar # java -jar -Dspring.profiles.active=france SpringCloudServiceRegistrationEurekaServer.jar # java -jar -Dspring.profiles.active=vietnam SpringCloudServiceRegistrationEurekaServer.jar --- spring: profiles: united-states application: name: eureka-server-clustered # ==> This is Service-Id server: port: 9001 eureka: instance: hostname: my-eureka-server-us.com client: registerWithEureka: true fetchRegistry: true serviceUrl: defaultZone: http://my-eureka-server-fr.com:9002/eureka/,http://my-eureka-server-vn.com:9003/eureka/ --- spring: profiles: france application: name: eureka-server-clustered # ==> This is Service-Id server: port: 9002 eureka: instance: hostname: my-eureka-server-fr.com client: registerWithEureka: true fetchRegistry: true serviceUrl: defaultZone: http://my-eureka-server-us.com:9001/eureka/,http://my-eureka-server-vn.com:9003/eureka/ --- spring: profiles: vietnam application: name: eureka-server-clustered # ==> This is Service-Id server: port: 9003 eureka: instance: hostname: my-eureka-server-vn.com client: registerWithEureka: true fetchRegistry: true serviceUrl: defaultZone: http://my-eureka-server-us.com:9001/eureka/,http://my-eureka-server-fr.com:9002/eureka/
```

**Eureka Monitor** (Eureka Монитор) позволяет вам видеть список услууг (приложений) работающих и зарегистрированных с данным **Eureka Server**, одновременно позволяет вам видеть реплики (replica) данного приложения, которые работают на распределенной системе. Вы можете войти в **Eureka Monitor** по **URL** ниже,

-   [http://localhost:9000](http://localhost:9000/)

Шаг 5:
После успешного выполнения **"Maven Install"**, вы имеете файл **jar** в папке **target** у проекта. 
Копировать только что созданный файл **jar** в определенную папку, одновременно создать 3 файла **BAT**:

-   **eureka-server-us.bat**
-   **eureka-server-fr.bat**
-   **eureka-server-vn.bat**

![[Pasted image 20221010095436.png]]

my-eureka-server-us.bat

```bash

java -jar -Dspring.profiles.active=united-states SpringCloudServiceRegistrationEurekaServer-0.0.1-SNAPSHOT.jar
```

Получится вот так:
![[Pasted image 20221010095523.png]]