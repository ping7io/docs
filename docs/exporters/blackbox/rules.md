---
title: Rules and Alerts
parent: Blackbox Exporter
grand_parent: Prometheus Exporters
nav_order: 3
---

# Prometheus alerting rules
{: .fs-9 }

Within Prometheus you can define [alerting rules](https://prometheus.io/docs/prometheus/latest/configuration/alerting_rules/). Alerting rules allow you to define alert conditions based on Prometheus expression language
expressions and to send notifications about firing alerts to an external service.

💡 Our [GitHub examples repository](https://github.com/ping7io/examples) contains alerting
rule examples.
{: .bg-grey-lt-000 .p-3 .d-block }

## Basic alerting rules

The collection of [awesome Prometheus alerts](https://awesome-prometheus-alerts.grep.to/rules.html)
contains alerting rules for the managed Promtheus exporters available at ping7.io.
The example alert rule below checks for metrics having a HTTP status code outside of the range `[200-399]`.

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

💡 Find all [awesome Blackbox Exporter alerting rules here](https://awesome-prometheus-alerts.grep.to/rules.html#blackbox).
{: .bg-grey-lt-000 .p-3 .d-block }


## Advanced alerting rules

Coming soon
{: .label .label-yellow }
