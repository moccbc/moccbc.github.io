---
author: yuta
title: Netflix Real-Time Distributed Graph (RDG) - Day 2
date: 2025-11-28 -0800
categories: [TechBlog, NetflixRdg]
tags: []
---

> Read up to right before "Processing Data with Apache Flink"

## Summary
I think reading the "Kafka as the Ingestion Backbone" section brought more questions than answers. The fundamental issue with my lack of understanding with this section is merely due to lack of knowledge about what Apache Kafka even is. 

There are terms and phrases like "topics", "messages that get published to topics", and "durability" being used in this section that I don't understand, so I don't understand what this even doing.

From what I could gather from this article though, it seems like Kafka is some kind of processing queue where it takes in some information and pops it out as needed. I don't quite understand why it is such a big component at the moment from my rudimentary understanding.

## Notes
### Kafka as the Ingestion Backbone
- What the hell is Kafka?
  - Isn't that an author's name?
    - Yes he is. That's what I thought. He's Franz Kafka and I recall from middle school that he's written "Metamorphosis" and "罪と罰" (I think that's "Crime and Punishment"?)
      - Nope, "Crime and Punishment" was written by Dostoyevsky.
    - Read his wikipedia article, very interesting person and there's a lot written about him. Seems like a tortured fellow though. Will read about him more later.
  - Why is the system named after the author? Maybe something like from the term "Kafkaesque"? I have no idea what that means though.
- "Member actions in the Netflix app are published to our API Gateway, which then writes them as records to Apache Kafka topics."
  - I thought "API Gateway" was an AWS offering for, well, an API gateway. Why do they specify "**our** API Gateway"? Is it something custom that Netflix made?
  - What is an API Gateway though?
  - In "member actions" does "member" mean a customer of Netflix and "actions" mean whatever actions they took? For example, like watching a video, playing a game?
  - I've also heard about "topics" a lot in terms of queues and data ingestion. It seems like a weird term to me because the term reminds me of news feeds. But obviously, it is not a news feed in this situation.
  - Okay to it seems like Kafka is the mechanism through which internal data applications can consume the events that occur.
  - So member actions are published to the API Gateway, these member actions are then written as records to Apache Kafka topics, then the internal data applications consume these events to process this data.
    - By doing this, it provides durable, replayable streams that downstream processors such as Apache Flink jobs can consume in real-time.
      - The durability comes from the writing of the member actions as records to Apache Kafka topics? Maybe there's a Write Ahead Logger there or something that allows for durability?
      - The replayability makes sense because I assume that once you make something durable, then you already have some sort of record of the data and how it is processed.
        - The replayaility part is very interesting.
- "Our team's applications consume several different Kafka topics, each generating up to roughly 1 million messages per second"
  - I don't quite understand how the topics relate to the messages. Is it one topic per message? Or is it each Kafka topic that generates up to 1 million messages per second? Or is it that the "team's application" generates 1 million messages per second?
    - These questions are just simply coming from my lack of understanding of how these systems work. Again, the article seems to be written towards an audience that actually understands a thing or two about these and I just cannot follow the technical jargon that is being thrown around here.
- "Topic records are encoded in the Apache Avro format, and Avro schemas are persisted in an internal centralized schema registry"
  - Okay, what's the significance of the "Apache Avro format" here?
  - Oh, [Apache Avro](https://avro.apache.org) is just a data serialization format. But what's so special about it?
    - Also I'm noticing a lot of things seem to have come from "Apache". I think that's a Native American tribe name? What does it have to do with software though?
- "In order to strike a balance between maintaining data availability and managing the financial expenses of storage infrastructure, we tailor retention policies for each topic according to its throughput and record size"
  - Hmm interesting. So that seems like a large overhead for customization though. If there are a lot of topics, you need to do a lot of maintenance work for this to tune for some sort of optimal.
  - So this sentence really makes it seem like each message is a topic. Or maybe there is a thing called a "topic" and you can publish a "message" to it. A "topic is to a message" like "book is to a page" kind of deal? In otherwords there can be multiple messages associated with a single topic? Man I just really need to learn about what this thing is about.
- "We also persist topic records to Apache Iceberg data warehouse tables, which allows us to backfill data in scenarios where older data is no longer available in the Kafka topics."
  - Okay, interesting. So I think "older data" here really means a "message", and since it seems like there are scenarios in which "older data is no longer available", that means that messages do not last that long. Maybe it's an in-memory thing?
- Before proceeding any more with this article, I need to learn about this Apache Kafka thing. I don't understand anything about this section.
