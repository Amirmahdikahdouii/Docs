---
title: "4. Backing Services"
weight: 4
---
# 4. Backing Services

> Treat backing services as attached resources

**Backing Service**: A backing service is any service the **app consumes over the network as part of its normal operation**.

Examples include datastores (such as `MySQL` or `CouchDB`), messaging/queueing systems (such as `RabbitMQ` or `Beanstalkd`), SMTP services for outbound email (such as `Postfix`), and caching systems (such as `Memcached`).

**The code for a twelve-factor app makes no distinction between local and third party services**. To the app, both are attached resources, accessed via a URL or other locator/credentials stored in the config.
A deploy of the twelve-factor app should be able to swap out a **local MySQL database** with **one managed by a third party** (such as Amazon RDS) **without any changes to the app’s code**.
Likewise, a local SMTP server could be swapped with a third-party SMTP service (such as Postmark) without code changes.
**In both cases, only the resource handle in the config needs to change.**

![Attached resources to an application](/images/12-factors/attached-resources.png)

**Resources can be attached to and detached from deploys at will**.
For example, if the app’s database is misbehaving due to a hardware issue, the app’s administrator might spin up a new database server restored from a recent backup. The current production database could be detached, and the new database attached – all without any code changes.
