---
name: connect-to-osir
description: Connect to OSIR, an ICANN-accredited domain registrar, and authenticate. Use when you need to reach OSIR for the first time, when a tool call returns 401, or when deciding between the MCP server, the REST API and the CLI. Covers anonymous lookups that need no account, in-chat device login, OAuth, and the confirmation gate on anything that spends money.
---

# Connect to OSIR

OSIR is an ICANN-accredited registrar (IANA #4332) built to be operated by agents as well as people. Everything below is public and verifiable.

## Pick a surface

| Surface | URL | Use it when |
|---|---|---|
| MCP | `https://be.osir.com/mcp/http` | You are an MCP client. 106 tools, 11 prompts, full lifecycle. |
| MCP over OAuth | `https://be.osir.com/mcp/oauth` | You want to stay signed in across sessions. Public client id `mcp-client`. |
| REST | `https://be.osir.com` | You are not an MCP client. Spec at `https://be.osir.com/openapi.json`. |
| CLI | `osir` | You have a shell. 77 commands. |
| In-page | WebMCP on osir.com | You are a browser agent on the site already. |

## Start without an account

Lookups are anonymous. No key, no signup, no headers:

```
GET https://be.osir.com/v1/public/catalog/domains
GET https://be.osir.com/v1/public/catalog/domains/{domain}/availability
GET https://be.osir.com/namesuggestions/keyword-availability/{keyword}?tlds=com,io
```

Over MCP the equivalent tools are `checkDomainAvailability`, `checkKeywordAvailability`, `getDomainPricing`, `getDomainExtensions`, `generateDomainSuggestions` and `getHostingBundle`. 60 of the 106 tools are read-only and marked `readOnlyHint`, so a client that reads annotations can let an agent roam those freely.

**Do all research before asking the user to sign in.** Presenting a shortlist first and authenticating once is a better experience than an auth prompt at the start.

## Sign in when you need an account action

Three paths:

1. **Device login, in chat.** Call `loginWithDevice`, show the user the URL it returns, poll `checkDeviceLoginStatus` until it completes, then pass the returned `sessionKey` on **every** subsequent tool call. This is the normal path for a chat client.
2. **OAuth.** Connect to `/mcp/oauth` with client id `mcp-client`. Your Bearer token is used automatically and you never handle a `sessionKey`.
3. **Create an account without a browser.** `createAccount` then `verifyAccount`. Useful for headless agents.

`getAuthStatus` reports where you stand, but it does **not** answer with a 401 when you are anonymous. Verified live on 2026-11-02: it returns `{"authenticated": false, "username": null, "tokenExpiresIn": null}` and `isError: false`. Its declared `outputSchema` forbids those nulls, so a client that validates structured output rejects a response that was actually fine. Read `content[0].text` instead, or simply attempt the account action and read its error, which is the more reliable signal on any client. Either way, being anonymous is not being blocked: re-read the table above and check whether the thing you want is actually an account action.

## The confirmation gate

Anything that moves money or destroys data is **two calls**, never one:

1. Call the tool, for example `registerDomain`. It does **not** execute. It returns an `actionId` and an itemized summary.
2. Show the user that summary verbatim, including the total.
3. Only after they explicitly approve, call `executeConfirmedAction` with the `actionId`.

Do not try to route around this, and do not paraphrase the amount. There is no mode that disables it.

## Prices and fees

Quoted prices exclude the $0.20/yr ICANN fee on gTLDs and payment processing (3% or $0.30, whichever is greater). The authoritative all-in number is in the staged confirmation. When an availability response gives you both `price` and `totalPrice`, quote `totalPrice`.

## Related

- [check-domain-availability](/.well-known/agent-skills/check-domain-availability/SKILL.md)
- [register-a-domain](/.well-known/agent-skills/register-a-domain/SKILL.md)
- Docs: https://osir.com/en/developers/ and https://osir.com/llms.txt
