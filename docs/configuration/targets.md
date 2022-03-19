---
title: Target Selection
parent: General Configuration
nav_order: 3
---

# Configure check targets in Prometheus
{: .fs-9 .no_toc}

Check target can either be provided as a static list or via the
api clients integrated in Prometheus (e.g. for Kubernetes or AWS).
{: .fs-6 .fw-300 .no_toc}


On this page, you'll find examples for [Prometheus `scrape_configs`](https://prometheus.io/docs/prometheus/latest/configuration/configuration/#scrape_config) to feed check targets into
any of the Prometheus Exporters ping7 is offering.

1. TOC
{:toc}

💡 If possible you should aim for an automatic target selection via some api.
For the beginning, the static target selection is the quickest way to get
started.
{: .bg-grey-lt-000 .p-3 .d-block .emoji}

## Static targets

The easiest integration is the [static target configuration](https://prometheus.io/docs/prometheus/latest/configuration/configuration/#static_config).
Here, you explicitly list the websites you want to check. You can
list any HTTP or HTTPS website that is publicly available.

```yaml
scrape_configs:
  - job_name: blackbox-eu-central
    # general config options here
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

### `relabel_config` for static targets

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

## Kubernetes Ingress endpoints

This example uses the [Kubernetes API](https://prometheus.io/docs/prometheus/latest/configuration/configuration/#kubernetes_sd_config)
to retrieve endpoints in the current cluster.

```yaml
scrape_configs:
  - job_name: blackbox-eu-central
    # general config options here
    kubernetes_sd_configs:
      - role: ingress
    relabel_configs:
      - source_labels: [__meta_kubernetes_ingress_host]
        regex: (.+)
        action: keep
      - source_labels: [__meta_kubernetes_ingress_scheme,__meta_kubernetes_ingress_host]
        separator: ://
        target_label: __param_target
        action: replace
      - source_labels: [__param_target]
        target_label: instance
      - source_labels: [__address__]
        target_label: __address__
        replacement: check.ping7.io
      - source_labels: [__param_location]
        target_label: location
```

### `relabel_config` for Kubernetes Ingresses

These [`relabel_configs`](https://prometheus.io/docs/prometheus/latest/configuration/configuration/#relabel_config)
transform the configured Prometheus options for this job
into a valid ping7.io API request.

```yaml
relabel_configs:
  - source_labels: [__meta_kubernetes_ingress_host]
    regex: (.+)
    action: keep
  - source_labels: [__meta_kubernetes_ingress_scheme,__meta_kubernetes_ingress_host]
    separator: ://
    target_label: __param_target
    action: replace
  - source_labels: [__param_target]
    target_label: instance
  - source_labels: [__address__]
    target_label: __address__
    replacement: check.ping7.io
  - source_labels: [__param_location]
    target_label: location
```

## More examples & ideas

Prometheus already bundles a lot of api clients. Here are some ideas how to
automatically provision your targets:

* Tag your EC2 instances or DigitalOcean droplets with a specific tag. List
  all your instances and filter for the specific tags. Use the tag values
  as targets.
