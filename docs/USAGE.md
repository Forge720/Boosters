# Boosters.jar — Release Usage Guide

## Install

1. Run Paper 1.21.11 with Java 21.
2. Copy `Boosters-0.1.0.jar` from this repository into `plugins/`.
3. Optional: install Currency.jar for exact multi-currency rewards. Vault is optional and must be used through an explicit eligible-reward producer.
4. Start Paper once to generate `plugins/Boosters/config.yml` and initialize SQLite.
5. Review the generated configuration, then restart or run `/boost reload`.

Boosters never intercepts arbitrary deposits. The reward-producing plugin classifies an earned reward, applies the modifier once, and performs the authoritative credit through Currency.jar or Vault.

## Administration

Permission: `boosters.admin` (operators receive access).

```text
/boost admin
/boost give <player> <preset>
/boost set <player> <type> <multiplier> <duration> [id]
/boost global <type> <multiplier> <duration> [id]
/boost remove <player|global> <type> [id]
/boost list [player|global]
/boost reload
```

Examples:

```text
/boost give Steve money2x
/boost set Steve currency:tokens 2.5x 45m event
/boost global money 2x 30m event
/boost remove Steve money event
```

Use `permanent` for a non-expiring active assignment. Omitted IDs use `default`. The identity key is audience + canonical reward type + ID; assigning the same identity replaces the old record and resets its multiplier, duration, and lifecycle state.

## Players

Permission: `boosters.use`.

```text
/boost
/boost list
```

`/boost` opens the personal booster menu. READY boosters can be activated once; ACTIVE boosters count down using absolute timestamps and remain active across disconnects and restarts.

## Configuration

The generated `config.yml` controls stacking (`highest`, `additive`, or `multiplicative`), the individual/effective multiplier cap, bossbar display, and validated presets. Currency IDs are discovered from Currency.jar at runtime; do not maintain a duplicate currency list in Boosters configuration.

## AI/developer integration

Resolve `BoostersAPI` through Bukkit’s `ServicesManager`. Synchronous multiplier/evaluation methods are memory-resident and read-only. Assignment methods return `CompletionStage` and complete only after durable persistence. Do not perform Bukkit entity or inventory work from completion threads.

For Currency.jar, use `CurrencyRewardAdapter.creditEligible(...)` with an immutable `RewardContext`, a unique transaction ID, and `eligible=true`. Currency.jar remains responsible for parsing, precision, idempotency, and the final credit. Mark the `boosters` modifier in a context before reprocessing it.

For Vault, calculate the eligible reward before the single `Economy.depositPlayer` call. Do not install a deposit interceptor. For vanilla XP, Boosters edits positive `PlayerExpChangeEvent` amounts and never calls `giveExp` from the listener.

Build:

```bash
./gradlew clean test fatJar
```

The deployable artifact is `boosters-plugin/build/libs/Boosters-0.1.0.jar`.

## Verification and troubleshooting

- Confirm `Boosters 0.1 enabled` in the Paper log.
- Confirm Currency integration status in `/boost admin` when Currency.jar is installed.
- If Currency.jar is absent, XP and stored booster state remain available while currency evaluation is unavailable.
- If a mutation fails, treat the future/command result as failed; no in-memory assignment is acknowledged before SQLite commit.
- See `docs/TESTING.md` for executed evidence and explicitly unverified runtime gates.
