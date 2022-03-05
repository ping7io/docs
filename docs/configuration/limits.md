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

| plan       | requests per minute |
|:-----------|:--------------------|
| free       |  `5 rpm`            |
| basic      |  `30 rpm`           |
| enterprise |  `1000 rpm`         |


## Debugging

Prometheus talks to the ping7.io api via HTTP rest calls. These are easy
to debug. These are some of the common return codes you can expect.

### 401 Unauthorized

You either supplied a invalid api token or your api token expired. Creating
a new api token invalidates your old api token.

### 403 Forbidden

You have requested a location not included in your plan.

### 429 Too Many Requests

You ran out of requests included in your plan. Every plan includes the
request limits towards the ping7.io api as denoted above. The number of
requests is enforced per minute and may include some extra bursts.

The ping7.io api add the follwing HTTP response headers to debug your
request limit and plan:

| header                      | example                  | purpose  |
|:----------------------------|:-------------------------|:---------|
| `x-ratelimit-remaining`     | `4`                      | The remaining requests during the current minute. |
| `x-ratelimit-configuration` | `BASIC plan with 30 rpm `| A human readable definition of your current rate limit. |

These are two example `curl` calls to reproduce a `429 Too Many Requests`
error code from the api. This example works fine and returns a `200 OK`
status code along with said debug response headers.

```bash
$ curl -vH "Authorization: Bearer YOUR_API_TOKEN" \
  "https://check.ping7.io/blackbox/probe?location=eu-central&target=https//ping7.io&module=http_2xx"
< HTTP/2 200
< x-ratelimit-remaining: 4
< x-ratelimit-configuration: BASIC plan with 30 rpm
[...]
```

After repeating the above request a couple of times (> 30) during a short
time period, the API will return a `429 Too Many Requests` return code alon
with the following header values.

```bash
< HTTP/2 429
< x-ratelimit-remaining: 0
< x-ratelimit-configuration: BASIC plan with 30 rpm
```
