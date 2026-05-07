# Agent-Driven Framework Migration: Spring Boot to Quarkus

> **Use GitHub Copilot to migrate a working Spring Boot CRUD app to Quarkus — layer by layer, driven by Copilot Chat and custom agents.**

## The Scenario

Monday, 9:00 AM. Your team's `Product` CRUD service has been running on Spring Boot 2.x and Java 8 for years. It works — but leadership wants cloud-native: faster startup, lower memory footprint, and native image readiness for container deployments. The verdict is in: migrate to Quarkus 3.x this sprint.

The service isn't complex, but every layer needs to change:

- `pom.xml` — Spring Boot parent and starters must become Quarkus BOM and extensions
- `application.properties` — Spring-style config must move to Quarkus format
- `Product.java` — `javax.persistence` imports become `jakarta.persistence`
- `ProductRepository.java` — `JpaRepository` becomes a Quarkus `PanacheRepository`
- `MainController.java` — Spring MVC annotations become Jakarta REST annotations
- `SwaggerConfig.java` — manual Springfox config is deleted; SmallRye OpenAPI takes over

This workshop walks you through exactly that — using GitHub Copilot Chat and a custom agent to analyse, plan, and execute the migration one layer at a time.

## What You'll Learn

| Tool | How You'll Use It |
|------|------------------|
| **Custom Copilot Agent** | Build a Migration Analyser (`.github/agents/`) that reads the codebase and produces a structured gap report |
| **Agent Skill** | Encode the migration rules as a `SKILL.md` under `.github/skills/quarkus-migration/` — Copilot **auto-invokes** it whenever a migration-related prompt is detected, no manual attachment needed |
| **Custom Instructions** | Add `.github/copilot-instructions.md` so Copilot always has project context (package name, target stack, Java version) loaded in every Chat session |
| **GitHub Copilot Chat (agent mode)** | Drive each migration step — from `pom.xml` to Jakarta REST — with the skill silently governing every response |

## Prerequisites

- Java 17+ installed
- Maven 3.8+ installed
- Valid GitHub Copilot subscription (Individual, Business, or Enterprise)
- [VS Code](https://code.visualstudio.com/download) with the **GitHub Copilot Chat** extension installed
- [Git CLI](https://git-scm.com/downloads) for version control

> **Starter code provided:** This repository is the Spring Boot app you will migrate. Clone it and you are ready.

## Workshop Structure

| # | Exercise | Goal | Time |
|---|----------|------|------|
| 1 | [Analyse the Codebase](workshop/exercise-1.md) | Create a Migration Analyser agent and generate a gap report | ~5 min |
| 2 | [Define Migration Principles](workshop/exercise-2.md) | Capture governing migration rules as a reusable prompt file | ~8 min |
| 3 | [Plan the Migration](workshop/exercise-3.md) | Produce a phased plan and file-level task list | ~8 min |
| 4 | [Run the Quarkus Migrator Agent](workshop/exercise-4.md) | Create & run the Quarkus Migrator agent; end-to-end migration with build verification | ~15 min |
| — | **Total** | | **~36 min** |

## What Gets Migrated

| File | Spring Boot Concept | Quarkus Replacement |
|------|--------------------|--------------------|
| `pom.xml` | `spring-boot-starter-parent` + starters | `quarkus-bom` + Quarkus extensions |
| `application.properties` | `spring.datasource.*`, `spring.jpa.*` | `quarkus.datasource.*`, `quarkus.hibernate-orm.*` |
| `Product.java` | `javax.persistence.*` | `jakarta.persistence.*` |
| `ProductRepository.java` | `JpaRepository<Product, Long>` | `PanacheRepository<Product>` |
| `MainController.java` | `@RestController`, `@GetMapping`, `@Autowired` | `@Path`, `@GET`, `@Inject` (Jakarta REST + CDI) |
| `SwaggerConfig.java` | Manual Springfox bean configuration | Deleted — SmallRye OpenAPI handles it automatically |
| `AppException.java` | `RuntimeException` | `WebApplicationException` (Jakarta RS) |

## Get Started

Clone or fork this repository, then run the existing Spring Boot application first so you can see the baseline:

```shell
mvn spring-boot:run
```

Open [http://localhost:8888/swagger-ui.html](http://localhost:8888/swagger-ui.html) to explore the current state of the API — five CRUD endpoints for `Product`. This is what you will keep working, just on Quarkus.

Then start here: **[Exercise 1 — Analyse the Codebase](workshop/exercise-1.md)**

## Resources

- [Quarkus Migration Guide (Spring to Quarkus)](https://quarkus.io/guides/spring-di)
- [Quarkus Hibernate ORM with Panache](https://quarkus.io/guides/hibernate-orm-panache)
- [Quarkus RESTEasy Reactive](https://quarkus.io/guides/resteasy-reactive)
- [SmallRye OpenAPI](https://quarkus.io/guides/openapi-swaggerui)
- [Quarkus All Configuration Options](https://quarkus.io/guides/all-config)
| `SwaggerConfig.java` | Manual Springfox bean configuration | Deleted — SmallRye OpenAPI handles it automatically |
| `AppException.java` | `RuntimeException` | `WebApplicationException` (Jakarta RS) |

## Get Started

Clone or fork this repository, then run the existing Spring Boot application first so you can see the baseline:

```shell
mvn spring-boot:run
```

Open [http://localhost:8888/swagger-ui.html](http://localhost:8888/swagger-ui.html) to explore the current state of the API — five CRUD endpoints for `Product`. This is what you will keep working, just on Quarkus.

Then start here: **[Exercise 1 — Analyse the Codebase](workshop/exercise-1.md)**

## Resources

- [Quarkus Migration Guide (Spring to Quarkus)](https://quarkus.io/guides/spring-di)
- [Quarkus Hibernate ORM with Panache](https://quarkus.io/guides/hibernate-orm-panache)
- [Quarkus RESTEasy Reactive](https://quarkus.io/guides/resteasy-reactive)
- [SmallRye OpenAPI](https://quarkus.io/guides/openapi-swaggerui)
- [Quarkus All Configuration Options](https://quarkus.io/guides/all-config)