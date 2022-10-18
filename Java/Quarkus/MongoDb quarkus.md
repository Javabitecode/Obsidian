1. https://quarkus.pro/guides/mongodb-panache
2. https://quarkus.pro/guides/mongodb-panache#custom-ids

```java
@MongoEntity(collection="ThePerson")
public class Person  {
    public ObjectId id; // used by MongoDB for the _id field
    public String name;
    public LocalDate birth;
    public Status status;
}
```

```java
@ApplicationScoped
public class PersonRepository implements PanacheMongoRepository<Person> {

   // put your custom logic here as instance methods

   public Person findByName(String name){
       return find("name", name).firstResult();
   }

   public List<Person> findAlive(){
       return list("status", Status.Alive);
   }

   public void deleteLoics(){
       delete("name", "Loïc");
  }
}
```

```java
@Inject
PersonRepository personRepository;

@GET
public long count(){
    return personRepository.count();
}
```

```java
// creating a person
Person person = new Person();
person.name = "Loïc";
person.birth = LocalDate.of(1910, Month.FEBRUARY, 1);
person.status = Status.Alive;

// persist it
personRepository.persist(person);

person.status = Status.Dead;

// Your must call update() in order to send your entity modifications to MongoDB
personRepository.update(person);

// delete it
personRepository.delete(person);

// getting a list of all Person entities
List<Person> allPersons = personRepository.listAll();

// finding a specific person by ID
person = personRepository.findById(personId);

// finding a specific person by ID via an Optional
Optional<Person> optional = personRepository.findByIdOptional(personId);
person = optional.orElseThrow(() -> new NotFoundException());

// finding all living persons
List<Person> livingPersons = personRepository.list("status", Status.Alive);

// counting all persons
long countAll = personRepository.count();

// counting all living persons
long countAlive = personRepository.count("status", Status.Alive);

// delete all living persons
personRepository.delete("status", Status.Alive);

// delete all persons
personRepository.deleteAll();
```