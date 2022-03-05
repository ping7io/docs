---
title: Home
nav_order: 1
permalink: /
---

# Ping7.io documentation
{: .fs-9 }

This documentation provides a quick start and comprehensive insights in configuring
and using the ping7.io managed prometheus exporters. Learn how to gather valuable
metrics from multiple outside check locations.
{: .fs-6 .fw-300 }

![Schema](https://ping7.io/assets/img/illustrations/schema.png)

## Getting started

To get started you need:

* a working [Prometheus](https://prometheus.io) installation and
  access to its configuration. This can be either a local Docker
  container, a SaaS Prometheus offering or a system installed on
  a server.
* A [ping7.io account](https://ping7.io/customer) and a api token.

💡 We recommend that you start experimenting with a local Docker container to get
started
{: .bg-grey-lt-000 .p-3 .d-block }

### Quick start: Use example repo

We have provided you with an [example Prometheus configuration repository](https://github.com/ping7io/examples)
that contains some examples how to integrate our exporters easily into your Promtheus configuration. For the
example to run, you need [Docker](https://www.docker.com/get-started) installed.

1. __Clone our example repository__

```bash
$ git clone https://github.com/ping7io/examples.git
$ cd examples
```

2. __Provide ping7.io api token__

Create a file named `ping7io-credentials` that contains your
[ping7.io api token](https://ping7.io/customer).

```bash
$ echo "YOUR_API_TOKEN" > ping7io-credentials
```

The file is later picked up by Prometheus as a secret.

3. __Start a local Prometheus__

The local Prometheus will instantly start collecting availability metrics
for the [ping7.io](https://ping7.io) and [prometheus.io](https://prometheus.io)
websites.

```bash
$ docker compose up
```

💡 Learn how to query and analyze metrics in Prometheus in the
  [example repository](https://github.com/ping7io/examples)
{: .bg-grey-lt-000 .p-3 .d-block }

### Configure your own Prometheus instance

If you have an already preconfigured Prometheus
