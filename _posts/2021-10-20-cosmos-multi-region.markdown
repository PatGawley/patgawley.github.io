---
layout: post
title: "Cosmos Region Resilience"
date: 2021-10-20 14:00:00 +0100
author: Pat Gawley
---

One of our Senior Software Engineers ([Dave Edwards]) piked up a spike to investigate region making one of our Cosmos Dbs region resilience. The result was a very detailed wiki and set of test repos which I thought I'd summarize on this blog.

### What are we looking for?

The Cosmos Db in question supports a business critical app which needs to be immune to Azure regional outages. Looking at the [Global Distribution Overview] from Microsoft, some bold statements are made.

> Unlimited elastic write and read scalability.

> 99.999% read and write availability all around the world.

> Guaranteed reads and writes served in less than 10 milliseconds at the 99th percentile.

### Regions - How Many and Which ones?

### Single Or Multiple Region Writes?

### Client Configuration

Conflict resolution. Prefer region in which they are hosted.

### Zonal Redunancy

### Consistency

### Automatic vs Manual Failover

### Summary

Costs

> The Dream

![Potential Setup](/assets/DataLakeProposal.png)

[dave edwards]: https://github.com/davidedwards-iceland
[global distribution overview]: https://docs.microsoft.com/en-us/azure/cosmos-db/distribute-data-globally
