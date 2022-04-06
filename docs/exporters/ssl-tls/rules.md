---
title: Rules and Alerts
parent: SSL/TLS-Exporter
grand_parent: Prometheus Exporters
nav_order: 10
---

# Prometheus alerting rules for the SSL Exporter
{: .fs-9 }

Within Prometheus you can define [alerting rules](https://prometheus.io/docs/prometheus/latest/configuration/alerting_rules/). Alerting rules allow you to define alert conditions based on Prometheus expression language
expressions and to send notifications about firing alerts to an external service.

💡 Our [GitHub examples repository](https://github.com/ping7io/examples) contains alerting
rule examples.
{: .bg-grey-lt-000 .p-3 .d-block }

## Basic alerting rules

The collection of [awesome Prometheus alerts](https://awesome-prometheus-alerts.grep.to/rules.html)
contains alerting rules for the managed Promtheus exporters available at ping7.io. The following
rules are the important ones for the SSL Exporter.

```yaml
groups:
- name: ssl
  rules:

  - alert: SslCertificateProbeFailed
    expr: ssl_probe_success == 0
    for: 0m
    labels:
      severity: critical
    annotations:
      summary: SSL certificate probe failed (instance {\{ $labels.instance }})
      description: "Failed to fetch SSL information {\{ $labels.instance }}\n  VALUE = {\{ $value }}\n  LABELS = {\{ $labels }}"

  - alert: SslCertificateOscpStatusUnknown
    expr: ssl_ocsp_response_status == 2
    for: 0m
    labels:
      severity: warning
    annotations:
      summary: SSL certificate OSCP status unknown (instance {\{ $labels.instance }})
      description: "Failed to get the OSCP status {\{ $labels.instance }}\n  VALUE = {\{ $value }}\n  LABELS = {\{ $labels }}"

  - alert: SslCertificateRevoked
    expr: ssl_ocsp_response_status == 1
    for: 0m
    labels:
      severity: critical
    annotations:
      summary: SSL certificate revoked (instance {\{ $labels.instance }})
      description: "SSL certificate revoked {\{ $labels.instance }}\n  VALUE = {\{ $value }}\n  LABELS = {\{ $labels }}"

  - alert: SslCertificateExpiry(<7Days)
    expr: ssl_verified_cert_not_after{chain_no="0"} - time() < 86400 * 7
    for: 0m
    labels:
      severity: warning
    annotations:
      summary: SSL certificate expiry (< 7 days) (instance {\{ $labels.instance }})
      description: "{\{ $labels.instance }} Certificate is expiring in 7 days\n  VALUE = {\{ $value }}\n  LABELS = {\{ $labels }}"
```

💡 Find all [awesome Blackbox Exporter alerting rules here](https://awesome-prometheus-alerts.grep.to/rules.html#ssl/tls).
{: .bg-grey-lt-000 .p-3 .d-block }


## Advanced alerting rules

Coming soon
{: .label .label-yellow }
