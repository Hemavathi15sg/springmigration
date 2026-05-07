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

> **Critical:** The Quarkus BOM and Maven plugin use groupId `io.quarkus.platform`. Individual extension dependencies use groupId `io.quarkus` with **no version** (managed by the BOM). Mixing these groupIds causes resolution failures.

Replace the Spring Boot `<parent>` block with a BOM import. Use this **exact XML** inside `<dependencyManagement>`:

```xml
<dependencyManagement>
  <dependencies>
    <dependency>
      <groupId>io.quarkus.platform</groupId>
      <artifactId>quarkus-bom</artifactId>
      <version>3.9.5</version>
      <type>pom</type>
      <scope>import</scope>
    </dependency>
  </dependencies>
</dependencyManagement>
```

Replace `spring-boot-maven-plugin` with this **exact plugin XML** inside `<build><plugins>`:

```xml
<plugin>
  <groupId>io.quarkus.platform</groupId>
  <artifactId>quarkus-maven-plugin</artifactId>
  <version>3.9.5</version>
  <extensions>true</extensions>
  <executions>
    <execution>
      <goals>
        <goal>build</goal>
        <goal>generate-code</goal>
        <goal>generate-code-tests</goal>
      </goals>
    </execution>
  </executions>
</plugin>
```

Also add the maven-compiler-plugin configured for Java 17:

```xml
<plugin>
  <groupId>org.apache.maven.plugins</groupId>
  <artifactId>maven-compiler-plugin</artifactId>
  <version>3.13.0</version>
  <configuration>
    <source>17</source>
    <target>17</target>
  </configuration>
</plugin>
```

Replace Spring Boot extension dependencies with Quarkus extensions (groupId `io.quarkus`, no version tag):

| Remove | Add (groupId: `io.quarkus`, no `<version>`) |
|--------|---------------------------------------------|
| `spring-boot-starter-parent` (parent block) | BOM import above |
| `spring-boot-starter-web` | `quarkus-resteasy-reactive-jackson` |
| `spring-boot-starter-data-jpa` | `quarkus-hibernate-orm-panache` |
| `h2` (runtime scope) | `quarkus-jdbc-h2` |
| `springfox-swagger2`, `springfox-swagger-ui` | `quarkus-smallrye-openapi` |
| `spring-boot-starter-test`, `rest-assured` | Remove — not needed for this migration |
| _(not present in Spring)_ | `quarkus-arc` (CDI runtime — always required) |

Replace `<java.version>` property with:
```xml
<maven.compiler.source>17</maven.compiler.source>
<maven.compiler.target>17</maven.compiler.target>
```

## Rule 3 — Configuration Migration

In `application.properties`, replace Spring keys with Quarkus equivalents and remove all `spring.*` and `server.*` keys:

| Spring Boot key | Quarkus key |
|----------------|------------|
| `spring.datasource.url` | `quarkus.datasource.jdbc.url` |
| `spring.datasource.driver-class-name` | `quarkus.datasource.jdbc.driver` |
| `spring.datasource.username` | `quarkus.datasource.username` |
| `spring.datasource.password` | `quarkus.datasource.password` |
| `spring.jpa.hibernate.ddl-auto=create` | `quarkus.hibernate-orm.database.generation=drop-and-create` |
| `spring.jpa.show-sql=true` | `quarkus.hibernate-orm.log.sql=true` |
| `server.port=8888` | `quarkus.http.port=8080` |
| _(add — not in Spring)_ | `quarkus.datasource.db-kind=h2` |

Use H2 in-memory JDBC URL: `jdbc:h2:mem:testdb;DB_CLOSE_DELAY=-1;DB_CLOSE_ON_EXIT=FALSE`

## Rule 4 — Repository Pattern

Replace the Spring Data `JpaRepository` interface with a Quarkus Panache repository class:

```java
// Before (Spring Boot)
public interface ProductRepository extends JpaRepository<Product, Long> {}

// After (Quarkus)
@ApplicationScoped
public class ProductRepository implements PanacheRepository<Product> {}
```

Import: `io.quarkus.hibernate.orm.panache.PanacheRepository` and `jakarta.enterprise.context.ApplicationScoped`.

`PanacheRepository` already provides `findAll()`, `findById()`, `persist()`, `deleteById()`, and `count()`. Do not re-declare these methods.

## Rule 5 — API Documentation

Delete `SwaggerConfig.java` and `CrudApplication.java` entirely:
- `SwaggerConfig.java` — the `quarkus-smallrye-openapi` extension auto-configures Swagger UI at `/q/swagger-ui` and OpenAPI schema at `/q/openapi`. No Java config class is needed.
- `CrudApplication.java` — `@SpringBootApplication` and `main()` are not needed in Quarkus. Quarkus has its own build-time bootstrap.