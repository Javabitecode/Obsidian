# Example

```sql
@Query("SELECT * FROM people p WHERE (:firstName is null or LOWER (p.first_name) LIKE LOWER(CONCAT('%',:firstName,'%')))" +

            " AND (:lastName is null or LOWER(p.last_name) LIKE LOWER(CONCAT('%',:lastName,'%')))" +

            " AND (:gender is null or LOWER(p.gender) LIKE LOWER(CONCAT('%',:gender,'%')))")

    Flux<Person> findByFirstNameOrLastNameOrGender(

            @Param("firstName") String firstName,

            @Param("lastName") String lastName,

            @Param("gender") String gender);

}
```
