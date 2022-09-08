1. https://developers.google.com/protocol-buffers/docs/overview
2. Language Guide (proto3) https://developers.google.com/protocol-buffers/docs/proto3
3. https://tproger.ru/articles/grpc-integration-experience/
4. Example https://www.vinsguru.com/grpc-spring-boot-integration/ 
Protocol buffers provide a language-neutral, platform-neutral, extensible mechanism for serializing structured data in a forward-compatible and backward-compatible way. It’s like JSON, except it's smaller and faster, and it generates native language bindings.

Protocol buffers are a combination of the definition language (created in `.proto` files), the code that the proto compiler generates to interface with data, language-specific runtime libraries, and the serialization format for data that is written to a file (or sent across a network connection).
### Example
```proto
message Person {  
optional string name = 1;  
optional int32 id = 2;  
optional string email = 3;
}
```
После компиляции фалйа .proto, генерируеться класс Person и сопутсвующие методы.
```java
Person john = Person.newBuilder()    
	.setId(1234)    
	.setName("John Doe")    
	.setEmail("jdoe@example.com")    
	.build();
output = new FileOutputStream(args[0]);
john.writeTo(output);

```
