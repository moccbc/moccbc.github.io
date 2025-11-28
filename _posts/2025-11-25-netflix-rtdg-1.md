---
author: yuta
title: Netflix Real-Time Distributed Graph (RDG) - Day 1
date: 2025-11-25 -0800
categories: [DayOne, NetflixRdg]
tags: [dayone, netflixrdg]
---

> This is my commentary on Netflix's blog article ["How and Why Netflix Built a Real-Time Distributed Graph: Part 1 — Ingesting and Processing Data Streams at Internet Scale"](https://netflixtechblog.com/how-and-why-netflix-built-a-real-time-distributed-graph-part-1-ingesting-and-processing-data-80113e124acc)

Read until "Kafka as the Ingestion Backbone"

## Notes
### Introduction
- The article states that Netflix's core offering is "streaming video on demand". It makes a distinction between this and "ad-supported plans", "live programming events", and "mobile games"(!).
  - For "ad-supported" plans, I can see that this is a fundamentally different **business** side offering. As in, they are offering the customer a deal for their core offering experience
  - Very interesting how "live programming events" isn't considered a part of their core business offering.
  - Mobile games? Really, Netflix really offers those now. This kind of reminds me of Alex Horomozi's "Get really good at one thing and do not try to chase a million other things at the same time". I don't really know why Netflix wants to get into mobile games. Also reminds me of the YouTube Playables. Why do you want to include these things in something where the core offering is watching videos? It really doesn't make sense. 
    - It also reminds me of [this](https://www.youtube.com/watch?v=_ToZs0OVAUs) YouTube video that I recently watched of the interview between Steve Huynh and Carlos Arguelles. YouTube Playables reminds me of the example that Carlos gave of the amazing system that this engineer built at Google. It was super scalable, it was a magnificent work of technology, and a gift to humanity in terms of engineering. It took the engineer 6 months to design and build. But the answer was "basically only 2" to the question, "So how many people actually use it?" It reminds me of this story because I feel that nobody actually plays the YouTube Playables. What sort of use does it actually have other than to say "We really put games on this video watching platform for the heck of it"?
- The example that they give as the motivation for this article is the following
  - Imagine a single person using a single account. They first start watching on their phone, then move to the TV, then finally logs into their tablet to play the game.
  - Netflix wants to know that all of these activities are done by a single member. 
  - Apparently, in a traditional data warehouse, these events would land in **at least** 2 different tables and may be processed at different cadences.
    - I don't actually understand this part here. Specifically, why would it land in 2 different tables?
    - What I'm thinking here is why can we not just store it in a database where the key is a unique user ID and the value is some "activity" and "location"? An activity being what are they doing (watching, playing, etc) and location being from what device? I'm sure they can tell from what device a certain user is accessing from.
    - I think this lack of understanding this thought process comes from my lack of knowledge on how database systems work in real life.
    - I do think though that I'd understand if there are tables for each activity. 
      - For example, if there is an activity for "video playing" and an activity for "playing a game", then you would have 2 tables where the key is the customer ID and the value is the boolean value for isActivityTaken.
      - It seems like a waste of space to just store a boolean value though.
    - But wait, the core question is "Given different activities, how do we tell if it belongs to the same user?" I feel like this approach wouldn't answer it because this would allow you to answer the question "Given a customer, what are all of the actions that they took?"
      - Hmm, answering this question would basically give you the answer to the core question though.
      - Answering this question means that for a given user, you have every action that they took. So you can query against that information every activity that you want to know who the user is. If there is even one activity that is not in the list that answers the second question, then the answer to the first question is a no.
    - I still don't quite understand this part. I feel like this is important to understand because otherwise how do I know that the solution that this blog article is describing is useful?
  - "But in a graph system, they become connected almost instantly"
    - I think in this situation "they" means each event. I don't understand "connected" though. How do they connect? Is each node in the graph an event and the edge between the nodes mean that the event is shared by a single user? If each node is just an event, then it doesn't make sense how they would identify for each person. But if you make a graph where the node holds the information \[who did it, event\] the edge doesn't make sense anymore because who did it is already encoded in the node itself.
    - Hmmmmm, it feels like there's some industry standard understanding going on here that the article is not explaining (or maybe it will explain it later).
 - Netflix is apparently also famous for adopting a microservices architecture. 
  - Some notable benefits of microservices nicely summarized by the article
    - Service Decomposition: The modularity that having each service be responsible for their own business usecases allows for independent service deployment and scaling.
    - Data Isolation: Each service owner is free to use whatever data storage and retrieval methods they wish to use.
      - I think these benefits are probably coming from the point of view of data gathering and analyzing.
  - However, there are drawbacks especially from the view point of data engineering where you want to retrieve insights from it.
    - For example, the separation of concerns of the services themselves also means that the data is also isolated. In order to draw insights from 2 different services, you must combine potentially differently stored data sources, with different schematics, different field names (that could mean the same thing, or not) and stitch them together carefully.
    - Man this does seem like a nightmare! This also probably means that if you did want to gain a multi-faceted insight from different services, you needed to understand each services' API return, response, their terminology, etc. Microservices get really messy really fast huh.
  - In order to resolve these drawbacks (again from a data engineering perspective), Netflix decided to build a graph representation.
    - Apparently it offers 3 benefits.
      - Relationship-Centric Queries: A query is a graph traversal. For identifying relationships between data, it makes sense as to why this would be significantly faster than a database joining operation. I don't understand what they mean here by "manual denormalization" is though.
      - Flexibility as Relationships Grow: Ideally a graph based solution would not need major schema changes or re-architecture as new connections and entities emerge.
        - I feel like this is a lie or an overexaggeration of how "flexible" this solution is. It seems to **heavily** depend on how the data is being modelled.
        - Or it may a difference between what I consider to be a "major schema change or re-architecture" and what Netflix engineers consider one to be.
      - Pattern and Anomaly Detection: The stakeholders for the team that built this use cases often require identifying hidden relationships, cycles, or groupings in the data. Obviously, these would be much easier to find out if you have a graph representation of the data.
    - I see, I think the article is now finally going to explain the answer to my first couple of questions for a graph system.
    - These 3 benefits though, seem to really depend on how you are going to model the data as a graph. It really seems like if they mess this part up, then you've really messed everything up. Which, relates to why I guess DDIA describes in Chapter 2 already the importance of Data modelling. It makes sense as to why it states "... it affects not only how software is written, but also affects how we think about the problem that we are solving".

### Ingestion and Processing
- There are 3 layers in the RDG
  - Ingestion and Processing: Receives events from the different upstream data sources. Then takes that data and converts them into the graph nodes and edges.
    - Basically seems like the graph building part.
  - Storage: Writes the graph to the persistent data stores.
  - Serving: Exposes ways for internal clients to query the graph nodes and edges.
    - Basically the API step.
- The rest of the part 1 article will talk about the first layer - the ingestion and processing layer.
- High level design is Netflix -> API Gateway -> Kafka -> Flink -> DataMesh
  - Since batch processing systems and traditional data warehouses cannot offer the low latency needed to maintain an up-to-date graph that supports real-time applications, this architecture is called a "stream processing architecture" that can update the graph as events happen.
    - Very interesting. So this is kind of like the leetcode problem "find the median as data points flow in". Except, in this case, the "median" is the graph, and the data points are likely not really data "points" but closer to "blobs".

