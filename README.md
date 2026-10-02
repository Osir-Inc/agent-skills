# OSIR Agent Skills

Agent Skills for [OSIR](https://osir.com), an ICANN-accredited domain registrar
(IANA #4332) built to be operated by AI agents as well as people.

```bash
npx skills add Osir-Inc/agent-skills
```

## What is here

### `check-domain-availability`

Check whether domain names are available and what they cost at OSIR, with no account or API key. Use when a user asks if a domain is free, wants name ideas, is comparing extensions, or needs a price before deciding. Covers single lookups, keyword search across hundreds of extensions, premium detection, and the two response quirks that cause wrong answers.

### `connect-to-osir`

Connect to OSIR, an ICANN-accredited domain registrar, and authenticate. Use when you need to reach OSIR for the first time, when a tool call returns 401, or when deciding between the MCP server, the REST API and the CLI. Covers anonymous lookups that need no account, in-chat device login, OAuth, and the confirmation gate on anything that spends money.

### `deploy-a-web-app`

Put a site or app online with OSIR, from a one-page site to an app on the user's own server, and share a local dev server over a public HTTPS link. Use when a user wants something live on the internet, has just registered a domain and asks what now, needs to show work in progress, or needs a public URL for a webhook.

### `manage-dns-records`

Create, read, update and delete DNS records for a domain at OSIR, and point a domain at a host, a site or a mail provider. Use when a user wants a domain to resolve somewhere, needs an A, AAAA, CNAME, MX or TXT record, is verifying a service that asked for a DNS record, or reports that a change has not taken effect.

### `order-a-vps`

Order and manage a VPS at OSIR, including picking a package and location, adding an SSH key, and getting into the machine afterwards. Use when a user needs a server rather than shared hosting, asks about specs or regions, or cannot SSH into a VPS they already have.

### `register-a-domain`

Register a domain name at OSIR on behalf of a user, end to end. Use when a user has chosen a name and wants to buy it, or asks you to acquire a domain for them. Covers the confirmation gate, funding a short balance including agent-initiated payment, what happens automatically after registration, and the mistakes that cost real money.

### `set-up-email`

Set up mailbox hosting on a user's own domain at OSIR, with JMAP, IMAP, POP3 and SMTP. Use when a user wants email at their domain, asks for a professional address instead of a free provider, needs the anti-spam DNS records, or reports that their mail is landing in spam.

### `transfer-a-domain`

Transfer a domain into OSIR from another registrar, or check and approve a transfer already in flight. Use when a user wants to move a domain to OSIR, asks why a transfer is stuck, has received a transfer approval email, or wants to know whether a domain is eligible to move yet.


## Canonical source

These are mirrored from the discovery index the site publishes:

- Index: https://osir.com/.well-known/agent-skills/index.json
- Spec: [Agent Skills Discovery v0.2.0](https://github.com/cloudflare/agent-skills-discovery-rfc)

Every artifact in that index carries a sha256 digest and conforming clients
verify it, so **this repo is a mirror and is generated**. Do not edit a
`SKILL.md` here: the change would not reach the published index, and the two
copies would diverge silently. Open an issue, or send the change to the site
repo that generates them.

## Using OSIR without any of this

Domain lookups, pricing and suggestions are anonymous. No key, no signup:

```bash
curl https://be.osir.com/v1/public/catalog/domains/example.com/availability
```

Full API surface: https://osir.com/en/developers/ and https://be.osir.com/openapi.json
MCP server: `https://be.osir.com/mcp/http` (106 tools, 11 prompts)

## Licence

These skills are documentation. Reuse freely.
