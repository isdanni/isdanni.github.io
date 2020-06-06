---
layout: single
title: "Streaming Systems: Data Processing, Watermarks & Advanced Windowing"
author: "Danni"
toc: true
toc_sticky: true
tags:
  - System Design
  - Distributed System
---

This post is my reading notes of **Part 1, The Beam Model(Chapter 1-4)** from the book, which covers the high-level batch, streaming data processing model called [Apache Beam](https://beam.apache.org/);

# Streaming 101

## 1. What is streaming?

### Streaming System

A type of <u>data processing engine</u> designed with <u>infinite</u> datasets in mind.

### Shape of a dataset

|   | **Cardinality**  | **Consitution**  |
|---|---|---|
|definition | its size, with the most salient aspect of cardinality being whether the given dataset is infinite or finite;  | physical manifestation, which defines the way one can interact with the given dataset;  |
| types  | - **Bounded data**: a dataset that is finite in size;<br>- **Unbounded data**: a dataset that is infinite in size(at least theoretically); |  Two primary constitutions of importance is:<br> - **Table**: A holistic view of a dataset at a specific point in time. SQL systems have traditionally dealt in tables;<br> - **Stream**: An element-by-element view of the evolution of the dataset over time. The MapReduce lineage of data processing systems have traditionally dealt in streams. |

### Why stream processing is important?

- business requires more *<u>timely insights</u>* & streaming achieves lower *<u>latency</u>*;
- easier to manage massive, *<u>unbounded</u>* dataset that are increasingly common nowadays;
- more *<u>consistent, predictable comsumption of resources</u>* since the incoming data arrival is spread out evenly;

## 2. Background

### Lambda Architecture

> **[Lambda Architecture](https://en.wikipedia.org/wiki/Lambda_architecture)**: a data processing architecture that has <u>stream system</u> to produce low-latency, inaccurate(either bcoz of approximation algorithm or the system itself does not provide correctness)/speculative results and <u>batch system</u> to provide eventual correct results;

> Some links:
1. [How to beat the CAP theorem](http://nathanmarz.com/blog/how-to-beat-the-cap-theorem.html)
2. [Questioning the Lambda Architecture](https://www.oreilly.com/radar/questioning-the-lambda-architecture/)


The reason that the Lambda Architecture is successful is it could actually provide some good results even though the correctness is a bit of letdown; However, it is a lot of work to <u>maintain two independent versions of pipeline and merge the results in the end</u>; 

[Some people](https://www.oreilly.com/radar/questioning-the-lambda-architecture/) argue against the <u>necessity of dual-mode execution</u> because of the issue of repeatability of using a replayable system(like [Kafka](https://kafka.apache.org/10/documentation/streams/core-concepts.html#streams_topology)) so they propose the [Kappa Architecture](https://hazelcast.com/glossary/kappa-architecture/), which runs a single pipeline using a well designed & built system(like [Apache Flink](https://flink.apache.org/));

Lambda Architecture  |  Kappa Architecture
:-------------------------:|:-------------------------:
![lambda](/assets/images/post/ss/lambda.jpg)  |  ![kappa](/assets/images/post/ss/kappa.jpg)

### Lambda vs Kappa Architecture

Usually if the real-time algorithm and batch algorithm have different outputs, meaning batch & real-time layers cannot be merged, then must use Lambda Architecture;

> TBC

### Batch vs Streaming Efficiency 

- **Batch**: high-latency, higher-efficiency;
- **Streaming**: low-latency, lower-efficiency;

But for streaming system to achieve the same performance of batch systems, we only need to focus on 2 things:
1. **correctness**: because <u>strong consistency</u> is required for <u>exactly-once processing</u>, which is required for <u>correctness</u>, which is requirerd to meet batch system's level of performance.  (ref: [Why local state is a fundamental primitive in stream processing](https://www.oreilly.com/content/why-local-state-is-a-fundamental-primitive-in-stream-processing/))
2. **tools for reasoning about time**: essential for dealing with unbounded, unordered data of varying time skew;

### Event Time vs Processing Time

---|   Event Time  |  Processing Time
:-------------------------:| -------| :-------------------------:
Definition | the time at which events actually occured | the time at which events are observed in the system 

Some variables that can affect the skew between event time and processing time;
- shared recource limitations like network congestion, network partitions, shared CPU, etc;
- software causes like distributed system logic, contention, etc;
- features of the data like distribution, variance in throughput, variance in disorder;

<img src="/assets/images/post/ss/event-process.png" alt="event-process" width="350"/>

Because the overall mapping between event time and processing time is not static(the lag/skew can vary arbitraily over time), we cannot analyze data soely by the observed time;

To cope with such unfortunate design for unbounded data of many systems, we implement the windowing of the incoming data, meaning chopping up a dataset into finite pieces along temporal boundaries; 

### Data Processing Patterns

#### Bounded Data

pretty straightforward, run the dataset through some data processing engine to get a strcutured dataset with greater inherent value;

#### Unbounded Data

##### Fixed windows

Most common way, repeatedly run a batch engine to process input data which is windowed into fixed-size windows(separate data source, sometimes called tumbling windows);

##### Sessions



# Reference

- [Picture for Lambda Architecture & Kappa Architecture](https://www.ericsson.com/en/blog/2015/11/data-processing-architectures--lambda-and-kappa)
- [Lightweight Asynchronous Snapshots for Distributed Dataflows](https://arxiv.org/pdf/1506.08603.pdf)
- [Questioning the Lambda Architecture](https://www.oreilly.com/radar/questioning-the-lambda-architecture/)