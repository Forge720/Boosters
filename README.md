# Boosters.jar

Boosters.jar is a Paper 1.21.11 booster engine for personal and global money, XP, and Currency.jar reward multipliers. It owns booster state and calculations; reward producers remain responsible for the authoritative credit transaction.

## Features

- READY personal boosters with one-time activation and persistent ACTIVE timers.
- Timed/permanent global boosters.
- Exact same-identity replacement: player/global + canonical reward type + ID.
- Highest, additive, and multiplicative stacking with an effective cap.
- BigDecimal calculations for native integrations and a bounded reflective Vault adapter.
- Vanilla PlayerExpChangeEvent integration.
- SQLite WAL persistence, atomic upserts, asynchronous serialized mutations, and restart recovery.
- Player inventory GUI and compact administrator GUI with chat-validated preset editing.
- Bukkit service API in the separate boosters-api module.

## Requirements

- Java 21.
- Paper 1.21.11.
- Currency.jar is optional. Vault is optional and is never globally intercepted.

## Install

Copy release/Boosters-0.1.0.jar into the Paper plugins directory. Start Paper once, review plugins/Boosters/config.yml, and restart or run /boost reload. Install Currency.jar before Boosters when using exact Currency.jar reward integration.

## Build

```bash
./gradlew clean test fatJar
```

The deployable artifact is release/Boosters-0.1.0.jar. See docs/USAGE.md for the shortest administrator and AI-developer setup path.

## Commands

/boost opens the player menu. /boost list prints personal and global assignments.

```text
/boost admin
/boost give <player|uuid> <preset>
/boost set <player|uuid> <type> <multiplier> <duration|permanent> [id]
/boost global <type> <multiplier> <duration|permanent> [id]
/boost remove <player|global> <type> [id]
/boost reload
```

## Permissions

- boosters.use — player GUI and list; default true.
- boosters.admin — administrative commands and GUI; default op.

## Integration boundary

Currency.jar reward producers classify an eligible reward, call the Boosters API or CurrencyRewardAdapter exactly once, and commit the returned amount once through Currency.jar. Do not install a global Vault deposit interceptor. See docs/INTEGRATIONS.md.

## Validation

The release includes deterministic tests and recorded Paper 1.21.11 startup/integration evidence. See docs/TESTING.md for executed checks and unverified runtime boundaries.
