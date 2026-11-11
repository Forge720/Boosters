# Integrations

## Currency.jar

Currency.jar is the preferred monetary integration. Boosters.jar now consumes Currency.jar's public `currency-api-0.1.0` service when Currency is enabled. Currency.jar still owns currency registration, balances, precision, limits, and the final transaction; Boosters.jar owns assignments and returns an adjustment only.

Boosters discovers `net.plexmc.currency.api.CurrencyService` through Bukkit's service manager and refreshes it periodically, so enabled dynamic currency IDs appear in tab completion and canonical `currency:<id>` resolution. A lightweight `CurrencyBridge` remains available for compatible alternate providers. Currency.jar does not expose a Vault provider in v0.1; `money` is the canonical primary Currency.jar identity.

Recommended producer flow:

```java
RewardContext context = new RewardContext(player, "currency:tokens", original,
    "Mines.jar", "MINING", transactionId, true, Set.of());
CurrencyRewardAdapter.creditEligible(currency, boosters, context, transactionId,
    "Mines.jar", "MINING");
```

Do not call `credit` twice. Do not classify transfers, refunds, admin deposits, conversions, or corrections as eligible.

## Vault

Vault has no universal earnings event. Never hook all deposits. An eligible producer calculates before calling its normal `Economy.depositPlayer` operation. `VaultRewardAdapter` rejects negative, non-finite, or non-representable `double` values and checks the provider’s transaction response. Vault cannot provide exact arbitrary-precision semantics; use Currency.jar for large-number economies.

## XP

Boosters listens to `PlayerExpChangeEvent`, modifies the event amount once using HALF_UP integer rounding, and never calls `giveExp` from the listener. This covers normal event-driven XP paths such as experience-orb pickup when the server emits the event. Direct XP paths outside this event must call `BoostersAPI` explicitly. Command level grants and arbitrary command output are not automatically intercepted.

