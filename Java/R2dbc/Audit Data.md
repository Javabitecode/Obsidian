1. https://medium.com/swlh/data-auditing-with-spring-data-r2dbc-5d428fc94688
2. https://github.com/hantsy/spring-r2dbc-sample/blob/master/docs/auditing.md

# Example

Таблицы прописать в flyway или т.п.

```java
@Configuration  
@EnableR2dbcAuditing  
class DatabaseConfig{  

@Bean  
ReactiveAuditorAware<String> auditorAware() {  
	return () -> ReactiveSecurityContextHolder.getContext()  
	.map(SecurityContext::getAuthentication)  
	.filter(Authentication::isAuthenticated)  
	.map(Authentication::getPrincipal)  
	.map(User.class::cast)  
	.map(User::getUsername);  
	}
}
```

```java
@Data  
@ToString  
@Builder  
@NoArgsConstructor  
@AllArgsConstructor  
@Table(value = "posts")  
class Post {   
	@Id  
	@Column("id")  
    private UUID id;   
    
    @Column("title")  
    private String title; 
    
    @Column("content")  
    private String content; 
    
    @Column("created_at")  
    @CreatedDate  
    private LocalDateTime createdAt; 
    @Column("created_by")  
    @CreatedBy  
    private String createdBy;
    @Column("updated_at")  
    @LastModifiedDate  
    private LocalDateTime updatedAt;
    @Column("updated_by")  
    @LastModifiedBy  
    private String updatedBy;
    @Column("version")  
    
    @Version  
    private Long version; можно не ставить
    }
```
