---
name: deploy-a-web-app
description: Put a site or app online with OSIR, from a one-page site to an app on the user's own server, and share a local dev server over a public HTTPS link. Use when a user wants something live on the internet, has just registered a domain and asks what now, needs to show work in progress, or needs a public URL for a webhook.
---

# Deploy a web app

Four different jobs sit behind "put it online". Pick the right one before you start.

| The user wants | Use |
|---|---|
| A one-page site, designed by conversation | `osirSiteDesignBrief` then `osirSitePublish` |
| To publish HTML they already have | `osirSitePublish` alone |
| A real app running somewhere | `osirAppCreateUpload` then `osirAppDeploy` |
| To show localhost to someone right now | `osir tunnel` on the CLI |

## A one-page site

`osirSiteDesignBrief` takes an interview-style brief and returns design instructions; **the server never calls a model, you write the HTML.** `osirSitePublish` then puts it live on a free `name.osir.app` subdomain. It is standalone: any complete HTML document up to 1 MiB works, and publishing the same name again redeploys it.

## An app

1. `osirAppCreateUpload` to get an upload target, and send the bundle.
2. `osirAppDeploy` to start it.
3. `osirAppStatus` and `osirAppLogs` to see whether it actually came up. **Always check logs after a deploy rather than reporting success from the deploy call alone.**
4. `osirAppSetSecret` for configuration. Never put a credential in the bundle.
5. `osirAppProvisionDatabase` if it needs storage.
6. `osirAppDeployToVps` moves a running app off the shared free tier onto the user's own server. The platform ships it over its own deploy key, so you need no shell access and no outbound SSH. Use this instead of writing an install script.

`osirAppList` and `osirAppDelete` manage the rest. `osirAppGetSource` retrieves what was deployed.

## A public link to localhost

`osir tunnel 3000` gives a server on the user's machine a public HTTPS address with a certificate browsers trust. Requires one `osir auth login`. Add `-o json` for a single machine-readable line with the URL, which is what you want in a script.

Useful for webhook testing, showing a client work in progress, and testing on a real phone. Addresses are random and last only while the command runs. **Anyone with the link can reach the app and there is no password**, so say that before the user points it at something private.

If their dev server answers "Blocked request" or "Invalid Host header", it is rejecting the public hostname: `server.allowedHosts: ['.osir.run']` in Vite, `allowedHosts: ['.osir.run']` in webpack-dev-server.

## Pointing a domain at it

`getHostingBundle` gives exact hosting prices for a domain the user owns or just checked. To point a name at a deployment, see [manage-dns-records](/.well-known/agent-skills/manage-dns-records/SKILL.md). A tunnel URL is for testing, never production.
