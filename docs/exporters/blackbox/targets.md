---
title: Job Configuration
parent: Blackbox Exporter
grand_parent: Prometheus Exporters
nav_order: 1
---

# Configure Blackbox Exporter in Prometheus
{: .fs-9 }

In Prometheus you have to configure a distinct job per Exporter
per location you want to check your targets with. Target can
either be provided as a static list or via the api clients
integrated in Prometheus (e.g. Ingresses in Kubernetes)

## Configure job

The easiest integration is the [static target configuration](https://prometheus.io/docs/prometheus/latest/configuration/configuration/#static_config).
Here, you explicitly list the websites you want to check. Below you find an example checking
two websites from three locations for a `HTTP 200` response code.

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
      location:
        - eu-central
        - eu-north
        - us-east
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

💡 In case of the Blackbox Exporter we recommend
that you check every target from at least three locations.
{: .bg-grey-lt-000 .p-3 .d-block .emoji}


## Configuration options

💡 The general configuration options like authentication and scrape interval
are described in the [general configuration section](../configuration/targets.md).
{: .bg-grey-lt-000 .p-3 .d-block .emoji}

The following configuration options are specfic to the Blackbox Exporter.

### Exporter selection

Selects the Blackbox Exporter to scrape.

```yaml
metrics_path: /blackbox/probe
```

Find the [list of available exporters here](../exporters/).

### Module selection

The Blackbox Exporter module describes the check to execute against the
target and the check that is perfomed on the response if any. Select
a module from the [list of available modules](modules.md).


The `http_2xx` module executes a HTTP request and checks for a
`HTTP 200` return code.

```yaml
params:
  module: [http_2xx]
```

### Check location

Configures the locations to issue the check from.

```yaml
params:
  location: [eu-central, eu-north, us-east]
```

Check out the [available locations](locations.md).

<i class="bi bi-github"></i> [Blackbox Exporter Configuration in it's GitHub Repo](https://github.com/prometheus/blackbox_exporter#prometheus-configuration)
{: .bg-grey-lt-000 .p-3 .d-block}

### Target selection

Check the [general configuration for target selection](../../configuration/targets.md).
