# Boosters 0.1.0 — Live Validation Report

Validation date: 2026-11-11 UTC  
Repository: `Forge720/Boosters` (public `main`)  
Artifact tested: `Boosters-0.1.0.jar`  
Artifact SHA-256: `44846e6b1bfce1d97ee6240b29c161317f89d26e9502d020be6846a30dee2d5e`

## Verdict

**One confirmed Vault precision defect was found and fixed.** The corrected release passed clean Paper startup, Currency integration discovery, protocol-level player interaction, permissions, GUI opening, assignment replacement, timed expiration behavior, global and personal assignments, strict input validation, reward calculation, idempotency protection, restart persistence, Vault conversion safety tests, and live Bukkit XP event validation.

**Production gate: CONDITIONAL, not fully certified.** The Gradle wrapper and offline dependency set now build successfully. Natural XP-orb pickup, graphical pixel capture, long-duration/load measurements, and a real Vault provider were not newly executed in this loop.

## Environment

- Paper 1.21.11, offline isolated server
- Java 21.0.13
- Exact published Boosters JAR
- Currency.jar 0.1.0, discovered as a live runtime service with four currencies
- Disposable RewardHarness producer using the published Boosters API and Currency adapter
- Two independent offline Mineflayer protocol clients: `BoostersTester` (operator) and `GuestTester` (non-operator)
- Clean generated server/world for the main run
- Paper reached `Done (49.906s)`

## Executed results

| Area | Result | Evidence |
|---|---|---|
| Two-player join | PASS | Both clients joined Paper; server log records both connections. |
| Player `/boost` | PASS | Inventory window opened; top menu is 27 slots (63 including player inventory). |
| Player `/boost list` | PASS | Command returned without exception. |
| Permission boundary | PASS | Non-operator received `No permission.` for `/boost admin`. |
| Personal assignment | PASS | `/boost set ... permanent` returned `Assigned`. |
| Same-ID replacement | PASS | Reassignment returned `Replaced existing assignment`; list contained one 3× record. |
| Timed assignment | PASS | Timed assignment accepted; after expiration it was absent while unrelated permanent records remained. |
| Global assignment/removal | PASS | Global assignment returned `Assigned`; removal returned `Removed.` |
| Invalid multiplier | PASS | `Invalid multiplier: nope`. |
| Invalid duration | PASS | `Duration must use s, m, h, d, or permanent`. |
| Currency startup | PASS | Currency reported `ready with 4 currencies`; Boosters enabled afterward. |
| Eligible reward, personal + global | PASS | 100 seed balance + 10 reward with personal 2× and global 3× produced 130 under HIGHEST. |
| Double-application protection | PASS | Repeated operation returned success without changing balance: remained 130. |
| Restart recovery | PASS | After restart, all three active records and balance 130 were restored. |
| Bukkit XP event | PASS | Live Paper harness fired `PlayerExpChangeEvent(5)` and observed 10 with a 2× booster; zero remained zero. |
| Vault precision safety | PASS | Unit test verifies exact representable deposit and rejects unsafe large double conversion. |
| Clean shutdown | PASS | Server was stopped after each live run; no Boosters exception was observed. |

## Defect log

### Fixed during this loop

**High — Vault double precision guard.** `VaultRewardAdapter` used `Double.toString(amount)` for its round-trip check, which could conceal IEEE-754 approximation for large values. It now compares against `new BigDecimal(double)` and rejects values that cannot be represented exactly. Regression coverage passes.

The first economy attempt was discarded as invalid evidence because it reused an existing SQLite/world state and operation ID. The rerun used a clean server and a new operation ID and passed.

## Not verified in this loop

- Natural XP orb pickup and Mending interaction. The Mineflayer orb attempt did not trigger pickup in the offline client; this is retained as an environment/client coverage gap. The actual Bukkit event path passed live.
- A real Vault provider and Vault-specific live deposit. Adapter precision and success behavior pass in deterministic tests.
- GUI mutation clicks, preset creation/editing, pagination, and rapid-click activation.
- Graphical Prismarine/Playwright capture; protocol and inventory state were verified instead.
- Concurrent assignment race testing, database fault injection, long-duration retention, and load/latency percentiles.
- Optional Currency primary-Vault alias path with a live Vault provider.

These are **coverage gaps**, not failures. They prevent an unconditional production-ready claim.

## Evidence files

- `live-validation-7/server.log` — clean Paper/Currency/Boosters startup and command trace
- `live-validation-7/tools_live_boosters.mjs` — protocol test harness
- `live-validation-1/restart_check.mjs` — restart recovery probe
- `live-validation-11/server.log` — live Paper XP event validation
- `boosters-release/boosters-plugin/src/test/java/com/plexmc/boosters/integration/IntegrationSafetyTest.java` — Vault safety regression test
- `Boosters-0.1.0.jar` — exact deployed artifact

## Reproduction outline

1. Run Paper 1.21.11 on Java 21 with `online-mode=false` in an isolated test directory.
2. Install `Boosters-0.1.0.jar`, Currency.jar 0.1.0, and the disposable RewardHarness.
3. Start Paper and wait for `Done (`.
4. Connect two offline protocol clients.
5. Run the assignment, replacement, validation, Currency reward, idempotency, GUI, restart, and XP event checks documented above.

## Build evidence

`HOME=/home/agent JAVA_HOME=<Java-21-JDK> ./gradlew clean test fatJar --offline` passed with 8 tests and 0 failures. The wrapper is pinned to Gradle 9.9.0; SQLite JDBC 3.49.1.0 and the JUnit test runtime are pinned under `libs/` for offline reproducibility.

