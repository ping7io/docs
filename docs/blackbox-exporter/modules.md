---
title: Blackbox Exporter Modules
parent: Blackbox Exporter
nav_order: 2
---

# Blackbox Exporter
{: .fs-9 }

Use the Blackbox Exporter to check your endpoints for expected HTTP return codes
or general availability via ICMP ping requests.
{: .fs-6 .fw-300 }



checks the result of publicly available enpoints
against _modules_.

https://github.com/prometheus/blackbox_exporter#prometheus-configuration

🕞 We are deploying the `0.20.0` version of the Blackbox Exporter.
{: .emoji .bg-grey-lt-000 .p-3 .d-block .mt-8}

## Endpoints


### `/blackbox/probe`

| parameter     | example            | default    | description            |
|:--------------|:-------------------|:-----------|:-----------------------|
| `target`      | `https://ping7.io` | --         |The target url to test |
| `module`      | `http_2xx`         | `http_2xx` |The module to check against the target urls response (see below) |

➡️ Full Example to check ping7.io for a HTTP 200 return code: `https://check.ping7.io/blackbox/probe?target=https://ping7.io&module=http_2xx`
{: .emoji .bg-grey-lt-000 .p-3 .d-block .mt-8}

### `/blackbox/debug`


### `/blackbox/config`


## Modules

The Blackbox Exporter

https://github.com/prometheus/blackbox_exporter#configuration

| module               | prober | ip stack | description |
|:---------------------|:-------|:---------|:------------|
| `http_2xx`           | `http` | `ipv4`   | _The default module_. Issues a HTTP `GET` request and expects a `200` HTTP return code |
| `http_3xx`           | `http` | `ipv4`   | |
| `http_3xx_temporary` | `http` | `ipv4`   | |
| `http_3xx_permanent` | `http` | `ipv4`   | |
| `http_4xx`           | `http` | `ipv4`   | |
| `http_401`           | `http` | `ipv4`   | |
| `ping`               | `icmp` | `ipv4`   | |

This is our current Blackbox Exporter configuration:

```yaml
modules:
  http_2xx:
    prober: http
    timeout: 2s
    http:
    preferred_ip_protocol: ip4
  http_3xx:
    prober: http
    timeout: 2s
    http:
    valid_status_codes: [ 301, 302, 303, 304, 305, 306, 307, 308]
    preferred_ip_protocol: ip4
  http_3xx_temporary:
    prober: http
    timeout: 2s
    http:
    valid_status_codes: [ 302, 307]
    preferred_ip_protocol: ip4
  http_3xx_permanent:
    prober: http
    timeout: 2s
    http:
    valid_status_codes: [ 301, 308]
    preferred_ip_protocol: ip4
  http_4xx:
    prober: http
    timeout: 2s
    http:
    valid_status_codes: [ 401, 403 ]
    preferred_ip_protocol: ip4
  http_401:
    prober: http
    timeout: 2s
    http:
    valid_status_codes: [ 401 ]
    preferred_ip_protocol: ip4
  ping:
    prober: icmp
    timeout: 1s
    icmp:
    preferred_ip_protocol: ip4
```

💡 You can query the configured modules using the `/blackbox/config`
endpoint.
{: .bg-grey-lt-000 .p-3 .d-block .mt-8}
