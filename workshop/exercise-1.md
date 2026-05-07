# Exercise 1 — Analyse the Codebase & Identify Gaps

> **Goal:** Create a custom Copilot agent called "Migration Analyser" that reads the Spring Boot codebase and produces a structured gap report mapping every Spring construct to its Quarkus equivalent.

> **Time:** ~5 minutes &nbsp;|&nbsp; **Standalone:** No prior exercises needed &nbsp;|&nbsp; **Track:** Required for all subsequent exercises

---

## Context

The `Product` CRUD service is live and working on Spring Boot 2.x. Before changing anything, you need a clear picture of what exists and what must change for Quarkus 3.x. Rather than reading every file manually, you'll create a custom Copilot agent that does the analysis for you — reading the codebase, identifying Spring Boot constructs, and mapping each to its Quarkus replacement.

The layers that need migration:

1. **Build configuration** — `pom.xml` Spring Boot parent and starters → Quarkus BOM and extensions
2. **Application entry point** — `@SpringBootApplication` → no equivalent needed in Quarkus
3. **Model** — `javax.persistence.*` → `jakarta.persistence.*`
4. **Repository** — `JpaRepository` → Quarkus Panache `PanacheRepository`
5. **Controller** — Spring MVC annotations → Jakarta REST annotations
6. **API docs** — manual Springfox `SwaggerConfig` → SmallRye OpenAPI (zero config)
7. **Runtime config** — `application.properties` Spring-style keys → Quarkus-style keys

---

## Step 0 — Run the App

Before analysing the code, run the application so you can see the current state of the API firsthand.

### Check Your Java Version

Spring Boot 2.x in this project requires **Java 8**. If you have Java 17 or higher installed, you need to set `JAVA_HOME` to point to a Java 8 installation before building.

Open a PowerShell terminal and check your current Java version:

```powershell
java -version
```

If the output shows Java 8 (version 1.8.x), skip to the [Build and Run](#build-and-run) section below.

If the output shows Java 17 or higher, continue to set `JAVA_HOME` to Java 8.

### Set JAVA_HOME to Java 8 (if needed)

Find your Java 8 installation path. Common locations:
- Eclipse Adoptium: `C:\Program Files\Eclipse Adoptium\jdk-8.0.482.8-hotspot` (example)
- Oracle JDK: `C:\Program Files\Java\jdk1.8.0_xxx` (example)
- OpenJDK: `C:\Program Files\OpenJDK\jdk-8` (example)

In PowerShell, set `JAVA_HOME` and update `PATH`:

```powershell
$env:JAVA_HOME = "C:\Program Files\Eclipse Adoptium\jdk-8.0.482.8-hotspot"
$env:PATH = "$env:JAVA_HOME\bin;" + $env:PATH
```

Replace the path above with your actual Java 8 installation path if it's different.

Verify the change:

```powershell
java -version
```

It should now show Java 8.

### Build and Run

Use the Maven wrapper to clean, build, and package the application:

```powershell
.\mvnw clean package
```

Wait for the build to complete (it may take a minute or two).

Once the build succeeds, run the JAR directly:

```powershell
java -jar target\crud-1.0.0.jar
```

You should see output indicating the server started on port 8888.

### View the Swagger UI

Open your browser and navigate to:

```
http://localhost:8888/swagger-ui.html
```

Explore what's already there:

- Review the five endpoints: `GET /api/products`, `POST /api/products`, `GET /api/products/{id}`, `PUT /api/products/{id}`, `DELETE /api/products/{id}`
- Notice the `Product` model fields: `id`, `name`, `description`, `price`, `createdAt`, `updatedAt`

This is the baseline. Everything must work identically after migration. Keep the app running as you work through Step 1.

---

## Step 1 — Create the Migration Analyser Agent

1. Open **Copilot Chat** in VS Code
2. Type `/create-agent` and send the following as your prompt:

```
Create a custom agent called "Migration Analyser" at .github/agents/migration-analyser.agent.md with the following content:

---
name: Migration Analyser
description: Analyses a Spring Boot codebase and produces a structured gap report mapping Spring constructs to their Quarkus 3.x equivalents.
tools: [read, search]
---

You are a senior Java engineer specialising in cloud-native framework migrations.

When the user asks you to analyse this codebase for a Quarkus migration, you will:

1. Read all source files under src/main/java and src/main/resources
2. Read pom.xml to identify all Spring Boot dependencies
3. For each file or configuration area, identify the specific Spring Boot concept in use
4. Map each concept to its Quarkus 3.x equivalent
5. Output a structured gap report as a markdown table with the following columns:

| Layer | File | Spring Boot Concept | Quarkus Equivalent | Change Required |
|-------|------|--------------------|--------------------|-----------------|

One row per distinct change required. Be thorough — check all files across all layers before producing the report.

Ask the user to confirm the target Quarkus version if they have not specified one.
```

3. Review and accept the generated file.

---

## Step 2 — Run the Agent

1. Open **Copilot Chat** in VS Code
2. Click the **Agent** dropdown at the bottom of the chat input (it says "Agent" by default) and select **Migration Analyser** from the list of custom agents
3. Send the following message:

```
Analyse this Spring Boot codebase and produce a migration gap report for moving to Quarkus 3.x.
Target version: Quarkus 3.x with Java 17.
```

4. Wait for the agent to read all source files and return the gap report table.

---

## Step 3 — Validate the Output

The agent's gap report should identify **all** of the following. Use this as your checklist:

| Layer | Expected Gap |
|-------|-------------|
| `pom.xml` | `spring-boot-starter-parent` must be replaced with Quarkus BOM |
| `pom.xml` | `spring-boot-starter-web` must be replaced with `quarkus-resteasy-reactive-jackson` |
| `pom.xml` | `spring-boot-starter-data-jpa` must be replaced with `quarkus-hibernate-orm-panache` |
| `pom.xml` | `springfox-swagger2` and `springfox-swagger-ui` must be removed; add `quarkus-smallrye-openapi` |
| `CrudApplication.java` | `@SpringBootApplication` entry point not needed; delete file |
| `Product.java` | All `javax.persistence.*` imports → `jakarta.persistence.*` |
| `ProductRepository.java` | `JpaRepository<Product, Long>` → implement `PanacheRepository<Product>` with `@ApplicationScoped` |
| `MainController.java` | `@RestController`, `@RequestMapping`, `@GetMapping`, `@PostMapping`, `@PutMapping`, `@DeleteMapping`, `@Autowired` — all must change |
| `SwaggerConfig.java` | Entire file can be deleted; `quarkus-smallrye-openapi` extension auto-configures Swagger UI |
| `AppException.java` | Must extend `WebApplicationException` (Jakarta RS) instead of `RuntimeException` |
| `application.properties` | All `spring.*` keys must be replaced with `quarkus.*` equivalents |

> If the agent misses any of the above, open `.github/agents/migration-analyser.agent.md`, refine the instructions in the system prompt, and re-run the agent.

---

## Done?

You now have a clear, agent-generated picture of every change required. Head to [Exercise 2 — Define Migration Principles](exercise-2.md) to capture the governing rules for every migration decision as a reusable prompt file.

**Next: [Exercise 2 →](exercise-2.md)**
