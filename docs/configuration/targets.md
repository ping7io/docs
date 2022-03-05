---
title: Targets
parent: Configuration
nav_order: 1
---

# Configure check targets in Prometheus
{: .fs-9 }

Check target can either be provided as a static list or via the
api clients integrated in Prometheus (e.g. for Kubernetes.
{: .fs-6 .fw-300 }


## Configure static targets

The easiest integration is the static target configuration. Here, you explicitly
list the websites you wan to check.

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
      - source_labels: [__param_location]
        target_label: location
```

## General configuration options

Let's break down the configuration options. Most of these options are defined in Prometheus
[`<scrape_config>`](https://prometheus.io/docs/prometheus/latest/configuration/configuration/#scrape_config)
and apply to all ways of configuring targets (static of via Kubernetes Ingress).

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

### Check expectation

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

Configures the location to issue the check from. You
can only supply a _single location_.

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
  - source_labels: [__param_location]
    target_label: location
```
The configuration above would result in the following api call.

```bash
$ curl -vH "Authorization: Bearer YOUR_API_TOKEN" \
  "https://check.ping7.io/blackbox/probe?location=eu-central&target=https//ping7.io&module=http_2xx"
```

## Target selection

If possible you should aim for an automatic target selection via some api.
For the beginning, the static target selection is the quickest way to get
started.

### Static target selection

Configures [static targets](https://prometheus.io/docs/prometheus/latest/configuration/configuration/#static_config)
to check. You can list any HTTP or HTTPS
website that is publicly available.

```yaml
static_configs:
  - targets:
      - https://prometheus.io
      - https://ping7.io
```

### Kubernetes: Configure Ingress endpoints

Uses the [Kubernetes API](https://prometheus.io/docs/prometheus/latest/configuration/configuration/#kubernetes_sd_config)
to retrieve endpoints in the current cluster.

...

### More examples & ideas

Prometheus already bundles a lot of api clients. Here are some ideas how to
automatically provision your targets:

* Tag your EC2 instances or DigitalOcean droplets with a specific tag. List
  all your instances and filter for the specific tags. Use the tag values
  as targets.
