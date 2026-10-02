---
name: manage-dns-records
description: Create, read, update and delete DNS records for a domain at OSIR, and point a domain at a host, a site or a mail provider. Use when a user wants a domain to resolve somewhere, needs an A, AAAA, CNAME, MX or TXT record, is verifying a service that asked for a DNS record, or reports that a change has not taken effect.
---

# Manage DNS records

Requires a signed-in session. Free anycast DNS is included with every domain.

## Tools

| Tool | Does |
|---|---|
| `listDnsRecords` | Read the zone. Always start here. |
| `getDnsRecord` | One record by id. |
| `createDnsRecord` | Add a record. |
| `updateDnsRecord` | Change one. Needs the record id from the list. |
| `deleteDnsRecord` | Remove one. |
| `initializeDnsZone` | Create the zone. Rarely needed, see below. |

A domain registered at OSIR gets its zone created automatically. Reach for `initializeDnsZone` only when `listDnsRecords` shows no zone at all, which normally means the domain was transferred in rather than registered.

## Work read first, write second

1. `listDnsRecords` and show the user what is already there.
2. Decide whether you are adding or replacing. **If a record of the same type and name exists, update it.** Creating a second A record for the same host splits traffic between two addresses and is almost never what anyone wants.
3. Make the change.
4. Re-read the zone and confirm.

## The records people actually ask for

| Goal | Record |
|---|---|
| Point a domain at a server | `A` to an IPv4, `AAAA` to an IPv6 |
| Point `www` at the apex | `CNAME` `www` to the domain |
| Email | `MX`, plus SPF, DKIM and DMARC as `TXT`. See [set-up-email](/.well-known/agent-skills/set-up-email/SKILL.md) |
| Verify ownership for a third party | `TXT` with the exact string they gave you |

**A CNAME cannot coexist with any other record on the same name, and the apex cannot be a CNAME.** If a user wants the bare domain pointed at something that only gives a hostname, use the provider's IP with an `A`, or web forwarding.

## Nameservers are a different thing

Records only take effect if the domain's nameservers point at OSIR. `updateNameservers` changes delegation; `getDomainInfo` shows the current set. If a user swears a record is correct and nothing resolves, check delegation before debugging the record. For registering glue records, see `createHost` and `getHostsForDomain`.

## Propagation

Changes publish quickly but resolvers cache for the record's TTL. Before a planned change, lower the TTL, wait for the old one to expire, then change. Tell the user to verify with `dig example.com A` rather than their browser, which caches separately.
