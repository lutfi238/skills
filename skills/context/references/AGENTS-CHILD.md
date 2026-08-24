# Nested `AGENTS.md` — local docs

A child doc governs its directory and everything below it, until a deeper child doc takes over.

## When a directory earns one

All three must hold:

1. **It is a lasting boundary.** A module, a service, a layer — something that will still be a distinct thing in six months. Not a folder that happens to hold four files.
2. **It has rules of its own.** Something is true here that is not true elsewhere in the repo. If its only rules are the project's rules, the rail already covers it.
3. **An agent could get it wrong.** There is a real trap, a non-obvious contract, or a convention that looks arbitrary from outside.

Fails any one of them → no doc. Put the fact in the parent instead.

The failure mode to avoid is doc spam: a doc in every directory, each restating the rail in slightly different words. That trains agents to skim, which defeats the whole system. Three well-placed child docs beat fifteen hollow ones.

Some signals a directory has earned one: it has its own dependency set; it has its own test command; it is the only place a particular external service is called; it enforces an invariant the rest of the repo relies on; you have watched an agent break it twice.

## Shape

```md
# <Area name>

<One or two sentences: what this area is responsible for.>

## Owns

<Which paths this doc governs, and where its responsibility stops.>

## Contracts

<What this area exposes, what it expects from callers, what invariants must hold.
The things that break other code when changed.>

## Local rules

<Rules that apply here and are not already in the root rail.>

## Verify

<How to check a change here is correct. The actual command.>

## Index

<Child docs below this one. Omit the section when there are none.>
```

Leave a section empty rather than filling it. An empty `Verify` is honest information — it says there is no automated check for this area.

## Example

```md
# Payments

Charges customers and reconciles Stripe webhook events against local order state.

## Owns

`src/payments/**`. Stripe API calls, webhook handlers, the `payment_intents` and
`refunds` tables. Does **not** own invoice rendering — that is `src/billing/`.

## Contracts

- `createCharge()` is idempotent on `orderId`. Callers may retry safely; do not add a
  code path that charges twice for one order.
- Every state change emits `PaymentStateChanged`. `src/notifications/` consumes it. Add a
  state without emitting and customers stop receiving receipts.
- Amounts are integer cents throughout. A float reaching this module is a bug upstream.

## Local rules

1. Never call the Stripe SDK outside `client.ts` — it is the only place retry and
   idempotency-key handling live.
2. Webhook handlers must tolerate replay. Stripe redelivers, sometimes days later.
3. New payment states need a migration **and** an entry in `STATE_TRANSITIONS`. The
   transition table is enforced at runtime; a missing entry throws in production.

## Verify

`pnpm test src/payments` — includes the Stripe mock suite. Also run
`pnpm test:e2e payments` before changing anything under `webhooks/`.
```

Note what makes this useful: every statement names a consequence. "Do not add a code path that charges twice" is actionable. "Be careful with payments" is not.

## Rules

- **Never restate the rail.** If it is true project-wide, delete it from here.
- **If three siblings say the same thing, it belongs in the parent.** Move it up and delete all three copies.
- **Name consequences, not sentiments.** What breaks, and where.
- **Index your children.** A child doc two levels down is unreachable if nothing between it and the rail points to it.
- **Delete the doc when the directory dies.** An orphan doc describing code that no longer exists is actively misleading.
