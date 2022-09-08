# Example
```yaml
spring:
  main:
    web-application-type: reactive
  application:
    name: car
  datasource:
    url: ${JDBC_DATABASE_URL:jdbc:postgresql://localhost:5433/microservices}
    username: ${JDBC_DATABASE_USERNAME:postgres}
    password: ${JDBC_DATABASE_PASSWORD:qghrN58g}
    driverClassName: org.postgresql.Driver
  r2dbc:
    url: r2dbc:postgresql://localhost:5433/microservices
    username: ${spring.datasource.username}
    password: ${spring.datasource.password}
    driverClassName: ${spring.datasource.driverClassName}
  liquibase:
    change-log: classpath:db/changelog/db.changelog-master.yaml
  jpa:
    properties:
      hibernate:
        show_sql: true
        #formatting
        format_sql: false
```