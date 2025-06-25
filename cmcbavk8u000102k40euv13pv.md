---
title: "Building Tenant ID Finder: Exploring Microsoft endpoints for fun"
datePublished: Wed Apr 02 2025 05:00:00 GMT+0000 (Coordinated Universal Time)
cuid: cmcbavk8u000102k40euv13pv
slug: building-tenant-id-finder-exploring-microsoft-endpoints-for-fun
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1750816364891/f9044955-003e-44f1-9da0-3eb969d87ed7.jpeg
tags: automation, microsoft365

---

In my day-to-day work supporting MSPs and building automations, I often found myself solving the same kinds of problems over and over. Sometimes the fix is small, like a PowerShell script or a DNS record check. Other times, a missing bit of context slows everything down, like knowing which Microsoft 365 tenant a domain belongs to.  
  
That’s what sparked the idea behind [tenantidfinder.com](http://tenantidfinder.com).

[It started from](https://tenantidfinder.com) a personal need. I was working through [a list of domains](https://tenantidfinder.com) and needed to find their associated Microsoft 365 tenant IDs. Not one or two, dozens. And doing it manually was quickly becoming a pain. I knew the information was publicly accessible with the right OpenID Connect call, but there wasn’t a quick, no-login tool to do it in bulk. So I built one.

### The Problem

If you've worked at an MSP or supported clients with multiple brands, you're probably familiar with the mess that is tracking which domain belongs to which tenant. Sometimes there’s one tenant per brand, sometimes not. When a client asks you to verify something, or you’re building automation that needs a tenant ID, you're left hopping between portals, logging into each tenant, checking verified domains, and wasting a ton of time.

You can’t look up domains from a tenant ID (at least not easily or reliably), but thanks to OpenID Connect, you *can* look up the tenant ID from a domain. That tiny asymmetry is where this tool lives.

### What It Does

[tenantidfinder.com](http://tenantidfinder.com) takes in a list of domains, just type or paste them in, and gives you back:

* The tenant ID, pulled using an OpenID Connect `.well-known` discovery call.
    
* MX, SPF, and DMARC DNS records, resolved using DNS over HTTPS.
    
* Autodiscover CNAME records for Microsoft Exchange.
    
* CSV export so you can save results or import them into another tool.
    

[I](https://tenantidfinder.com)t’s designed to be dead simple. No login. No OAuth. Just paste and go.

This isn’t meant to replace complex domain tooling or security tools, but it fills a very specific gap: figuring out what tenant a domain belongs to and confirming it behaves like a typical Microsoft-hosted domain.

### Technical Notes

The two core pieces of functionality are:

* **OpenID Connect for tenant discovery**. Specifically, I’m querying [`https://login.microsoftonline.com/{domain}/.well-known/openid-configuration`](https://login.microsoftonline.com/{domain}/.well-known/openid-configuration) and parsing the returned JSON for the `issuer` value. That’s where the tenant ID is embedded.
    
* **DNS lookups via DNS over HTTPS**. I’m using a public DoH endpoint (like Cloudflare or Google) to fetch DNS records. This means the lookups are consistent, fast, and don’t rely on browser or network-specific behavior.
    

Everything is built into a static site. There’s no backend, no logging, no analytics—just client-side JavaScript. That keeps it fast and private. You could use it on an airplane Wi-Fi connection or a locked-down client network and it would still do its job.

### Why I Shared It

My friend Joel once told me to keep dropping “MSP cheat codes” in the form of LinkedIn posts. I liked that term. It sums up the spirit of this kind of work: solving problems just enough to turn them into tools or shortcuts for others.

That’s really why I built and shared Tenant ID Finder. Not just to save myself time, but to put something useful in the hands of other techs who run into the same wall. I’ve seen people do this the hard way, and while it’s not *hard*, it’s tedious and repetitive. If this tool saves someone 20 minutes or prevents a wrong tenant ID in a PowerShell script, I’m calling it a win.