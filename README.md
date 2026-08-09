# DNS-Tracing

A manual, iterative DNS resolution trace for `www.metallica.com`, performed using `dig` with the `+norecurse` flag to query each server in the DNS hierarchy directly rather than relying on recursive resolution.

## What this shows

Starting from a root DNS server, each query follows the referral chain returned by the previous server — root → TLD → authoritative — mimicking how a recursive resolver builds up an answer step by step.

The trace also surfaces DNS-based CDN indirection: `www.metallica.com` resolves through a custom CDN hostname (`cc-ecdn.net`), which in turn resolves through Cloudflare's edge network, illustrating how CDNs use DNS chaining to route traffic.

## Method

```bash
dig @198.41.0.4 www.metallica.com +norecurse
dig @192.5.6.30 www.metallica.com +norecurse
dig @162.159.24.4 www.metallica.com +norecurse
dig @198.41.0.4 commcloud.prod-bcpj-metallica-com.cc-ecdn.net +norecurse
dig @192.5.6.30 commcloud.prod-bcpj-metallica-com.cc-ecdn.net +norecurse
dig @205.251.194.219 commcloud.prod-bcpj-metallica-com.cc-ecdn.net +norecurse
dig @198.41.0.4 commcloud.prod-bcpj-metallica-com.cc-ecdn.net.cdn.cloudflare.net +norecurse
dig @192.5.6.30 commcloud.prod-bcpj-metallica-com.cc-ecdn.net.cdn.cloudflare.net +norecurse
dig @108.162.198.250 commcloud.prod-bcpj-metallica-com.cc-ecdn.net.cdn.cloudflare.net +norecurse
```

Each `@server` is the IP address returned by the previous query as the next server to ask, working down from a root server toward the domain's authoritative answer.

## Context

Coursework for COMP2860 (Networks) — University of Leeds.
