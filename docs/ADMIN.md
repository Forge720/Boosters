# Administration

## Presets

Open `/boost admin` and select Create preset. Enter one line in chat:

```text
money2x money 2x 30m
```

The four fields are ID, reward type, multiplier, and duration. Durations use `s`, `m`, `h`, `d`, or `permanent`. Editing uses the same form. Delete asks only for the preset ID. Input expires after 60 seconds; `cancel` aborts it.

## Assignment

`/boost give` creates a READY personal record. It uses the preset ID as the booster ID. `/boost set` creates an ACTIVE personal record, and `/boost global` creates an ACTIVE global record. Omit the ID to use `default`.

Every assignment is an upsert. The same player/global scope, canonical reward type, and ID always leave exactly one record. Replacement resets multiplier, duration, and activation state; it never extends the old timer or adds the old multiplier.

## Stacking

Configure `settings.stacking` as `highest`, `additive`, or `multiplicative`. Distinct IDs can stack. A personal and a global booster are distinct identities. `settings.max-multiplier` caps only the final effective value.

## Removal and inspection

Use `/boost list`, `/boost list global`, or `/boost list <player|uuid>`. Use `/boost remove <player|global> <type> [id]`. GUI item stacks are representations only and cannot be extracted or used to manufacture boosters.

