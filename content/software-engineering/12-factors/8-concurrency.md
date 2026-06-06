---
title: "Concurrency"
weight: 8
---
# Concurrency

> Scale out via the process model

Any computer program, once run, is represented by **one or more process**.
**Processes** in the twelve factor app play a crutial role, and they take strong cues from the unix process model for running service daemons.

By using this model, apps can run in multiple instances and they are totally separated from each other, allowing to better response to end user.

For example, HTTP requests can be handled by web apps but long running processes that takes time, could delegate to other processes like `workers`.

There are 2 different types of scaling:

1. Vertical scaling: VMs can scale vertically, by adding more VMs with more resources to existing ones.
2. Horizontal scaling: In this type of scaling, apps running on a VM could scale in multiple instances, allow better response.

- [Source - GeeksForGeeks](https://www.geeksforgeeks.org/system-design/system-design-horizontal-and-vertical-scaling/)

The process model truly shines when it comes time to scale out. The share-nothing, horizontally partitionable nature of twelve-factor app processes means that **adding more concurrency is a simple and reliable operation**. The array of process types and number of processes of each type is known as the process formation.

**Twelve-factor app processes should never daemonize** or **write PID files**.
Instead, rely on the operating system’s process manager (such as `systemd`, a distributed process manager on a cloud platform, or a tool like `Foreman` in development) to manage output streams, respond to crashed processes, and handle user-initiated restarts and shutdowns.
