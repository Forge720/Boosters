# API

Add the `boosters-api` artifact as a compile-only dependency and obtain the service at runtime:

```java
RegisteredServiceProvider<BoostersAPI> registration =
    Bukkit.getServicesManager().getRegistration(BoostersAPI.class);
if (registration == null) return;
BoostersAPI boosters = registration.getProvider();
```

Reward calculations are synchronous, read-only, memory-resident, and safe for the reward hot path. Mutations return `CompletionStage` and complete only after SQLite commit and snapshot publication. Completion callbacks must schedule Bukkit operations back onto the server thread.

```java
BigDecimal adjusted = boosters.apply(player.getUniqueId(), "currency:tokens", new BigDecimal("1000"));
boosters.setPersonal(player.getUniqueId(), "event", "money", new BigDecimal("2.5"), Duration.ofMinutes(30));
boosters.setPersonalPermanent(player.getUniqueId(), "rank", "money", new BigDecimal("2"));
boosters.activateReady(player.getUniqueId(), "money", "event");
```

Use `RewardContext` and `evaluate` when classifying a reward. The context preserves the original amount and a `boosters` modifier marker prevents the same context from being adjusted twice. This is not transaction idempotency; the producer still owns source transaction IDs and exactly-once credit behavior.

Custom reward IDs can be registered through `registerRewardType`. Currency namespace entries are resolved dynamically and `Currency.jar` primary-currency aliases normalize to `money`.

