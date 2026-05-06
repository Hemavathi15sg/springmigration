# Exercise 5 — Migrate the Application Code

> **Goal:** Layer by layer, use GitHub Copilot Chat to migrate every Java source file from Spring Boot to Quarkus 3.x — model, repository, controller, and cleanup — ending with all five CRUD endpoints working and visible in the Quarkus Swagger UI.

> **Time:** ~12 minutes &nbsp;|&nbsp; **Prerequisite:** `pom.xml` and `application.properties` migrated ([Exercise 4](exercise-4.md)) &nbsp;|&nbsp; **Track:** Required — this is the final exercise

---

## Context

With the build and config in place (Exercise 4), all that stands between you and a running Quarkus API are the Java source files. You'll migrate them in the order defined in your migration plan: model → repository → controller → cleanup. Each layer is one Copilot Chat prompt; each step ends with the project in a compilable state.

> **The `quarkus-migration` skill is active for every prompt in this exercise.** You do not need to attach any file — Copilot auto-invokes the skill and applies annotation mapping, Panache pattern, and all other rules automatically.

**Migration order and rationale:**

| Step | File | Why This Order |
|------|------|---------------|
| 1 | `Product.java` | Model has no Spring dependencies itself — just a `javax` → `jakarta` package rename. Everything else depends on it. |
| 2 | `ProductRepository.java` | Depends on `Product`. Once migrated, the controller can be migrated without import errors. |
| 3 | `MainController.java` | Depends on both model and repository. This is the largest change. |
| 4 | `AppException.java` | Minor change: `RuntimeException` → `WebApplicationException` |
| 5 | `SwaggerConfig.java` + `CrudApplication.java` | These files are deleted entirely — Quarkus replaces both with zero-config equivalents |

---

## Step 1 — Migrate `Product.java` (Model Layer)

1. Open `src/main/java/com/englishcentral/crud/model/Product.java` in the VS Code editor
2. Open **Copilot Chat** in agent mode and send:

```
Migrate Product.java to Quarkus 3.x.

Apply these changes:
1. Replace all javax.persistence.* imports with jakarta.persistence.*
2. Replace org.hibernate.annotations.CreationTimestamp with jakarta.persistence.PrePersist lifecycle logic
   OR keep org.hibernate.annotations.CreationTimestamp — it is still compatible with Quarkus Hibernate
3. Keep all field definitions, getters, and setters exactly as they are
4. Do not add Panache inheritance — Product remains a plain @Entity JPA class

Show the complete updated Product.java.
```

3. Review the diff — only `javax` → `jakarta` in the import statements should change.
4. **Accept** the changes.
5. Run `mvn compile -q` — it should now compile `Product.java` without errors (other files will still fail).

---

## Step 2 — Migrate `ProductRepository.java` (Repository Layer)

1. Open `src/main/java/com/englishcentral/crud/repository/ProductRepository.java` in the editor
2. In Copilot Chat (same session), send:

```
Migrate ProductRepository.java to Quarkus 3.x.

Apply these changes:
1. Remove the interface declaration that extends JpaRepository<Product, Long>
2. Create a class (not an interface) named ProductRepository that implements
   PanacheRepository<Product> from io.quarkus.hibernate.orm.panache
3. Annotate the class with @ApplicationScoped (from jakarta.enterprise.context)
4. Remove all Spring Data import statements

Note: PanacheRepository already provides findAll(), findById(), persist(), deleteById()
and other standard CRUD methods — do not redeclare them.

Show the complete updated ProductRepository.java.
```

3. Review: the file should be a class annotated `@ApplicationScoped` implementing `PanacheRepository<Product>` with no method bodies needed.
4. **Accept** the changes.
5. Run `mvn compile -q` — `Product.java` and `ProductRepository.java` should now compile without errors.

---

## Step 3 — Migrate `MainController.java` (Controller Layer)

This is the largest change. Take a moment to read through the generated diff before accepting.

1. Open `src/main/java/com/englishcentral/crud/controller/MainController.java` in the editor
2. In Copilot Chat (same session), send:

```
Migrate MainController.java to Quarkus 3.x.

Apply these changes:
1. Remove all org.springframework.* imports
2. Replace @RestController with @Path("/api") + @ApplicationScoped
   (import jakarta.ws.rs.Path and jakarta.enterprise.context.ApplicationScoped)
3. Replace @RequestMapping("/api") — already handled by @Path on the class
4. Replace @Autowired ProductRepository with @Inject ProductRepository
   (import jakarta.inject.Inject)
5. Replace @GetMapping("/products") with @GET @Path("/products") @Produces(MediaType.APPLICATION_JSON)
6. Replace @GetMapping("/products/{id}") with @GET @Path("/products/{id}") @Produces(MediaType.APPLICATION_JSON)
   Replace @PathVariable Long id with @PathParam("id") Long id
7. Replace @PostMapping("/products") with @POST @Path("/products") @Consumes(MediaType.APPLICATION_JSON) @Produces(MediaType.APPLICATION_JSON)
   Replace @RequestBody ProductRequest request with ProductRequest request (JAX-RS reads the body automatically)
   Replace ResponseEntity.created(location).body(...) with Response.created(location).entity(...).build()
   (import jakarta.ws.rs.core.Response and jakarta.ws.rs.core.UriBuilder for location)
8. Replace @PutMapping("/products/{id}") with @PUT @Path("/products/{id}") @Consumes(MediaType.APPLICATION_JSON) @Produces(MediaType.APPLICATION_JSON)
   Replace ResponseEntity return type with Response
9. Replace @DeleteMapping("/products/{id}") with @DELETE @Path("/products/{id}") @Produces(MediaType.APPLICATION_JSON)
   Replace ResponseEntity return type with Response
10. Replace org.springframework.web.servlet.support.ServletUriComponentsBuilder with UriBuilder (jakarta.ws.rs.core.UriBuilder) for building the Location URI

Keep all business logic (repository calls, Optional handling, AppException throws) identical.

Show the complete updated MainController.java.
```

3. Review the diff carefully — every Spring annotation should be gone, replaced by a Jakarta RS equivalent.
4. **Accept** the changes.
5. Run `mvn compile -q` — the three core files should now compile. Only `SwaggerConfig.java`, `AppException.java`, and `CrudApplication.java` may still have Spring imports.

---

## Step 4 — Migrate `AppException.java`

1. Open `src/main/java/com/englishcentral/crud/exception/AppException.java` in the editor
2. In Copilot Chat, send:

```
Migrate AppException.java to Quarkus 3.x.

Apply these changes:
1. Remove all org.springframework.* imports
2. Change the class to extend WebApplicationException (jakarta.ws.rs.WebApplicationException)
   instead of RuntimeException
3. The constructor should accept a String message and pass it to:
   super(Response.status(Response.Status.NOT_FOUND).entity(message).type(MediaType.TEXT_PLAIN).build())
   (import jakarta.ws.rs.core.Response and jakarta.ws.rs.core.MediaType)
4. Keep the class in the same package

Show the complete updated AppException.java.
```

3. **Accept** the changes.

---

## Step 5 — Delete `SwaggerConfig.java` and `CrudApplication.java`

These files serve no purpose in Quarkus and must be removed.

1. In Copilot Chat, send:

```
Delete these two files from the project — they are not needed in Quarkus:

1. src/main/java/com/englishcentral/crud/config/SwaggerConfig.java
   Reason: The quarkus-smallrye-openapi extension automatically generates OpenAPI/Swagger UI
   with zero configuration. Manual Springfox bean configuration is incompatible with Quarkus.

2. src/main/java/com/englishcentral/crud/CrudApplication.java
   Reason: @SpringBootApplication and the main() method are a Spring Boot entry point.
   Quarkus has its own build-time entry point — there is no equivalent class needed.

Delete both files.
```

2. **Accept** both deletions.

---

## Step 6 — Start Quarkus and Verify All Endpoints

Start the Quarkus development server:

```shell
mvn quarkus:dev
```

The server should start cleanly. Open the Swagger UI:

[http://localhost:8080/q/swagger-ui](http://localhost:8080/q/swagger-ui)

You should see five endpoints under `/api/products`. Verify each one:

| Endpoint | Action to Verify |
|----------|-----------------|
| `POST /api/products` | Create a product: `{"name":"Laptop","description":"High-end laptop","price":1299.99}` — expect `201 Created` with a `Location` header |
| `GET /api/products` | List all products — expect a JSON array containing the product just created |
| `GET /api/products/{id}` | Fetch the created product by its ID — expect `200 OK` with the product JSON |
| `PUT /api/products/{id}` | Update the product name to `"Gaming Laptop"` — expect `200 OK` |
| `DELETE /api/products/{id}` | Delete the product — expect `200 OK` or `204 No Content` |

Also check the OpenAPI schema at [http://localhost:8080/q/openapi](http://localhost:8080/q/openapi) — it should describe all five endpoints automatically.

---

## Final Validation Checklist

| Check | Status |
|-------|--------|
| `Product.java` uses only `jakarta.persistence.*` imports | ☐ |
| `ProductRepository.java` is a class implementing `PanacheRepository<Product>` | ☐ |
| `MainController.java` has no `org.springframework.*` imports | ☐ |
| `MainController.java` is annotated `@Path("/api")` and `@ApplicationScoped` | ☐ |
| `AppException.java` extends `WebApplicationException` | ☐ |
| `SwaggerConfig.java` has been deleted | ☐ |
| `CrudApplication.java` has been deleted | ☐ |
| `mvn quarkus:dev` starts without compilation or runtime errors | ☐ |
| Swagger UI visible at `http://localhost:8080/q/swagger-ui` | ☐ |
| All five CRUD endpoints respond correctly | ☐ |

---

## You've Completed the Workshop!

| What You Built | How You Built It |
|----------------|-----------------|
| A fully migrated Quarkus 3.x Product CRUD API | Layer-by-layer migration driven by GitHub Copilot Chat |
| A reusable Migration Analyser agent | `.github/agents/migration-analyser.agent.md` |
| A reusable migration principles prompt | `.github/prompts/migration-principles.prompt.md` |
| A phased migration plan and task list | `workshop/migration-tasks.md` |

The same workflow — Analyse → Principles → Plan → Execute — applies to any Spring Boot application you need to migrate to Quarkus. The agent and principles file you created today are reusable starting points for your next migration.
