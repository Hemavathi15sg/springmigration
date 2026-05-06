# Exercise 2 — Set Up Custom Instructions & the Migration Skill

> **Goal:** Give Copilot permanent awareness of this project by creating `.github/copilot-instructions.md`, then encode all five migration principles as an **agent skill** under `.github/skills/quarkus-migration/SKILL.md` so Copilot auto-invokes the right rules for every migration prompt — without you attaching anything.

> **Time:** ~8 minutes &nbsp;|&nbsp; **Prerequisite:** Exercise 1 completed (gap report in hand) &nbsp;|&nbsp; **Track:** Required for Exercises 3, 4, and 5

---

## Context

Two Copilot customisation mechanisms work together here:

| Mechanism | File location | When it fires | What to put in it |
|-----------|--------------|---------------|--------------------|
| **Custom Instructions** | `.github/copilot-instructions.md` | **Always** — loaded into every Copilot Chat session in this repo automatically | Stable project facts: package name, Spring Boot version, target Quarkus version, Java version, DB |
| **Agent Skill** | `.github/skills/quarkus-migration/SKILL.md` | **On demand** — Copilot reads the `description` field and decides when the skill is relevant to the current prompt | Migration rules: annotation mapping, dependency swap, config keys, Panache pattern, OpenAPI |

The key benefit: once these two files exist, you never need to attach, paste, or reference them manually. Copilot handles it.

---

## Step 1 — Create Repository Custom Instructions

1. Open **Copilot Chat** in VS Code (agent mode)
2. Send the following prompt:

```
Create the file .github/copilot-instructions.md with this content:

This repository contains a Spring Boot 2.x Product CRUD service being migrated to Quarkus 3.9.5.

Project facts:
- Java package root: com.englishcentral.crud
- Java version target: 17
- Source framework: Spring Boot 2.0.4 with Spring Data JPA, Spring MVC, Springfox Swagger
- Target framework: Quarkus 3.9.5 with RESTEasy Reactive, Hibernate ORM Panache, SmallRye OpenAPI
- Database: H2 in-memory (kept across migration)
- Build tool: Maven

When helping with this project, always apply the migration rules from the quarkus-migration skill.
```

3. Review and **accept** the file.

---

## Step 2 — Create the Quarkus Migration Agent Skill

Agent skills live in `.github/skills/<skill-name>/SKILL.md`. The `name` and `description` fields in the YAML front matter tell Copilot what the skill is for and when to load it automatically.

1. Still in Copilot Chat, send:

```
Create the file .github/skills/quarkus-migration/SKILL.md with the following content exactly:

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

In pom.xml, replace Spring Boot dependencies with Quarkus extensions (groupId: io.quarkus):

| Remove | Add |
|--------|-----|
| `spring-boot-starter-parent` (parent) | `quarkus-bom` in `<dependencyManagement>` |
| `spring-boot-starter-web` | `quarkus-resteasy-reactive-jackson` |
| `spring-boot-starter-data-jpa` | `quarkus-hibernate-orm-panache` |
| `h2` (runtime) | `quarkus-jdbc-h2` |
| `springfox-swagger2`, `springfox-swagger-ui` | `quarkus-smallrye-openapi` |
| `spring-boot-maven-plugin` | `quarkus-maven-plugin` |

Always use Quarkus version 3.9.5. Set Java source/target to 17.

## Rule 3 — Configuration Migration

In application.properties, replace Spring keys with Quarkus equivalents:

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
```

2. Review and **accept** the file.

---

## Step 3 — Validate Both Files

Open each file and confirm:

**`.github/copilot-instructions.md`**

| Check | Expected |
|-------|----------|
| Package root stated | `com.englishcentral.crud` |
| Source and target framework versions listed | Spring Boot 2.0.4 → Quarkus 3.9.5 |
| Reference to the skill present | "apply the migration rules from the quarkus-migration skill" |

**`.github/skills/quarkus-migration/SKILL.md`**

| Check | Expected |
|-------|----------|
| YAML front matter present | `name: quarkus-migration` and `description:` fields |
| Rule 1 present | Annotation mapping table |
| Rule 2 present | Dependency replacement table |
| Rule 3 present | Configuration key mapping table |
| Rule 4 present | `PanacheRepository` before/after code example |
| Rule 5 present | States `SwaggerConfig.java` must be deleted; Swagger UI at `/q/swagger-ui` |

> **How to verify the skill is loaded:** Open Copilot Chat in agent mode, type "migrate Product.java to Quarkus" — Copilot should mention `jakarta.persistence` in its response without you telling it. That confirms the skill was auto-invoked.

---

## Done?

From this point forward, every Copilot Chat session in this repo automatically loads the project context, and every migration-related prompt automatically triggers the `quarkus-migration` skill. Head to [Exercise 3 — Plan the Migration](exercise-3.md) to produce a phased plan and file-level task list.

**Next: [Exercise 3 →](exercise-3.md)**