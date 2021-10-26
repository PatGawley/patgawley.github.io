---
layout: post
title: "Firing the Change Feed"
date: 2021-10-26 14:00:00 +0100
author: Pat Gawley
---

I have had a fun morning trouble shooting an issue and thought that the resolution (at least part of it) would be worth writing up.

## Background

We use the Cosmos [Change Feed feature](https://docs.microsoft.com/en-us/azure/cosmos-db/change-feed) to batch up documents, I won't debate the wisdom of this decision here but it's what we've done. The change feed kind of works like a trigger from the RDBMS world and it felt like a handy way of batching documents up as you can specifiy how many documents you could pick up from the change feed in one go. So as the documents are inserted we have a web job hosted in an Azure web app which uses the change feed processor[^1] to consume the documents in batches, do a little processing then persist them in blob storage. We had a production issue; again I won't go into details but the upshot was that we needed to 'replay' the change feed to send documents through again i.e. fire the change feed again.

## Initialliay Considered Option

The ChangeFeedProcessorBuilder allows you to specify a start time

```
return cosmosClient.GetContainer(_databaseName, _sourceContainerName)
                .GetChangeFeedProcessorBuilder<Document>(ProcessorName, changesHandler)
                .WithInstanceName(_instanceName)
                .WithLeaseContainer(cosmosClient.GetContainer(_databaseName, _leaseContainerName))
                .WithStartTime(new System.DateTime(2021,10,25,14,0,0))
                .WithMaxItems(10)
                .Build();
```

Unfortunately this wouldn't work as the implementation meant that the ChangeFeedProcessor would be rebuilt multiple times and thus would continuously go back to the start time specified (at least that's the theory, we didn't try it and simply went onto the chosen option).

## Chosen Option

There is a [DocumentDB Data Migration tool](https://www.microsoft.com/en-us/download/details.aspx?id=46436). This allowed us to import from our collection onto itself, while not changing the document but firing the change feed. Crucially this also allowed us throttle the process by RUs.

### Step 1 - Select Your Source and Limit Using Select Query

![Select Source](/assets/SelectSource.png)

### Step 2 - Select Your Target

![Select Target](/assets/SelectTarget.png)
Make sure that you tick **Update Existing Documents** and your **Collection Throughput** (the ability to throttle is a huge advantage to this approach).

### Step 3 - Go

![Importing](/assets/ImportGo.png)

The tool reads from the table and then upserts into the same table with the same value. Nothing changes but crucually, the change feed is fired. This got us out of a tight spot and we've updated our documentation so that we have a ready to roll recovery process (always nice to have, no one likes trying to solve a problem under presssure).

[^1]: We used a web job because we needed to use the change feed processor as this allow us to implement a retry pattern if there was an error. Something you can't do when using an Azure Function trigger
