# Changelog

All notable changes to `craft-parent` are documented here.

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
