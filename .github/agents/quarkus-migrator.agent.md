---
name: Quarkus Migrator
description: Migrates a Spring Boot 2.x CRUD application to Quarkus 3.x end-to-end. Reads the migration task list and plan, applies every change in phase order, verifies the build compiles, and reports the final status.
tools: [read, edit, create, delete, run]
---

You are a senior Java engineer executing a full Spring Boot to Quarkus 3.9.5 migration. Follow these instructions exactly.

## Step 1 — Read the Migration Inputs

Before changing any file, read these three files in full:
1. `workshop/migration-tasks.md` — the ordered task list with acceptance criteria
2. `workshop/migration-plan.md` (if it exists) — the phased implementation plan
3. `.github/skills/quarkus-migration/SKILL.md` — the five governing migration rules

If `workshop/migration-tasks.md` does not exist, read `workshop/exercise-3.md` to understand the expected task structure, then read all source files and produce the task list yourself before proceeding.

## Step 2 — Execute Phase 1: Build and Configuration

### 2a. Migrate pom.xml

Apply these changes to `pom.xml`:

1. Remove the entire `<parent>` block.
2. Add this exact `<dependencyManagement>` section (groupId is `io.quarkus.platform`):

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

3. Replace `<java.version>` with:
```xml
<maven.compiler.source>17</maven.compiler.source>
<maven.compiler.target>17</maven.compiler.target>
```

4. Remove all `spring-boot-starter-*` dependencies and `springfox-*` and `rest-assured` dependencies.

5. Add these extension dependencies (groupId `io.quarkus`, no `<version>` tag — managed by BOM):
```xml
<dependency>
  <groupId>io.quarkus</groupId>
  <artifactId>quarkus-resteasy-reactive-jackson</artifactId>
</dependency>
<dependency>
  <groupId>io.quarkus</groupId>
  <artifactId>quarkus-hibernate-orm-panache</artifactId>
</dependency>
<dependency>
  <groupId>io.quarkus</groupId>
  <artifactId>quarkus-jdbc-h2</artifactId>
</dependency>
<dependency>
  <groupId>io.quarkus</groupId>
  <artifactId>quarkus-smallrye-openapi</artifactId>
</dependency>
<dependency>
  <groupId>io.quarkus</groupId>
  <artifactId>quarkus-arc</artifactId>
</dependency>
```

6. Replace `spring-boot-maven-plugin` with this exact plugin block (groupId is `io.quarkus.platform`):
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

7. Also add or update the maven-compiler-plugin:
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

8. Keep `<groupId>`, `<artifactId>`, `<version>`, `<packaging>`, `<name>`, and `<description>` unchanged.

### 2b. Migrate application.properties

Replace `src/main/resources/application.properties` contents with:

```properties
quarkus.datasource.db-kind=h2
quarkus.datasource.jdbc.url=jdbc:h2:mem:testdb;DB_CLOSE_DELAY=-1;DB_CLOSE_ON_EXIT=FALSE
quarkus.datasource.jdbc.driver=org.h2.Driver
quarkus.datasource.username=sa
quarkus.datasource.password=

quarkus.hibernate-orm.database.generation=drop-and-create
quarkus.hibernate-orm.log.sql=true

quarkus.http.port=8080
```

### 2c. Verify Phase 1

Run: `mvn dependency:resolve -q`

If this fails with resolution errors, re-read pom.xml and fix the groupId or version. Do not proceed to Phase 2 until dependency resolution succeeds.

## Step 3 — Execute Phase 2: Model

Migrate `src/main/java/com/englishcentral/crud/model/Product.java`:
- Replace all `javax.persistence.*` imports with `jakarta.persistence.*`
- Replace `javax.persistence.Column` → `jakarta.persistence.Column`, etc.
- `org.hibernate.annotations.CreationTimestamp` and `org.hibernate.annotations.UpdateTimestamp` are compatible with Quarkus — keep them unchanged
- Keep all fields, getters, and setters exactly as they are
- Do NOT add `extends PanacheEntity` or any Panache inheritance

## Step 4 — Execute Phase 3: Repository

Migrate `src/main/java/com/englishcentral/crud/repository/ProductRepository.java`:
- Change from an `interface` that `extends JpaRepository<Product, Long>` to a `class` that `implements PanacheRepository<Product>`
- Add `@ApplicationScoped` annotation (`jakarta.enterprise.context.ApplicationScoped`)
- Import `io.quarkus.hibernate.orm.panache.PanacheRepository`
- Remove all Spring Data imports (`org.springframework.data.*`, `org.springframework.stereotype.*`)
- Do NOT add any method bodies — `PanacheRepository` provides all CRUD methods

## Step 5 — Execute Phase 4: Controller

Migrate `src/main/java/com/englishcentral/crud/controller/MainController.java`:
- Remove ALL `org.springframework.*` imports
- Add `@Path("/api")` and `@ApplicationScoped` on the class
- Replace `@Autowired` with `@Inject` (`jakarta.inject.Inject`)
- For each endpoint method, apply this mapping:

  **GET /products:**
  ```java
  @GET
  @Path("/products")
  @Produces(MediaType.APPLICATION_JSON)
  public List<Product> getProducts() { ... }
  ```

  **GET /products/{id}:**
  ```java
  @GET
  @Path("/products/{id}")
  @Produces(MediaType.APPLICATION_JSON)
  public Product getProduct(@PathParam("id") Long id) { ... }
  ```

  **POST /products:**
  ```java
  @POST
  @Path("/products")
  @Consumes(MediaType.APPLICATION_JSON)
  @Produces(MediaType.APPLICATION_JSON)
  public Response createProduct(ProductRequest request) {
      // ... build product, call productRepository.persist(product)
      URI location = UriBuilder.fromResource(MainController.class)
              .path(String.valueOf(result.getId()))
              .build();
      return Response.created(location).entity(new ApiResponse(true, "Product was successfully saved.")).build();
  }
  ```

  **PUT /products/{id}:**
  ```java
  @PUT
  @Path("/products/{id}")
  @Consumes(MediaType.APPLICATION_JSON)
  @Produces(MediaType.APPLICATION_JSON)
  public Response updateProduct(ProductRequest request, @PathParam("id") Long id) { ... }
  ```

  **DELETE /products/{id}:**
  ```java
  @DELETE
  @Path("/products/{id}")
  @Produces(MediaType.APPLICATION_JSON)
  public Response deleteProduct(@PathParam("id") Long id) { ... }
  ```

- For `findById()` calls: Panache returns `Optional<Product>` — the existing `product.isPresent()` check works unchanged
- Replace all `ResponseEntity` returns with `Response` (`jakarta.ws.rs.core.Response`)
- Replace `ResponseEntity.ok(...)` with `Response.ok(...).build()`
- Replace `ResponseEntity.created(uri).body(...)` with `Response.created(uri).entity(...).build()`
- Replace `ResponseEntity.notFound().build()` with `Response.status(Response.Status.NOT_FOUND).build()`
- Replace `ServletUriComponentsBuilder` with `UriBuilder` from `jakarta.ws.rs.core.UriBuilder`

## Step 6 — Execute Phase 5: Cleanup

### 6a. Migrate AppException.java

Migrate `src/main/java/com/englishcentral/crud/exception/AppException.java`:
- Remove all `org.springframework.*` imports
- Change `extends RuntimeException` to `extends WebApplicationException`
- Import `jakarta.ws.rs.WebApplicationException`, `jakarta.ws.rs.core.Response`, `jakarta.ws.rs.core.MediaType`
- Constructor body:
  ```java
  public AppException(String message) {
      super(Response.status(Response.Status.NOT_FOUND)
              .entity(message)
              .type(MediaType.TEXT_PLAIN)
              .build());
  }
  ```

### 6b. Delete unnecessary files

Delete these files — they have no equivalent in Quarkus:
- `src/main/java/com/englishcentral/crud/config/SwaggerConfig.java`
- `src/main/java/com/englishcentral/crud/CrudApplication.java`

## Step 7 — Verify Full Build

Run: `mvn compile -q`

If compilation fails:
1. Read the error output carefully
2. Fix the specific import or annotation issue identified
3. Re-run `mvn compile -q`
4. Repeat until compilation succeeds

Do not report success until `mvn compile -q` exits with code 0.

## Step 8 — Report

Once the build succeeds, report:
- A summary of every file changed (one line per file: what changed)
- Confirmation that `mvn compile -q` passed
- Instructions for the participant to run `mvn quarkus:dev` and verify Swagger UI at `http://localhost:8080/q/swagger-ui`