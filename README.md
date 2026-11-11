# Boosters.jar

Boosters.jar is a Paper 1.21.11 booster engine for personal and global money, XP, and Currency.jar reward multipliers. It owns booster state and calculations; reward producers remain responsible for the authoritative credit transaction.

## Features

- READY personal boosters with one-time activation and persistent ACTIVE timers.
- Timed/permanent global boosters.
- Exact same-identity replacement: player/global + canonical reward type + ID.
- Highest, additive, and multiplicative stacking with an effective cap.
- BigDecimal calculations for native integrations and a bounded reflective Vault adapter.
- Vanilla `PlayerExpChangeEvent` integration.
- SQLite WAL persistence, atomic upserts, asynchronous serialized mutations, and restart recovery.
- Player inventory GUI and compact administrator GUI with chat-validated preset editing.
- Bukkit service API in the separate `boosters-api` module.

## Requirements

- Java 21.
- Paper 1.21.11.
- Currency.jar is optional. Vault is optional and is never globally intercepted.

## Build

```bash
./gradlew clean test fatJar
```

The deployable artifact in this repository is `Boosters-0.1.0.jar`; the reproducible build output is `boosters-plugin/build/libs/Boosters-0.1.0.jar`.

For the shortest administrator and AI-developer setup path, see [docs/USAGE.md](docs/USAGE.md).

## Commands

`/boost` opens the player menu. `/boost list` prints personal and global assignments.

Administrators can use:

```text
/boost admin
/boost give <player|uuid> <preset>
/boost set <player|uuid> <type> <multiplier> <duration|permanent> [id]
/boost global <type> <multiplier> <duration|permanent> [id]
/boost remove <player|global> <type> [id]
/boost reload
```

## Permissions

- `boosters.use` — player GUI and list; default true.
- `boosters.admin` — administrative commands and GUI; default op.

## Integration boundary

Currency.jar should obtain `BoostersAPI` through Bukkit services, classify an eligible reward, call `evaluate`, and commit the returned amount exactly once in Currency.jar. Do not install a global Vault deposit interceptor. See [INTEGRATIONS.md](docs/INTEGRATIONS.md).

## Known limitations

The concrete Currency.jar public API is compile-only and optional at runtime; Boosters discovers it only when Currency.jar is enabled and safe-loads without it. The XP event API does not carry a universal “natural-only” flag; producers that award custom XP should use the API explicitly and avoid also firing an event that Boosters will process.

