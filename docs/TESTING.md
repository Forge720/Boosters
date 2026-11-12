# Testing and validation

## Automated tests

Use Java 21 and run:

```bash
HOME=/home/agent JAVA_HOME=<Java-21-JDK> ./gradlew clean test fatJar --offline
```

The wrapper is pinned to Gradle 9.9.0. SQLite JDBC 3.49.1.0 and the JUnit runtime are pinned under `libs/` for reproducible offline validation. The corrected suite contains 8 tests with 0 failures, including identity replacement, stacking/cap behavior, READY activation, expiration, SQLite restart persistence, Currency availability fallback, permanent assignments, and Vault precision safety.

## Clean Paper procedure

Run Paper 1.21.11 on Java 21 in an isolated offline directory. Install `Boosters-0.1.0.jar`, optionally install Currency.jar, accept the EULA, and wait for `Done (`. Use protocol clients or a compatible Minecraft client to exercise:

- `/boost`, `/boost list`, and `/boost admin`
- permission denial for non-admin players
- personal/global assignment, same-ID replacement, READY activation, expiration, and restart recovery
- Currency eligible reward credit and repeated operation idempotency
- live `PlayerExpChangeEvent` behavior
- invalid multiplier/duration inputs and clean shutdown

## Current evidence

Verified in the current validation loop:

- The Gradle wrapper build completed successfully offline on Java 21.
- The fat JAR passed ZIP integrity validation and contains the Currency adapters, plugin descriptor, and bundled SQLite driver.
- Paper 1.21.11 booted with Boosters and Currency.jar; Currency reported four ready currencies.
- Two offline protocol clients joined; permissions, GUIs, replacement, global/personal stacking, Currency credit, idempotency, expiration, and restart recovery passed.
- A live Paper event harness confirmed a 5 XP event became 10 under 2x and a zero event remained zero.
- Vault conversion safety passed deterministic tests. A real Vault provider was not installed in this loop.
- A Vault precision defect was found and fixed: exact IEEE-754 conversion is now rejected instead of being hidden by `Double.toString`.

Not yet claimed complete: natural XP-orb pickup/Mending, graphical pixel capture, GUI mutation workflows, real Vault provider, concurrent assignment fault injection, long-duration retention, and measured performance percentiles.

## Performance methodology

Benchmark `multiplier` and `evaluate` separately from mutation futures with 100 and 1,000 synthetic players, multiple global IDs, replacement bursts, and synchronized expiry. Record host/JVM, median, p95/p99 latency, allocations, heap, mutation latency, and invalidation cost only from executed measurements. No performance numbers are claimed in this release.
