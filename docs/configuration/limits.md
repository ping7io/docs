---
title: Account limits
parent: Configuration
nav_order: 5
---

# Account limits
{: .fs-9 }

Just the Docs gives your documentation a jumpstart with a responsive Jekyll theme that is easily customizable and hosted on GitHub Pages.
{: .fs-6 .fw-300 }

## Account limits in place

## Debugging

Prometheus talks to the ping7.io api via HTTP rest calls. These are easy
to debug. These are some of the common return codes you can expect.

#### 401 Unauthorized

You either supplied a invalid api token or your api token expired. When you
create a new api token

#### 403 Forbidden

#### 429 Too Many Requests

```bash
$ curl -vH "Authorization: Bearer YOUR_API_TOKEN" \
  "https://check.ping7.io/blackbox/probe?location=eu-central&target=https//ping7.io&module=http_2xx"
< HTTP/2 200
< x-ratelimit-remaining: 4
< x-ratelimit-configuration: BASIC plan with 30 rpm
[...]
```

```bash
< HTTP/2 429
< x-ratelimit-remaining: 0
< x-ratelimit-configuration: BASIC plan with 30 rpm
```
