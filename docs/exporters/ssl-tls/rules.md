---
title: Rules and Alerts
parent: SSL/TLS-Exporter
grand_parent: Prometheus Exporters
nav_order: 10
---

# Prometheus alerting rules
{: .fs-9 }

As we are utilizing the well known Blackbox and SSL-Exporter you basically
use Promtheus rules built for those.
{: .fs-6 .fw-300 }

Within Prometheus you can define [alerting rules](https://prometheus.io/docs/prometheus/latest/configuration/alerting_rules/). Alerting rules allow you to define alert conditions based on Prometheus expression language
expressions and to send notifications about firing alerts to an external service.

💡 Our [GitHub examples repository](https://github.com/ping7io/examples) contains alerting
rule examples.
{: .bg-grey-lt-000 .p-3 .d-block }

## Well known alerting rules

The collection of [awesome Prometheus alerts](https://awesome-prometheus-alerts.grep.to/rules.html)
contains well known alerting rules for the managed Promtheus exporters available at ping7.io.

### Blackbox Exporter

The [Blackbox Exporter](../exporters/blackbox-exporter.md) checks publicly available
HTTP endpoints for return codes. The example alert rule below checks for metrics having
a HTTP status code outside of the range `[200-399]`.

```yaml
groups:
- name: blackbox
  rules:
  - alert: BlackboxProbeHttpFailure
    expr: probe_http_status_code <= 199 OR probe_http_status_code >= 400
    for: 0m
    labels:
      severity: critical
    annotations:
      summary: Blackbox probe HTTP failure (instance {\{ $labels.instance }})
      description: "HTTP status code is not 200-399\n  VALUE = {\{ $value }}\n  LABELS = {\{ $labels }}"
```

💡 Find all well known [Blackbox Exporter alerting rules here](https://awesome-prometheus-alerts.grep.to/rules.html#blackbox).
{: .bg-grey-lt-000 .p-3 .d-block }


### SSL Exporter

The [SSL Exporter](../exporters/ssl-exporter.md) is capable of detecting expired TLS certificates or
misconfigured TLS chains. It can also check OCSP stapling errors.

💡 Find all well known [SSL Exporter alerting rules here](https://awesome-prometheus-alerts.grep.to/rules.html#ssl/tls).
{: .bg-grey-lt-000 .p-3 .d-block }


## Extended alerting rules
