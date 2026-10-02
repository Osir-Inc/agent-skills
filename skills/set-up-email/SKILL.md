---
name: set-up-email
description: Set up mailbox hosting on a user's own domain at OSIR, with JMAP, IMAP, POP3 and SMTP. Use when a user wants email at their domain, asks for a professional address instead of a free provider, needs the anti-spam DNS records, or reports that their mail is landing in spam.
---

# Set up email on a domain

Enabling a domain for email is **free**. Mailboxes are the paid product, billed per mailbox monthly or annually. Never quote a mailbox price from memory: call `listMailPlans` or `getMailboxQuote`, or point at https://osir.com/en/email/.

## The flow

1. `listMailPlans` to see packages and current prices.
2. `enableMailDomain` for the domain. Free.
3. **DNS.** If the domain uses OSIR nameservers, the records publish automatically. If it uses external DNS, call `getMailDnsRecords`, have the user add them, then `verifyMailDns` to confirm.
4. `createMailbox`. Paid, so it stages an `actionId`: show the summary, get approval, `executeConfirmedAction`.
5. **The password is shown exactly once.** Capture it and hand it to the user immediately. It cannot be retrieved later, only reset with `setMailboxPassword`.

`listMailboxes`, `getMailboxUsage` and `deleteMailbox` manage them afterwards.

## Deliverability is the whole job

A mailbox nobody receives is worthless. Four records matter, and OSIR publishes them for you on OSIR nameservers:

| Record | Does |
|---|---|
| `MX` | Routes mail to the servers |
| `SPF` | Says which servers may send as the domain |
| `DKIM` | Signs outbound mail so it cannot be forged |
| `DMARC` | Tells receivers what to do when SPF or DKIM fails |

MTA-STS and autoconfig are published automatically too, so most clients set themselves up from the address alone.

**If a user says their mail goes to spam, check these four before anything else.** The usual cause is a second SPF record: a domain may have exactly one, and adding a new one beside an old one breaks both. Merge them.

## Connecting a client

JMAP, IMAP, POP3 and SMTP are all supported. JMAP is the modern one and worth preferring where the client supports it. There is webmail; do not invent a URL for it, point users at the panel.

## Gotchas

- Enabling a domain is free but **creating a mailbox charges**, so the confirmation gate applies to step 4 and not step 2.
- A mailbox has a grace period after deletion, but treat deletion as permanent when talking to the user.
- Taking over a domain that already has mail elsewhere returns a 409 rather than silently stealing the routing. That is a safety feature; resolve the existing setup first.
