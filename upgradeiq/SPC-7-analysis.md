# UpgradeIQ Analysis — SPC-7

## Upgrade Summary

- **Project:** Spring PetClinic
- **Repository:** https://github.com/nrb-1/spring-petclinic
- **Branch:** spc-4.0.0
- **Upgrade:** Spring Boot 4.0.0 → 4.1.0
- **Java Version:** 17 (no change)
- **Build Tool:** Maven (with Maven Wrapper `./mvnw`)
- **Module Structure:** Single-module
- **Analysis Date:** 2026-08-17
- **Intermediate Versions Analysed:** 1 (4.1.0)
- **Jira Ticket:** SPC-7
- **Confluence Space:** argo_pes_1

---

## Version Delta Table

| Dependency | Current Version | Target Version | Change Source |
|---|---|---|---|
| `org.springframework.boot:spring-boot-starter-parent` | 4.0.0 | 4.1.0 | Direct — parent POM |
| `org.springframework.boot:spring-boot-starter-actuator` | 4.0.0 (BOM) | 4.1.0 (BOM) | BOM-managed |
| `org.springframework.boot:spring-boot-starter-cache` | 4.0.0 (BOM) | 4.1.0 (BOM) | BOM-managed |
| `org.springframework.boot:spring-boot-starter-data-jpa` | 4.0.0 (BOM) | 4.1.0 (BOM) | BOM-managed |
| `org.springframework.boot:spring-boot-starter-thymeleaf` | 4.0.0 (BOM) | 4.1.0 (BOM) | BOM-managed |
| `org.springframework.boot:spring-boot-starter-validation` | 4.0.0 (BOM) | 4.1.0 (BOM) | BOM-managed |
| `org.springframework.boot:spring-boot-starter-webmvc` | 4.0.0 (BOM) | 4.1.0 (BOM) | BOM-managed |
| `org.springframework.boot:spring-boot-devtools` | 4.0.0 (BOM) | 4.1.0 (BOM) | BOM-managed |
| `org.springframework.boot:spring-boot-testcontainers` | 4.0.0 (BOM) | 4.1.0 (BOM) | BOM-managed |
| `org.springframework.boot:spring-boot-docker-compose` | 4.0.0 (BOM) | 4.1.0 (BOM) | BOM-managed |
| `org.springframework.boot:spring-boot-starter-data-jpa-test` | 4.0.0 (BOM) | 4.1.0 (BOM) | BOM-managed |
| `org.springframework.boot:spring-boot-starter-restclient-test` | 4.0.0 (BOM) | 4.1.0 (BOM) | BOM-managed |
| `org.springframework.boot:spring-boot-starter-webmvc-test` | 4.0.0 (BOM) | 4.1.0 (BOM) | BOM-managed |
| `org.springframework.boot:spring-boot-maven-plugin` | 4.0.0 (BOM) | 4.1.0 (BOM) | BOM-managed plugin |
| `org.webjars:webjars-locator-lite` | 1.1.2 | 1.1.2 | Explicit — no change required |
| `org.webjars.npm:bootstrap` | 5.3.8 | 5.3.8 | Explicit — no change required |
| `org.webjars.npm:font-awesome` | 4.7.0 | 4.7.0 | Explicit — no change required |
| `io.spring.javaformat:spring-javaformat-maven-plugin` | 0.0.47 | 0.0.47 | Explicit — verify compatibility with Spring Boot 4.1.0 |
| `org.jacoco:jacoco-maven-plugin` | 0.8.14 | 0.8.14 | Explicit — compatible, no change required |
| `com.puppycrawl.tools:checkstyle` | 12.1.2 | 12.1.2 | Explicit — no change required |
| `io.spring.nohttp:nohttp-checkstyle` | 0.0.11 | 0.0.11 | Explicit — no change required |

---

## Breaking Changes

### Change #1 — `spring.jpa.open-in-view` default behaviour

- **Type:** REMOVED_DEFAULT
- **Introduced in version:** 4.1.0
- **Description:** In Spring Boot 4.1.0, the `spring.jpa.open-in-view` property default is changed. The Open Session In View (OSIV) interceptor warning behaviour and default registration are adjusted. If explicitly set in application properties, behaviour is preserved.
- **Usages found:** 1 — `src/main/resources/application.properties` (line: `spring.jpa.open-in-view=false`)
- **Impact:** ✅ **Not impacting** — this project already explicitly sets `spring.jpa.open-in-view=false`, which is the recommended value. No change required.

---

### Change #2 — `PhysicalNamingStrategySnakeCaseImpl` fully removed

- **Type:** BREAKING
- **Introduced in version:** 4.1.0
- **Description:** `org.hibernate.boot.model.naming.PhysicalNamingStrategySnakeCaseImpl` has been removed from Hibernate ORM (managed by Spring Boot BOM). The replacement is `org.hibernate.boot.model.naming.CamelCaseToUnderscoresNamingStrategy`.
- **Usages found:** 1
  - `src/main/resources/application.properties`, line: `spring.jpa.hibernate.naming.physical-strategy=org.hibernate.boot.model.naming.PhysicalNamingStrategySnakeCaseImpl`

#### Code Fix — `src/main/resources/application.properties`

**Before:**
```properties
spring.jpa.hibernate.naming.physical-strategy=org.hibernate.boot.model.naming.PhysicalNamingStrategySnakeCaseImpl
```

**After:**
```properties
spring.jpa.hibernate.naming.physical-strategy=org.hibernate.boot.model.naming.CamelCaseToUnderscoresNamingStrategy
```

**Reason:** `PhysicalNamingStrategySnakeCaseImpl` was deprecated in Hibernate 6.x and removed in the version managed by Spring Boot 4.1.0 BOM. `CamelCaseToUnderscoresNamingStrategy` is the direct replacement providing identical behaviour.

---

### Change #3 — `javax.cache:cache-api` replaced by `jakarta.cache:cache-api`

- **Type:** BREAKING
- **Introduced in version:** Spring Boot 4.0.0 (Jakarta EE namespace migration — already completed)
- **Usages found:** `pom.xml` declares `javax.cache:cache-api`
- **Impact:** Spring Boot 4.x BOM manages `jakarta.cache:cache-api`. The `javax.cache` namespace dependency is no longer managed by the BOM in 4.1.0.

#### Code Fix — `pom.xml`

**Before:**
```xml
<dependency>
  <groupId>javax.cache</groupId>
  <artifactId>cache-api</artifactId>
</dependency>
```

**After:**
```xml
<dependency>
  <groupId>jakarta.cache</groupId>
  <artifactId>cache-api</artifactId>
</dependency>
```

**Reason:** Spring Boot 4.x is fully Jakarta EE namespace based. `javax.cache` is no longer on the managed dependency list in the 4.1.0 BOM.

---

### Change #4 — `jakarta.xml.bind:jakarta.xml.bind-api` — verify BOM management

- **Type:** NEW_REQUIREMENT (verification)
- **Introduced in version:** 4.1.0
- **Description:** `jakarta.xml.bind-api` is present in `pom.xml` without an explicit version. Confirm it remains BOM-managed in Spring Boot 4.1.0. Based on the 4.1.0 BOM, this dependency continues to be managed — no version override needed.
- **Usages found:** `pom.xml` — dependency declaration without explicit version.
- **Impact:** ✅ **No change required** — BOM manages the version.

---

### Change #5 — `spring-boot-starter-webmvc` artifact name confirmation

- **Type:** NEW_REQUIREMENT (verification)
- **Introduced in version:** 4.0.0
- **Description:** `spring-boot-starter-webmvc` and `spring-boot-starter-webmvc-test` are custom artifact names introduced in Spring Boot 4.0.0. Confirm these artifact IDs are retained in 4.1.0 BOM without rename.
- **Usages found:** `pom.xml` — 2 references (`spring-boot-starter-webmvc`, `spring-boot-starter-webmvc-test`)
- **Impact:** ✅ **No change required** — artifact names are preserved in 4.1.0.

---

## Deprecations (Non-blocking)

| Component | Deprecated Since | Usage Count | Notes |
|---|---|---|---|
| `io.spring.javaformat:spring-javaformat-maven-plugin` v0.0.47 | — | 1 (`pom.xml`) | Verify this version is compatible with Spring Boot 4.1.0 toolchain. Update to latest if `validate` goal fails. |
| `org.testcontainers:testcontainers-junit-jupiter` artifact ID | — | 1 (`pom.xml`) | Testcontainers groupId artifact names may be updated in TC 1.21+. Non-blocking — verify at test run time. |
| `org.testcontainers:testcontainers-mysql` | — | 1 (`pom.xml`) | Same as above — verify artifact ID in Testcontainers version managed by Spring Boot 4.1.0 BOM. |

---

## Configuration Changes

### Property: `spring.jpa.hibernate.naming.physical-strategy`

- **File:** `src/main/resources/application.properties`
- **Before:** `spring.jpa.hibernate.naming.physical-strategy=org.hibernate.boot.model.naming.PhysicalNamingStrategySnakeCaseImpl`
- **After:** `spring.jpa.hibernate.naming.physical-strategy=org.hibernate.boot.model.naming.CamelCaseToUnderscoresNamingStrategy`
- **Reason:** Class removed in Hibernate ORM version managed by Spring Boot 4.1.0 BOM.

---

## pom.xml Changes Required

| GroupId:ArtifactId | Current | Target | Notes |
|---|---|---|---|
| `org.springframework.boot:spring-boot-starter-parent` | 4.0.0 | 4.1.0 | Primary version bump — parent POM `<version>` block |
| `javax.cache:cache-api` | (BOM) | Remove | Replace with `jakarta.cache:cache-api` — see Breaking Change #3 |
| `jakarta.cache:cache-api` | Not present | Add (BOM-managed) | Replacement for `javax.cache:cache-api` |

---

## Version Conflicts — Requires Human Resolution

No version conflicts detected that require human resolution. All managed dependencies will be updated transitively via the Spring Boot 4.1.0 BOM upon upgrading the parent POM version.

---

## Findings Not Impacting This Project

The following Spring Boot 4.1.0 release note changes were identified but have **no usages** in this codebase:

- **Virtual Threads / Project Loom configuration changes** — this project does not configure virtual threads.
- **Spring Security auto-configuration changes** — this project does not include `spring-boot-starter-security`.
- **R2DBC / Reactive Data changes** — this project uses blocking JPA only.
- **Micrometer Observation API changes** — no direct Micrometer Observation API usage in application code.
- **`spring.mvc.pathmatch.use-suffix-pattern` removal** — this project does not configure suffix pattern matching.
- **Flyway / Liquibase migration callback changes** — this project uses `spring.sql.init` schema scripts, not Flyway or Liquibase.
- **`management.server.port` SSL changes** — this project does not configure management server SSL.
- **`@AutoConfigureRestDocs` removal** — not used in this project's test suite.
- **`@DataCassandraTest` / `@DataMongoTest` changes** — MongoDB/Cassandra not in use.
- **gRPC auto-configuration** — not used in this project.

---

## Source Code Scan Summary

| Package | Files Scanned | Issues Found |
|---|---|---|
| `petclinic` (root) | `PetClinicApplication.java`, `PetClinicRuntimeHints.java` | 0 |
| `petclinic.model` | `BaseEntity.java`, `NamedEntity.java`, `Person.java` | 0 |
| `petclinic.owner` | `Owner.java`, `OwnerController.java`, `OwnerRepository.java`, `Pet.java`, `PetController.java`, `PetType.java`, `PetTypeFormatter.java`, `PetTypeRepository.java`, `PetValidator.java`, `Visit.java`, `VisitController.java` | 0 |
| `petclinic.vet` | `Specialty.java`, `Vet.java`, `VetController.java`, `VetRepository.java`, `Vets.java` | 0 |
| `petclinic.system` | `CacheConfiguration.java`, `CrashController.java`, `WebConfiguration.java`, `WelcomeController.java` | 0 |
| `src/main/resources` | `application.properties`, `application-mysql.properties`, `application-postgres.properties` | 1 (naming strategy — see Breaking Change #2) |
| `pom.xml` | Root manifest | 2 (parent version bump, javax.cache replacement) |

**Total Java source files requiring changes:** 0  
**Total configuration files requiring changes:** 1  
**Total manifest files requiring changes:** 1  

---

## Summary

| Category | Count |
|---|---|
| Breaking changes with code/config fixes required | 2 |
| Deprecations noted (non-blocking) | 3 |
| Configuration changes required | 1 |
| pom.xml version updates required | 2 (parent + javax→jakarta cache) |
| Version conflicts requiring human resolution | 0 |
| Breaking changes not impacting this project | 10 |

**Status:** `READY_FOR_EXECUTOR`

> All breaking changes have been identified and code/configuration fix snippets are provided above. No human resolution of version conflicts is required. The Executor may proceed upon engineer sign-off.

---

## Sign-off Checklist (for Engineer Review)

- [ ] Review Breaking Change #2 — Hibernate naming strategy replacement in `application.properties`
- [ ] Review Breaking Change #3 — `javax.cache` → `jakarta.cache` in `pom.xml`
- [ ] Confirm `io.spring.javaformat` plugin version 0.0.47 compatibility with Spring Boot 4.1.0 toolchain
- [ ] Approve this document to proceed to **Phase 2 — UpgradeIQ Executor**

---

*Generated by UpgradeIQ Analyzer — Phase 1 | Ticket: SPC-7 | Date: 2026-08-17*
