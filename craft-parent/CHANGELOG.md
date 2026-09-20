# Changelog

All notable changes to `craft-parent` are documented here.

## 2.3.0-SNAPSHOT

- Added `<distributionManagement>` pointing `mvn deploy` at the internal
  Nexus instance (`nexus-releases` / `nexus-snapshots` hosted repositories),
  via a new `nexus.url` property. Replaces the need for each consuming
  service to declare its own `distributionManagement` (several were still
  pointing at a per-service Artifactory).
- This is config-only (no dependency/plugin version changes); the actual
  blocking of direct Maven Central access is enforced by the shared
  `settings.xml` (wildcard `<mirror>`) at the root of `craft-platform`, not
  by anything in this POM.

## 2.2.0-SNAPSHOT

- `maven-surefire-plugin`: added `**/*Tests.java` alongside `**/*Test.java`
  in the unit test includes. Spring Initializr generates the application
  test class as `<AppName>Tests.java` (plural), which the 2.1.0-SNAPSHOT
  pattern silently excluded - discovered by inheriting this parent from
  `inner-order-api`, where the generated `*ApplicationTests.java` ran 0
  tests with no build failure. `maven-failsafe-plugin`'s `**/*IT.java`
  include is unchanged (no equivalent Spring Initializr convention gap).

## 2.1.0-SNAPSHOT

- `maven-compiler-plugin`: set `<release>` from the `java.version` property
  (already in place) and added `-parameters` (via `<parameters>true</parameters>`)
  so parameter names are retained in compiled bytecode.
- Added a `spring-boot-maven-plugin` `pluginManagement` entry with the
  `repackage` and `build-info` executions preconfigured, so a consuming
  service only needs to declare the plugin with no configuration. This
  reverses the 1.2.0-SNAPSHOT decision to exclude it entirely: the plugin is
  now *managed* (versioned/preconfigured) but still never referenced from a
  `<plugins>` section in this repo itself, since no module here is a
  deployable application.
- `maven-resources-plugin`: restored the `@...@` filtering delimiter
  (`useDefaultDelimiters=false`, `delimiters=[@]`) for `application.yml`/
  `.properties`, and added `nonFilteredFileExtensions` (`jks`, `p12`, `pfx`,
  `crt`, `cer`, `der`) so binary/certificate resources are never filtered.
- `maven-surefire-plugin`: explicit `**/*Test.java` include (unit tests).
- `maven-failsafe-plugin`: explicit `**/*IT.java` include (integration
  tests), plus `integration-test`/`verify` executions so failsafe actually
  runs and fails the build on integration test failures once a consumer
  declares the plugin.

## 2.0.0-SNAPSHOT

- **Breaking**: bumped `spring-boot.version` from `3.4.1` to `4.0.0`.
  `testcontainers.version` and `otel.version` are bumped alongside it to
  `2.0.2` and `1.55.0` respectively, matching exactly what
  `spring-boot-dependencies:4.0.0` manages internally (verified against its
  published POM on Maven Central), to avoid version drift between the two
  imports.
- Testcontainers 2.0 renames module artifact IDs with a `testcontainers-`
  prefix (e.g. `junit-jupiter` -> `testcontainers-junit-jupiter`), relocates
  some container classes to new packages, and drops JUnit 4 support.
  `craft-parent` itself only manages versions (no `<dependencies>`), so this
  change is safe here, but any consuming microservice upgrading past this
  version must update its Testcontainers artifact IDs/imports accordingly.

## 1.2.0-SNAPSHOT

- Added `<build><pluginManagement>` pinning versions of the core build
  lifecycle plugins (compiler, surefire, failsafe, jar, clean, resources,
  install, deploy, source, javadoc, enforcer, dependency, jacoco, sonar).
  No plugin is bound/activated - consuming modules opt in individually.
- Deliberately excluded `spring-boot-maven-plugin` (this repo produces
  libraries, never a deployable artifact) and `maven-war-plugin` (no
  module here is packaged as a war).
- Added the corresponding plugin version properties.

## 1.1.0-SNAPSHOT

- Added `<dependencyManagement>` importing `spring-boot-dependencies`,
  `testcontainers-bom`, and `opentelemetry-bom` (import scope) for
  dependency version alignment, without inheriting from
  `spring-boot-starter-parent`.
- Added properties for the Java release, source/reporting encoding, the
  three imported BOM versions, and the versions of the quality tooling
  (Checkstyle, SpotBugs, PMD, JaCoCo) to be configured later.
