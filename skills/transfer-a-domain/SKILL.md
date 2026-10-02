---
name: transfer-a-domain
description: Transfer a domain into OSIR from another registrar, or check and approve a transfer already in flight. Use when a user wants to move a domain to OSIR, asks why a transfer is stuck, has received a transfer approval email, or wants to know whether a domain is eligible to move yet.
---

# Transfer a domain in

A transfer adds a year to the registration and costs the transfer price for that extension. It does not lose the time already paid for.

## Eligibility, check before promising anything

A domain cannot transfer if:

- It was **registered or previously transferred less than 60 days ago.** ICANN rule, no exceptions, no appeal.
- It is **locked.** The user unlocks at their current registrar; `unlockDomain` only applies to domains already at OSIR.
- WHOIS privacy hides the admin contact at some registrars, which blocks the approval email.
- It is within a few days of expiry. Renew first, then transfer.

## The flow

1. `getTransferQuote` for the price, and tell the user it adds a year.
2. The user gets the **auth code** (also called EPP code or transfer secret) from their current registrar and unlocks the domain there.
3. `initiateTransfer` with the domain and auth code. Like any paid action this stages an `actionId`: show the summary, get approval, then `executeConfirmedAction`.
4. The registry emails the registrant. **The user must click approve.** Nothing you do replaces that.
5. Poll `getTransferStatus`. `listPendingTransfers` shows everything in flight.

Without explicit approval a transfer completes on its own after five days at most registries, but tell the user to approve rather than wait.

## When it is stuck

In order of likelihood: the auth code is wrong or expired, the domain is still locked, the approval email went to a privacy-protected or stale address, or the 60-day rule applies. `getTransferStatus` usually names the reason. `cancelTransfer` stops one in flight.

## After it lands

The zone does **not** come with the domain. Recreate records before the nameservers change, or the site goes dark mid-switch: read the old zone at the old provider, create the records at OSIR with [manage-dns-records](/.well-known/agent-skills/manage-dns-records/SKILL.md), then `updateNameservers`.

Set `updateDomainAutoRenew` and check `updateDomainPrivacy`, since neither carries over.

## Email actions

Approval and rejection links in OSIR transfer emails are public and token-based, and the page asks for a click before acting. Never fetch one on the user's behalf to "check" it: these are single-use, and a scanner or preview fetch consumes the token.
