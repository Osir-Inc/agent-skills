---
name: order-a-vps
description: Order and manage a VPS at OSIR, including picking a package and location, adding an SSH key, and getting into the machine afterwards. Use when a user needs a server rather than shared hosting, asks about specs or regions, or cannot SSH into a VPS they already have.
---

# Order a VPS

A VPS is a recurring charge, so the confirmation gate applies. Dedicated hardware is a separate catalog: `getDedicatedServerCatalog`.

## Before ordering

1. `listVpsPackages` and `getVpsPackageDetails` for specs and price.
2. `listVpsLocations`. Pick for latency to the users and for data residency, which can be a legal requirement rather than a preference. Ask if it is not obvious.
3. `listVpsOsTemplates` for the OS.
4. **`addSshKey` first.** Adding the key before ordering is the difference between a usable machine and a support ticket. See the access section below.

## Ordering

`orderVps` stages an `actionId`: show the itemized summary including the recurring amount and the term, get explicit approval, then `executeConfirmedAction`. `buildVpsInstance` provisions it. Provisioning is not instant; poll `getVpsInstanceDetails` rather than assuming.

`listMyVpsInstances`, `countMyVpsInstances`, `changeVpsPaymentTerm` and `deleteVpsInstance` manage it afterwards. Deleting destroys the data.

## Getting in, and the trap that generates most tickets

**A VPS ships with your SSH key and no root password.** Two consequences:

- If no key was added before provisioning, there is no way in over SSH. Use `loginToVpsPanel` for the console.
- **Resetting the root password in the panel does not let you in over SSH.** The server still has `PasswordAuthentication no`, so SSH keeps answering `Permission denied` while the panel console works fine. This is correct behaviour and it looks exactly like a broken server.

The fix is either to use the panel console, or to log in there once and change `PasswordAuthentication` yourself. Key-based access is the better answer; suggest `addSshKey` and a rebuild if the machine is still empty.

`listMySshKeys` and `deleteSshKey` manage keys.

## Practical advice

- Smallest package that fits, and resize later. Users routinely over-buy.
- A VPS is unmanaged: patching, firewall and backups are the user's job. Say so once, plainly.
- To put an app on it, `osirAppDeployToVps` ships it over the platform's own deploy key and needs no SSH from you. See [deploy-a-web-app](/.well-known/agent-skills/deploy-a-web-app/SKILL.md).
- To point a domain at it, add an `A` record. See [manage-dns-records](/.well-known/agent-skills/manage-dns-records/SKILL.md).
