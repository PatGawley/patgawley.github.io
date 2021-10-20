---
layout: post
title: "First Dip In The Data Lake"
date: 2021-10-19 11:46:00 +0100
author: Pat Gawley
---

A conversation some weeks ago caused us to speculate that we may have a use case for a data lake. Our Grocery website has a requirement for a list of frequently bought items. We have the data.... sort of. Grocery website orders are posting (in XML) on an Azure Service Bus topic, as are transaction logs from our POS in store. It wouldn't take much effort to stream bonus card nominations in this way as well.

A rough, back of a cigarette packet, half-baked plan started to form. What if we streamed all this into an Azure Data Lake using Azure Event Hub? We could then use a Data Factory to run queries giving us what we need and presenting to an Azure Storage table for the website to do a simple point read. We even did a diagram.

> The Dream

![Potential Setup](/assets/DataLakeProposal.png)

Simples? Well, not quite. Collecting the data is simple-ish but for us Data Lakes are a whole new world.

> A Whole New World? - 'Come see the wonders of Big Data'

![Whole New World](/assets/wholenewworld.png)

Conceptually the raw data is immutable, a powerful idea that I haven't quite got to grips with. Then the data lake itself needs to be layered: - raw, curated and presentation. There is also the matter of file format in the curated layer:- avro, parquet? Then there is security, archiving etc.

None of these problems are insurmountable and one of our architects is currently beavering away to come up with an approach. I think we'll be able to turn something around fairly quickly. We might even avoid the dreaded data swamp.

> Data From The Dark Lagoon - 'Aaarrrrgh XML blueh!'

![Swamp Monster](/assets/swamp-monster.jpeg)

I'll post more as this develops.
