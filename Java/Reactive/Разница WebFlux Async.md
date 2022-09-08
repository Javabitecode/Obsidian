1. https://www.baeldung.com/spring-mvc-async-vs-webflux

Spring Async supports Servlet 3.0 specifications, but Spring WebFlux supports Servlet 3.1+. It brings a number of differences:

-   Spring Async I/O model during its communication with the client is blocking. It may cause a performance problem with slow clients. On the other hand, Spring WebFlux provides a non-blocking I/O model.
-   Reading the request body or request parts is blocking in Spring Async, whiles it is non-blocking in Spring WebFlux.
-   In Spring Async, _Filter_s and _Servlet_s are working synchronously, but Spring WebFlux supports full asynchronous communication.
-   Spring WebFlux is compatible with wider ranges of Web/Application servers than Spring Async, like Netty, and Undertow.

Moreover, Spring WebFlux supports reactive backpressure, so we have more control over how we should react to fast producers than both Spring MVC Async and Spring MVC.

Spring Flux also has a tangible shift towards functional coding style and declarative API decomposition thanks to Reactor API behind it.

Do all of these items lead us to use Spring WebFlux? Well, **Spring Async or even Spring MVC might be the right answer to a lot of projects out there, depending on the desired load scalability or availability of the system**.

Regarding scalability, Spring Async gives us better results than synchronous Spring MVC implementation. Spring WebFlux, because of its reactive nature, provides us elasticity and higher availability.