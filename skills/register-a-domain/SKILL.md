---
name: register-a-domain
description: Register a domain name at OSIR on behalf of a user, end to end. Use when a user has chosen a name and wants to buy it, or asks you to acquire a domain for them. Covers the confirmation gate, funding a short balance including agent-initiated payment, what happens automatically after registration, and the mistakes that cost real money.
---

# Register a domain

Registration spends the user's money. Read [connect-to-osir](/.well-known/agent-skills/connect-to-osir/SKILL.md) first if you are not authenticated.

## The flow

1. **Re-check availability and price.** Someone may have taken the name while you were talking. See [check-domain-availability](/.well-known/agent-skills/check-domain-availability/SKILL.md). Confirm `premium` is false, or that the user knows the premium price and that it recurs every year.
2. **Make sure you are signed in.** `getAuthStatus`. If not, `loginWithDevice`, then poll `checkDeviceLoginStatus`, then pass the `sessionKey` on every later call.
3. **Call `registerDomain`.** It does **not** register anything. It returns an `actionId` and an itemized summary.
4. **Show the user that summary, with the total, and wait.** Quote the all-in figure including the $0.20/yr ICANN fee on gTLDs and processing (3% or $0.30 minimum).
5. **On explicit approval, call `executeConfirmedAction`** with the `actionId`.

Never skip step 4, never guess at step 5, and never re-use an `actionId` after a failure without staging a fresh one.

## If the balance is short

OSIR accounts run on a prepaid balance. An insufficient-funds error is not a dead end: it returns the amounts you need and a `networkId`.

- **If the user has granted a payment method** through their assistant platform or wallet, mint a single-use Stripe Shared Payment Token for the **gross** amount, then call `fundBalanceWithSharedPaymentToken` with the **net** as `creditCents`. That also returns an `actionId`, so the same confirm-then-execute rule applies. Tokens are single-use.
- **Otherwise**, call `createPaymentSession` and give the user the checkout URL. This is still the normal path for most people.

Check `getAccountBalance` before a batch of registrations so you top up once rather than failing repeatedly.

## What happens automatically

- The DNS zone is created. You do not need `initializeDnsZone` for a fresh registration.
- WHOIS privacy and DNSSEC are included at no cost.
- Terms run 1 to 10 years. Longer terms protect against price rises.

Afterwards you can set records ([manage-dns-records](/.well-known/agent-skills/manage-dns-records/SKILL.md)), add mailboxes ([set-up-email](/.well-known/agent-skills/set-up-email/SKILL.md)), or call `getHostingBundle` for exact hosting prices on the name just registered.

## Mistakes that cost money

- **Registering a premium without flagging it.** A premium price recurs annually. Say so before, not after.
- **Typos.** `validateDomainName` first. A registration cannot be undone and is not refundable.
- **Trademarks.** A fan site on a club or brand name can be taken off the user. Flag the risk; do not give legal advice.
- **Letting it lapse.** Set `updateDomainAutoRenew` on, or tell the user the expiry date. Recovery after expiry costs far more than renewal.
- **Assuming registration is instant everywhere.** Most TLDs are immediate; some registries review first.
