# FleetCheck – Build Systems Lab

This project is intentionally incomplete. Follow the worksheet in the order given.

Expected final application output:

```
FleetCheck 1.0
Vehicles loaded: 4
Vehicles requiring service: 2
Average mileage: 37000 km
```

Do not copy the solution POM. The objective is to observe how each build change alters the result.

# Outputs:

## Evidence 8.1

Command: `gradle clean build`

```
/Users/diogo/Documents/Docs/Uni/5thSem/SoftwareQuality/worksheet4/gradle/FleetCheck_Starter/src/main/java/pt/upt/fleetcheck/App.java:4: error: package com.fasterxml.jackson.databind does not exist
```

Missing dependency: `com.fasterxml.jackson.core:jackson-databind`. `App.java` imports `com.fasterxml.jackson.databind.ObjectMapper` (line 4) and `com.fasterxml.jackson.core.type.TypeReference` (line 3), but Jackson is not declared in `build.gradle`.

## Evidence 8.2

Direct dependency: `jackson-databind:2.22.2` (declared with `implementation`). Transitive dependencies: `jackson-annotations:2.22` and `jackson-core:2.22.2`. Changing the build system did not change the application's dependencies: `gradle dependencies --configuration runtimeClasspath` shows the same three Jackson libraries, with the same versions, as `mvn dependency:tree`. (Gradle also lists `jackson-bom`, which only constrains the versions of these libraries.)

## Evidence 8.3

Before, the default JAR contained only the project's classes and had no `Main-Class`, so `java -jar` failed with `no main manifest attribute`. After the change, the manifest declares `Main-Class: pt.upt.fleetcheck.App` and the JAR also contains the contents of the runtime dependencies (Jackson), so it is self-contained and runs with `java -jar`.

## 8.4 question: which hidden environmental assumption did the Gradle Wrapper remove?

The assumption that the right version of Gradle is installed on the machine. `gradlew` uses the version pinned in `gradle/wrapper/gradle-wrapper.properties` (Gradle 9.8.0).

## Evidence 8.5

https://github.com/diogof146/QS-Worksheet4-Gradle/actions/runs/37235292767

## Evidence 8.6

The Gradle SBOM is generated from the resolved dependency graph, so it also contains the transitive dependencies of `jackson-databind` (`jackson-core` and `jackson-annotations`), which are not written in `build.gradle`.

## Final question: what changed, the software or the build process?

The build process. The source code, resources and output of FleetCheck are the same; only the tool that builds, tests and packages it changed.
