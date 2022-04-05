---
title: Enpoints and Modules
parent: Blackbox Exporter
grand_parent: Prometheus Exporters
nav_order: 2
---

# Blackbox Exporter Modules
{: .fs-9 .no_toc}

The Blackbox Exporter module describes the check to execute against the
given target and the check that is perfomed on the response if any. This
page describes the available endpoints and modules.
{: .fs-6 .fw-300 .no_toc}

1. TOC
{:toc}

## Endpoints

The Blackbox Exporter exposes two endpoints. Configure the endpoint to use
in your [Prometheus job configuration](targets.html) as the `metrics_path`.

### `/blackbox/probe`

Does the actual probe of a given target. It has the following parameters

| parameter     | example            | default    | description            |
|:--------------|:-------------------|:-----------|:-----------------------|
| `target`      | `https://ping7.io` | --         |The target url to test |
| `module`      | `http_2xx`         | `http_2xx` |The module to check against the target urls response (see below) |
| `debug`       | `true`             | `false`    |Displays debug output from the executed check|

➡️ Full Example to check prometheus.io for a HTTP 200 return code: `https://check.ping7.io/blackbox/probe?target=https://prometheus.io&module=http_2xx`
{: .emoji .bg-grey-lt-000 .p-3 .d-block .mt-8}


### `/blackbox/config`

Returns the current module configuration of the Blackbox Exporter (see below). This endpoint
does not take any parameters.

➡️ Full Example: `https://check.ping7.io/blackbox/config`
{: .emoji .bg-grey-lt-000 .p-3 .d-block .mt-8}


## Modules

With every probe the Blackbox Exporter expects a _module_ that defines the check
to be issued against the target and the check that should be conducted against the
response, if any.

💡 You can supply your custom module in the enterprise plan.
{: .bg-grey-lt-000 .p-3 .d-block .emoji}

https://github.com/prometheus/blackbox_exporter/blob/master/CONFIGURATION.md

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
