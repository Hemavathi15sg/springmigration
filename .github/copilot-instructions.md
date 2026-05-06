This repository contains a Spring Boot 2.x Product CRUD service being migrated to Quarkus 3.9.5.

## Project Facts

- **Java package root:** `com.englishcentral.crud`
- **Java version target:** 17
- **Source framework:** Spring Boot 2.0.4 with Spring Data JPA, Spring MVC, Springfox Swagger 2.7
- **Target framework:** Quarkus 3.9.5 with RESTEasy Reactive, Hibernate ORM Panache, SmallRye OpenAPI
- **Database:** H2 in-memory (kept across the migration — no database change required)
- **Build tool:** Maven
- **Source files to migrate:** `Product.java`, `ProductRepository.java`, `MainController.java`, `AppException.java`
- **Files to delete:** `SwaggerConfig.java`, `CrudApplication.java`

## Migration Rules

When helping with any file in this project, apply the rules from the `quarkus-migration` skill:
- Annotation mapping (Spring MVC → Jakarta RS, `@Autowired` → `@Inject`)
- Dependency replacement (Spring Boot starters → Quarkus extensions in `pom.xml`)
- Configuration migration (`spring.*` keys → `quarkus.*` keys in `application.properties`)
- Repository pattern (`JpaRepository` interface → `PanacheRepository` class with `@ApplicationScoped`)
- API documentation (`SwaggerConfig.java` deleted; SmallRye OpenAPI provides `/q/swagger-ui` automatically)

## Workshop Structure

This is a hands-on migration workshop. Participants work through five exercises guided by Copilot Chat. Do not change the code speculatively — only apply changes explicitly requested in the exercise prompt.
