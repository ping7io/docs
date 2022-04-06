---
title: Rules and Alerts
parent: Blackbox Exporter
grand_parent: Prometheus Exporters
nav_order: 3
---

# Prometheus alerting rules for the Blackbox Exporter
{: .fs-9 }

Within Prometheus you can define [alerting rules](https://prometheus.io/docs/prometheus/latest/configuration/alerting_rules/). Alerting rules allow you to define alert conditions based on Prometheus expression language
expressions and to send notifications about firing alerts to an external service.

💡 Our [GitHub examples repository](https://github.com/ping7io/examples) contains alerting
rule examples.
{: .bg-grey-lt-000 .p-3 .d-block }

## Basic alerting rules

The collection of [awesome Prometheus alerts](https://awesome-prometheus-alerts.grep.to/rules.html)
contains alerting rules for the managed Promtheus exporters available at ping7.io. The following
rules are the important ones for the Blackbox Exporter.

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

- alert: BlackboxSlowProbe
    expr: avg_over_time(probe_duration_seconds[1m]) > 1
    for: 1m
    labels:
      severity: warning
    annotations:
      summary: Blackbox slow probe (instance {\{ $labels.instance }})
      description: "Blackbox probe took more than 1s to complete\n  VALUE = {\{ $value }}\n  LABELS = {\{ $labels }}"

- alert: BlackboxSslCertificateWillExpireSoon
    expr: probe_ssl_earliest_cert_expiry - time() < 86400 * 30
    for: 0m
    labels:
      severity: warning
    annotations:
      summary: Blackbox SSL certificate will expire soon (instance {\{ $labels.instance }})
      description: "SSL certificate expires in 30 days\n  VALUE = {\{ $value }}\n  LABELS = {\{ $labels }}"

- alert: BlackboxSslCertificateWillExpireSoon
    expr: probe_ssl_earliest_cert_expiry - time() < 86400 * 3
    for: 0m
    labels:
      severity: critical
    annotations:
      summary: Blackbox SSL certificate will expire soon (instance {\{ $labels.instance }})
      description: "SSL certificate expires in 3 days\n  VALUE = {\{ $value }}\n  LABELS = {\{ $labels }}"

- alert: BlackboxSslCertificateExpired
    expr: probe_ssl_earliest_cert_expiry - time() <= 0
    for: 0m
    labels:
      severity: critical
    annotations:
      summary: Blackbox SSL certificate expired (instance {\{ $labels.instance }})
      description: "SSL certificate has expired already\n  VALUE = {\{ $value }}\n  LABELS = {\{ $labels }}"
```

💡 Find all [awesome Blackbox Exporter alerting rules here](https://awesome-prometheus-alerts.grep.to/rules.html#blackbox).
{: .bg-grey-lt-000 .p-3 .d-block }


## Advanced alerting rules

Coming soon
{: .label .label-yellow }
