---
title: Job Configuration
parent: SSL/TLS-Exporter
grand_parent: Prometheus Exporters
nav_order: 1
---

# Configure SSL Exporter in Prometheus
{: .fs-9 }

Check target can either be provided as a static list or via the
api clients integrated in Prometheus (e.g. for Kubernetes). Targets
can be HTTP endpoints only.

## Configure static targets

The easiest integration is the [static target configuration](https://prometheus.io/docs/prometheus/latest/configuration/configuration/#static_config).
Here, you explicitly list the websites you want to check. Below you find an example examining
the TLS certificates of two websites from the `eu-central` location.


```yaml
scrape_configs:
  - job_name: tls-eu-central
    scrape_interval: 5m
    metrics_path: /tls/probe
    scheme: https
    authorization:
      credentials_file: /etc/prometheus/ping7io-token
    params:
      location: [eu-central]
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

## General configuration options

Let's break down the configuration options. Most of these options are defined in Prometheus
[`<scrape_config>`](https://prometheus.io/docs/prometheus/latest/configuration/configuration/#scrape_config)
and apply to all ways of configuring targets (static of via Kubernetes Ingress).

### Scrape interval

How frequently to scrape targets.

```yaml
scrape_interval: 5m
```

You can supply any [valid Prometheus duration](https://prometheus.io/docs/prometheus/latest/configuration/configuration/#duration) like `5s`, `1m`, `1h45m` or `7d`.

💡 As the analysis of the SSL Exporter is costlier than other checks,
we recommend to examine TLS certificates not more often than every `5m`.
{: .bg-grey-lt-000 .p-3 .d-block .emoji}

### Exporter selection

The exporter to scrape. This is the endpoint of the TLS-Exporter.

```yaml
metrics_path: /tls/probe
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

### Check location

Configures the locations to issue the check from.

```yaml
params:
  location: [eu-central]
```

Check out the [available locations](locations.md).

### Prometheus request transformation & Metric labelling

These [`relabel_configs`](https://prometheus.io/docs/prometheus/latest/configuration/configuration/#relabel_config)
transform the configured Prometheus options for this job
into a valid ping7.io API request.

```yaml
relabel_configs:
  - source_labels: [__address__]
    target_label: __param_target
  - source_labels: [__param_target]
    target_label: instance
  - source_labels: [__address__]
    target_label: __address__
    replacement: check.ping7.io
```
The configuration above would result in the following api call.

```bash
$ curl -vH "Authorization: Bearer YOUR_API_TOKEN" \
  "https://check.ping7.io/tls/probe?location=eu-central&target=https//ping7.io"
```

### Target selection

Check the [general configuration for target selection](../../configuration/targets.md).
