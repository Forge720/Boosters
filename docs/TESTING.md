# Testing and validation

## Automated tests

Run:

```bash
./gradlew clean test jar
```

The current suite covers exact stacking results, cap behavior, same-identity replacement, READY activation only once, global plus personal additive stacking, expiration, SQLite persistence, unavailable Currency.jar fallback, and permanent personal assignments. The current deterministic run is 7 tests, 0 failures.

## Clean Paper procedure

Run Paper 1.21.11 on Java 21 in offline mode, install `Boosters-0.1.0.jar`, accept the EULA in an isolated directory, and use a local test account. Exercise `/boost`, `/boost admin`, preset creation, personal READY assignment, activation, active replacement, global assignment, XP collection, expiration, and restart recovery. Install ViaVersion/ViaBackwards only if the client protocol does not match; they do not replace the Paper 1.21.11 server.

## Evidence status

Verified in this workspace:

- Gradle Java 21 compilation, automated tests, and the fat-JAR task completed successfully.
- The artifact passed ZIP integrity validation and contains the public Currency API adapter, reward adapter, plugin descriptor, and bundled SQLite driver.
- Paper 1.21.11 booted on Java 21 in offline mode with Boosters, Currency.jar, ViaVersion, ViaBackwards, PlaceholderAPI, and the disposable reward harness enabled. Boosters reported `stacking=HIGHEST, cap=1E+3` and Currency reported four ready currencies.
- A live Currency test initially found a real defect: `setPersonalPermanent` dereferenced a null duration. The validation loop was stopped, the manager guard was corrected, a regression test was added, and the 7-test suite passed afterward.
- The same live run verified that an unboosted Currency credit path worked and that the server enabled the native Currency integration without a class-loading error.

Not yet claimed as complete: a clean post-fix client protocol run proving the full 2x-to-3x replacement and idempotent credit sequence, Vault against a real provider, natural XP pickup, restart recovery after a live mutation, GUI mutation workflows, and measured performance percentiles. These require the corresponding runtime/client or provider and must be recorded only after execution.

## Performance methodology

Use a fixed Java 21 runtime, record the host/JVM, and benchmark `multiplier`/`evaluate` separately from mutation futures. Include 100 and 1,000 synthetic players, multiple global IDs, replacement bursts, and synchronized expiry. Report median and p95/p99 latency, allocations, heap, mutation latency, and invalidation cost only from executed measurements. The reward path must show no SQL, network, disk, YAML parsing, or Bukkit inventory work. No performance numbers are claimed in this release yet.

