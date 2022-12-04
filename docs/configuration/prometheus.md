---
title: Prometheus Job Config
parent: General Configuration
nav_order: 2
---

# General Prometheus Configuration
{: .fs-9 .no_toc}

Integrate ping7.io Exporters seamlessly into your existing Prometheus
configuration.
{: .fs-6 .fw-300 .no_toc}

1. TOC
{:toc}

This page lays out the general configuration options for Exporter
selection, authentication und scrape interval. Below you find a example
integration for a check for two websites using the [Blackbox Exporter](/blackbox-exporter)
from two locations.

```yaml
scrape_configs:
  - job_name: blackbox-eu-central
    scrape_interval: 1m
    metrics_path: /blackbox/probe
    scheme: https
    authorization:
      credentials_file: /etc/prometheus/ping7io-token
    params:
      module: [http_2xx]
      location: [eu-central, eu-north]
    static_configs:
      - targets:
          - https://prometheus.io
          - https://ping7.io
    relabel_configs:
      - source_labels: [__address__]
        target_label: __param_target
      - source_labels: [__param_target]
        target_label: instance
      - source_labels: [__address__]
        target_label: __address__
        replacement: check.ping7.io
```

This configuration lets Prometheus query metrics using the following HTTP call:

```bash
$ curl -vH "Authorization: Bearer YOUR_API_TOKEN" \
  "https://check.ping7.io/blackbox/probe?target=https//ping7.io&module=http_2xx&location=eu-central&location=eu-north"
```


## General configuration options

Let's break down the configuration options.These options are defined in Prometheus
[`<scrape_config>`](https://prometheus.io/docs/prometheus/latest/configuration/configuration/#scrape_config)
and if not marked otherwise apply to all Exporter configurations.


### Scrape interval

How frequently to scrape targets.

```yaml
scrape_interval: 1m
```

You can supply any [valid Prometheus duration](https://prometheus.io/docs/prometheus/latest/configuration/configuration/#duration) like `5s`, `1m`, `1h45m` or `7d`.

### Exporter selection

The exporter to scrape, in this case the [Blackbox Exporter](../exporters/blackbox-exporter.md).

```yaml
metrics_path: /blackbox/probe
```

Find the [list of available exporters here](../exporters/).

### Authorization

You ping7.io api token as secret. You can either supply it directly in
your Promtheus configuration as `credentials` or store it in a file.
Supply the filename containing the ping7.io api token as `credentials_file`.

```yaml
authorization:
  [ credentials: <secret> ]
  [ credentials_file: <filename> ]
```

### Check expectation (module)

Blackbox Exporter
{: .label }

Every exporter checks the given targets for a specific outcome. In this
case the [Blackbox Exporter](../exporters/blackbox-exporter.md) checks
for a `HTTP 200` return code.

```yaml
params:
  module: [http_2xx]
```

Every exporter defines its own expectation defintion. Check out the
[Blackbox Exporter chapter](../exporters/blackbox-exporter.md) for
available `module` parameters.

### Check location

Configures the location to issue the check from.

```yaml
params:
  location: [eu-central, eu-north, us-east]
```

Check out the [available locations](locations.md).

### Prometheus request transformation & Metric labelling

These [`relabel_configs`](https://prometheus.io/docs/prometheus/latest/configuration/configuration/#relabel_config)
transform the configured Prometheus options for this job
into a valid ping7.io API request. `relabel_configs` vary
per [service discovery or target configuratin](targets.html).

```yaml
relabel_configs:
  - source_labels: [__address__]
    target_label: __param_target
  - source_labels: [__param_target]
    target_label: instance
  - source_labels: [__address__]
    target_label: __address__
    replacement: check.ping7.io
  - source_labels: [__param_location]
    target_label: location
```
