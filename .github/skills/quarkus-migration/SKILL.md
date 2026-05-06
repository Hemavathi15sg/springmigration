---
name: quarkus-migration
description: Governing rules for migrating a Spring Boot 2.x application to Quarkus 3.x. Use this skill when asked to migrate, convert, or update any Spring Boot file to Quarkus.
---

You are performing a Spring Boot 2.x to Quarkus 3.9.5 migration. Apply all five rules below to every file you touch.

## Rule 1 — Annotation Mapping

Replace Spring MVC and Spring Data annotations with their Jakarta REST and CDI equivalents:

| Spring Boot | Quarkus (Jakarta RS / CDI) |
|------------|---------------------------|
| `@RestController` | `@Path("/route")` + `@ApplicationScoped` |
| `@RequestMapping("/api")` | `@Path("/api")` on the class |
| `@GetMapping("/path")` | `@GET` + `@Path("/path")` + `@Produces(MediaType.APPLICATION_JSON)` |
| `@PostMapping("/path")` | `@POST` + `@Path("/path")` + `@Consumes(MediaType.APPLICATION_JSON)` + `@Produces(MediaType.APPLICATION_JSON)` |
| `@PutMapping("/path/{id}")` | `@PUT` + `@Path("/path/{id}")` |
| `@DeleteMapping("/path/{id}")` | `@DELETE` + `@Path("/path/{id}")` |
| `@PathVariable Long id` | `@PathParam("id") Long id` |
| `@RequestBody` | Remove annotation — JAX-RS reads the body parameter automatically |
| `@Autowired` | `@Inject` (jakarta.inject.Inject) |
| `ResponseEntity` return type | `Response` (jakarta.ws.rs.core.Response) |

## Rule 2 — Dependency Replacement

In `pom.xml`, replace Spring Boot dependencies with Quarkus extensions (groupId: `io.quarkus`):

| Remove | Add |
|--------|-----|
| `spring-boot-starter-parent` (parent) | `quarkus-bom` in `<dependencyManagement>` |
| `spring-boot-starter-web` | `quarkus-resteasy-reactive-jackson` |
| `spring-boot-starter-data-jpa` | `quarkus-hibernate-orm-panache` |
| `h2` (runtime) | `quarkus-jdbc-h2` |
| `springfox-swagger2`, `springfox-swagger-ui` | `quarkus-smallrye-openapi` |
| `spring-boot-maven-plugin` | `quarkus-maven-plugin` |

Always use Quarkus version `3.9.5`. Set Java source/target to `17`.

## Rule 3 — Configuration Migration

In `application.properties`, replace Spring keys with Quarkus equivalents:

| Spring Boot key | Quarkus key |
|----------------|------------|
| `spring.datasource.url` | `quarkus.datasource.jdbc.url` |
| `spring.datasource.driver-class-name` | `quarkus.datasource.jdbc.driver` |
| `spring.datasource.username` | `quarkus.datasource.username` |
| `spring.datasource.password` | `quarkus.datasource.password` |
| `spring.jpa.hibernate.ddl-auto=create` | `quarkus.hibernate-orm.database.generation=drop-and-create` |
| `spring.jpa.show-sql=true` | `quarkus.hibernate-orm.log.sql=true` |
| `server.port=8888` | `quarkus.http.port=8080` |
| _(not present in Spring)_ | `quarkus.datasource.db-kind=h2` |

Remove all remaining `spring.*` and `server.*` keys.

## Rule 4 — Repository Pattern

Replace the Spring Data `JpaRepository` interface with a Quarkus Panache repository class:

```java
// Before (Spring)
public interface ProductRepository extends JpaRepository<Product, Long> {}

// After (Quarkus)
@ApplicationScoped
public class ProductRepository implements PanacheRepository<Product> {}
```

`PanacheRepository` already provides `findAll()`, `findById()`, `persist()`, `deleteById()`, and `count()`. Do not re-declare these methods.

## Rule 5 — API Documentation

Delete `SwaggerConfig.java` entirely. The `quarkus-smallrye-openapi` extension provides:
- OpenAPI schema at `/q/openapi`
- Swagger UI at `/q/swagger-ui`

No Java configuration class is needed.
