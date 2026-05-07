# Exercise 4 — Run the Quarkus Migrator Agent

> **Goal:** Create the **Quarkus Migrator** custom agent, then trigger it with a single prompt. The agent reads the migration task list and the `quarkus-migration` skill rules, migrates every source file in phase order, verifies the build compiles cleanly, and reports the outcome — all without step-by-step instructions from you.

> **Time:** ~15 minutes &nbsp;|&nbsp; **Prerequisite:** Phased plan and `migration-tasks.md` created ([Exercise 3](exercise-3.md)) &nbsp;|&nbsp; **Track:** Required — final exercise

---

## Context

In the previous exercises you:
- Generated a gap report identifying 11 Spring → Quarkus deltas (Exercise 1)
- Created the `quarkus-migration` skill and `copilot-instructions.md` (Exercise 2)
- Produced a phased plan and `migration-tasks.md` (Exercise 3)

Now you will create a **custom agent** — a specialised Copilot mode with built-in instructions and tool access — that reads everything you built and executes the complete migration autonomously.

A custom agent differs from a simple chat prompt because it:
- Has persistent instructions baked in (the 8-step migration procedure)
- Knows which tools to use (`read`, `edit`, `create`, `delete`, `run`)
- Runs build verification and retries automatically if compilation fails

---

## Step 1 — Create the Quarkus Migrator Agent

The agent file already exists in the repository at [.github/agents/quarkus-migrator.agent.md](.github/agents/quarkus-migrator.agent.md). Inspect it before running:

1. Open the file and read the frontmatter (`name`, `description`, `tools`) and the 8-step migration procedure.
2. Notice the exact BOM XML in Step 2a and the exact annotation mapping table in Step 5 — these prevent the common groupId mistakes that break Quarkus builds.

**If you want to understand how the agent was authored**, paste this prompt in Copilot Chat (`@workspace` mode):

```
@workspace Explain what the Quarkus Migrator agent in .github/agents/quarkus-migrator.agent.md does and how it differs from a plain chat prompt.
```

---

## Step 2 — Run the Migration with a Single Prompt

Open Copilot Chat and switch to the **Quarkus Migrator** agent using the agent picker (`@Quarkus Migrator` or selecting it from the mode dropdown).

Paste this single prompt:

```
Run the full Quarkus migration.

1. Read workshop/migration-tasks.md for the ordered task list.
2. Read .github/skills/quarkus-migration/SKILL.md for the five migration rules.
3. Migrate all files in phase order (Phase 1: pom.xml + application.properties,
   Phase 2: Product.java, Phase 3: ProductRepository.java,
   Phase 4: MainController.java, Phase 5: AppException.java + delete
   SwaggerConfig.java and CrudApplication.java).
4. After Phase 1, verify that mvn dependency:resolve -q succeeds before continuing.
5. After all phases, run mvn compile -q and report the result.
6. If compilation fails, identify the error, fix the file, and retry up to 3 times.
7. Report every file changed and confirm the final build status.
```

**What to expect:** The agent will produce file edits for each phase, run terminal commands to verify dependencies and compilation, and print a final report. This takes 3–5 minutes.

> **Note:** If your Copilot plan does not support custom agents, use `@workspace` mode and paste the same prompt. The `copilot-instructions.md` and skill will still provide the migration rules automatically.

---

## Step 3 — Review and Accept the Diffs

After the agent finishes, review each changed file in the diff editor before accepting:

| File | Key change to verify |
|------|---------------------|
| `pom.xml` | BOM groupId is `io.quarkus.platform`, extension deps have groupId `io.quarkus` with **no** `<version>` tag, `quarkus-arc` is present |
| `application.properties` | All `spring.*` and `server.*` keys replaced with `quarkus.*`; `quarkus.datasource.db-kind=h2` present |
| `model/Product.java` | All imports changed from `javax.persistence.*` to `jakarta.persistence.*` |
| `repository/ProductRepository.java` | Now a `class` with `@ApplicationScoped` implementing `PanacheRepository<Product>` |
| `controller/MainController.java` | `@RestController` replaced by `@Path` + `@ApplicationScoped`; `@Autowired` → `@Inject`; `ResponseEntity` → `Response` |
| `exception/AppException.java` | Extends `WebApplicationException`; constructor calls `super(Response.status(...).entity(...).build())` |
| `config/SwaggerConfig.java` | **Deleted** |
| `CrudApplication.java` | **Deleted** |

Accept all changes once you are satisfied.

---

## Step 4 — Verify the Application Starts

Start Quarkus dev mode:

```bash
mvn quarkus:dev
```

Wait for the startup message:

```
__  ____  __  _____   ___  __ ____  ______
 --/ __ \/ / / / _ | / _ \/ //_/ / / / __/
 -/ /_/ / /_/ / __ |/ , _/ ,< / /_/ /\ \
--\___\_\____/_/ |_/_/|_/_/|_|\____/___/
Quarkus 3.9.5 on JVM started in ...s. Listening on: http://localhost:8080
```

Open **http://localhost:8080/q/swagger-ui** and test each endpoint:

| # | Method | URL | Expected response |
|---|--------|-----|-------------------|
| 1 | `GET` | `/api/products` | `200 OK` — empty array `[]` |
| 2 | `POST` | `/api/products` | `201 Created` — `{"success":true,"message":"Product was successfully saved."}` |
| 3 | `GET` | `/api/products/1` | `200 OK` — the product you just created |
| 4 | `PUT` | `/api/products/1` | `200 OK` — updated product |
| 5 | `DELETE` | `/api/products/1` | `200 OK` — deletion confirmation |

---

## Validation Checklist

Confirm all 12 items before moving on:

- [ ] `pom.xml` — `<parent>` block removed
- [ ] `pom.xml` — `<dependencyManagement>` uses `io.quarkus.platform:quarkus-bom:3.9.5`
- [ ] `pom.xml` — Quarkus extension `<dependency>` entries use `io.quarkus` with no `<version>`
- [ ] `pom.xml` — `quarkus-arc` extension present
- [ ] `pom.xml` — Quarkus Maven plugin uses `io.quarkus.platform` groupId
- [ ] `application.properties` — no `spring.*` or `server.*` keys remain
- [ ] `Product.java` — all imports use `jakarta.persistence.*`
- [ ] `ProductRepository.java` — `class` (not `interface`), `@ApplicationScoped`, `PanacheRepository<Product>`
- [ ] `MainController.java` — `@Path`, `@Inject`, `Response` return types, `@PathParam`
- [ ] `AppException.java` — extends `WebApplicationException`
- [ ] `SwaggerConfig.java` — deleted
- [ ] `CrudApplication.java` — deleted
- [ ] `mvn compile -q` — exits with code 0
- [ ] `http://localhost:8080/q/swagger-ui` — shows 5 endpoints

---

## Done?

You have completed the workshop! The Spring Boot CRUD service is now fully migrated to Quarkus 3.9.5 with:

- **RESTEasy Reactive** (JAX-RS) replacing Spring MVC
- **Hibernate ORM Panache** replacing Spring Data JPA
- **SmallRye OpenAPI** replacing Springfox Swagger
- **Quarkus Arc** (CDI) replacing Spring DI
- A custom agent and skill that encode the migration rules for future use

**← [Back to Exercise 3](exercise-3.md)** | **[Back to README](../README.md)**