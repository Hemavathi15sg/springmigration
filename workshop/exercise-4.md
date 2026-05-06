# Exercise 4 — Migrate Dependencies & Configuration

> **Goal:** Use GitHub Copilot Chat to migrate `pom.xml` and `application.properties` from Spring Boot to Quarkus 3.x, then verify the Quarkus development server starts cleanly — before touching any Java source files.

> **Time:** ~10 minutes &nbsp;|&nbsp; **Prerequisite:** Task list created ([Exercise 3](exercise-3.md)) &nbsp;|&nbsp; **Track:** Required for Exercise 5

---

## Context

Phase 1 of the migration plan is the foundation: without a valid Quarkus `pom.xml` and a correctly formatted `application.properties`, nothing else can compile or run. This exercise handles both files and ends with a working Quarkus dev server — even though the Java source files still contain Spring Boot annotations. That compile error is expected and is resolved in Exercise 5.

**What changes in this exercise:**

| File | What Leaves | What Arrives |
|------|------------|--------------|
| `pom.xml` | `spring-boot-starter-parent`, all `spring-boot-starter-*` deps, Springfox swagger deps | `quarkus-bom` (BOM import), Quarkus extensions (see below) |
| `application.properties` | All `spring.*` and `server.*` keys | All `quarkus.*` equivalents |

**Quarkus extensions to add:**

| Extension Artifact ID | Replaces |
|----------------------|---------|
| `quarkus-resteasy-reactive-jackson` | `spring-boot-starter-web` |
| `quarkus-hibernate-orm-panache` | `spring-boot-starter-data-jpa` |
| `quarkus-jdbc-h2` | `h2` (runtime scope) |
| `quarkus-smallrye-openapi` | `springfox-swagger2` + `springfox-swagger-ui` |

---

## Step 1 — Migrate `pom.xml`

> The `quarkus-migration` skill and `.github/copilot-instructions.md` are loaded automatically. Copilot already knows the target Quarkus version, required extensions, and replacement rules — no attachment needed.

1. Open `pom.xml` in VS Code (make it the active editor tab)
2. Open **Copilot Chat** in agent mode and send the following prompt:

```
Migrate pom.xml from Spring Boot 2.x to Quarkus 3.x. Apply these changes:

1. Replace the <parent> block (spring-boot-starter-parent) with Quarkus BOM import inside
   <dependencyManagement>. Use Quarkus version 3.9.5.

2. Remove all spring-boot-starter-* dependencies.

3. Remove springfox-swagger2, springfox-swagger-ui, and rest-assured from dependencies.

4. Add the quarkus-maven-plugin to <build><plugins> using the same Quarkus version.
   Remove the spring-boot-maven-plugin.

5. Add these Quarkus extension dependencies (groupId: io.quarkus):
   - quarkus-resteasy-reactive-jackson
   - quarkus-hibernate-orm-panache
   - quarkus-jdbc-h2
   - quarkus-smallrye-openapi

6. Update <java.version> property to 17 (or remove it and set maven.compiler.source and
   maven.compiler.target to 17).

7. Keep groupId, artifactId, version, name, and description unchanged.

Do not add any dependencies not listed here. Show the complete updated pom.xml.
```

3. Review the diff carefully. Verify:
   - No `spring-boot-*` entries remain
   - All four Quarkus extensions are present
   - `quarkus-maven-plugin` is present in `<build>`
   - Java version is 17
4. **Accept** the changes.

---

## Step 2 — Migrate `application.properties`

1. Open `src/main/resources/application.properties` in VS Code
2. In Copilot Chat (same session), send:

```
Migrate application.properties from Spring Boot format to Quarkus 3.x. Apply these changes:

1. Replace spring.datasource.url with quarkus.datasource.jdbc.url
   Use: jdbc:h2:mem:testdb;DB_CLOSE_DELAY=-1;DB_CLOSE_ON_EXIT=FALSE

2. Replace spring.datasource.driver-class-name with quarkus.datasource.jdbc.driver
   Use: org.h2.Driver

3. Replace spring.datasource.username / spring.datasource.password with
   quarkus.datasource.username and quarkus.datasource.password

4. Replace spring.jpa.hibernate.ddl-auto=create with quarkus.hibernate-orm.database.generation=drop-and-create

5. Replace spring.jpa.show-sql=true with quarkus.hibernate-orm.log.sql=true

6. Replace server.port=8888 with quarkus.http.port=8080
   (Quarkus default port is 8080; we align to that)

7. Add: quarkus.datasource.db-kind=h2

8. Remove any remaining spring.* or server.* keys.

Show the complete updated application.properties.
```

3. Review the diff. Verify no `spring.*` or `server.*` keys remain.
4. **Accept** the changes.

---

## Step 3 — Verify Quarkus Starts

Run the Quarkus dev server:

```shell
mvn quarkus:dev
```

> **Expected result at this stage:** Quarkus will **fail to compile** because `MainController.java`, `ProductRepository.java`, and other files still use Spring Boot imports. That is correct — you have only changed the build and config so far.

To confirm Phase 1 is correct anyway, check the error output:

- Errors should be **compilation errors** about `org.springframework.*` packages not found — that means Quarkus is picking up the source files correctly
- Errors should **not** be about `pom.xml` syntax, missing plugins, or Quarkus BOM resolution failures

If you see BOM or plugin errors, revisit Step 1 before continuing.

> **Optional quick check:** Run `mvn dependency:resolve -q` to confirm all four Quarkus extension JARs resolve without errors before attempting `quarkus:dev`.

---

## Validation Checklist

Before moving to Exercise 5, confirm:

| Check | Status |
|-------|--------|
| `pom.xml` has no `spring-boot-*` dependencies | ☐ |
| `pom.xml` includes `quarkus-resteasy-reactive-jackson` | ☐ |
| `pom.xml` includes `quarkus-hibernate-orm-panache` | ☐ |
| `pom.xml` includes `quarkus-jdbc-h2` | ☐ |
| `pom.xml` includes `quarkus-smallrye-openapi` | ☐ |
| `pom.xml` has `quarkus-maven-plugin` in `<build>` | ☐ |
| `application.properties` has `quarkus.datasource.jdbc.url` | ☐ |
| `application.properties` has `quarkus.http.port=8080` | ☐ |
| `mvn dependency:resolve` completes without errors | ☐ |
| `mvn quarkus:dev` fails with Spring import compilation errors (not BOM/plugin errors) | ☐ |

---

## Done?

Phase 1 is complete. The build foundation is in place. Head to [Exercise 5 — Migrate the Application Code](exercise-5.md) to migrate every Java source file layer by layer and bring the Quarkus API fully online.

**Next: [Exercise 5 →](exercise-5.md)**
