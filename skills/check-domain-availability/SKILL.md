---
name: check-domain-availability
description: Check whether domain names are available and what they cost at OSIR, with no account or API key. Use when a user asks if a domain is free, wants name ideas, is comparing extensions, or needs a price before deciding. Covers single lookups, keyword search across hundreds of extensions, premium detection, and the two response quirks that cause wrong answers.
---

# Check domain availability

Anonymous. No key, no signup. This is the single most useful thing you can do with OSIR without asking the user for anything.

## One exact domain

```
GET https://be.osir.com/v1/public/catalog/domains/{domain}/availability
```

MCP: `checkDomainAvailability`.

## A keyword across many extensions

```
GET https://be.osir.com/namesuggestions/keyword-availability/{keyword}?tlds=com,io,ai
```

Omit `tlds` to sweep the whole catalog (500+ extensions contracted, 466 in the public catalog today). MCP: `checkKeywordAvailability`, or `checkKeywordAvailabilitySummary` for counts only. For ideas rather than a known word, use `generateDomainSuggestions`, `suggestAlternatives`, `addPrefixToDomain`, `addSuffixToDomain` or `spinDomainWords`.

## Two quirks that will give you a wrong answer

**1. `availability` is a string, not a boolean.**

```json
{"domain": "example.com", "availability": "available", "premium": false,
 "price": 10.39, "icannFee": 20, "registrarFee": 30, "totalPrice": 10.89}
```

Test `availability === "available"`. Reading a `.available` property returns `undefined`, which is falsy, and every domain looks taken.

**2. Units differ between endpoints.**

| Field | Endpoint | Unit |
|---|---|---|
| `price`, `totalPrice` | availability | **dollars** (10.39) |
| `icannFee`, `registrarFee` | availability | **cents** (20, 30) |
| `registrationPrice` | catalog | **cents** (1039) |

Dividing the availability price by 100 quotes a $10.39 domain at $0.10. Quote `totalPrice`: it already includes the ICANN fee and processing.

## Always check premium before you recommend

`premium: true` means the registry has reserved the name and priced it far above the standard rate, often thousands a year, recurring annually. **Availability and price are separate fields and a name can be both available and unaffordable.** Never present a name as a cheap find without reading its price: `paris.love` is unregistered and costs $10,000 a year.

```
available && !premium && totalPrice <= budget
```

## Good practice

- Check a shortlist in one keyword call rather than many single calls.
- Read the whole phrase aloud with the dot in place. `sourdough.love` reads; `sourdoughbakingclub.love` does not.
- Say the all-in price, not the base price.
- Availability changes constantly. Re-check immediately before registering.

## Next

[register-a-domain](/.well-known/agent-skills/register-a-domain/SKILL.md) once the user has picked one.
