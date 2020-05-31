---
layout: single
title: "Reservoir Sampling"
author: "Danni"
toc: true
toc_sticky: true
tags:
  - Distributed System
  - Algorithm
---

> How the algorithm works and its implementation in streaming system

## Algorithm

**Reservoir sampling** is a family of [randomized algorithms](https://en.wikipedia.org/wiki/Randomized_algorithm) for 

###### choose a simple [random sample] [without replacement of k items] from a population of [unknown size n] in a [single pass] over the items.

**NOTE**: 
- size n here usually cannot fit into [main memory](https://en.wikipedia.org/wiki/Main_memory); 
- n is unknown, revealed over the time; otherwise it would be too easy;
- time complexity required is `O(N)`;
- probability of each item being chosen must be `k/n`;

###### Simple Algorithm


The commonly used algotihm that is **simple but slow**, known as: [Algorithm R](http://www.cs.umd.edu/~samir/498/vitter.pdf);

 

```java

/**
 * Algorithm R works by maintaining a reservoir size of k, 
 * 1. which initially contains the first k items of the input;
 * then iterates over the remaining items until the input is exhausted.
 * 2. when reaches the ith item
 *   a. if i >= k, random choose d in [0, i]
 *      if d is within [0, k -1], use ith item to replace dth item in reservoir; 
 * 3. repeat second step;
 */

int[] reservoir = new int[k];

for (int i = 0; i < reservoir.length; i++)
{
    reservoir[i] = dataStream[i];
}

for (int i = k; i < dataStream.length; i++)
{
    // random integer in [0, i];
    int d = rand.nextInt(i + 1);
    // if integer is within [0, m-1]，then replace reservoir
    if (d < k)
    {
        reservoir[d] = dataStream[i];
    }
}
```

**CONS**: [asymptotic running time](https://en.wikipedia.org/wiki/Big_O_notation) is thus `O(n)`, which causes the algorithm to be unnecessarily slow if the input population is large.

## Distributed/Parallel Reservoir Sampling

In idistributed systems, **main memory & IO ops** would be the bottleneck; So for the data of a super large scale, we could improve the overall performance  using parallel algorithm:


1. Assume we have `m` machines, divide the stream into `m` data stream, every single machine process one stream of m samples, then note down as `N1, N2, ..., Nk, ... NK`(assume m < Nk>) => N1 + N2 + N3 + ... + NK = N;
2. Choose a **random number** d from `[1, N]`:
    a. if d < N1, then replace (1/m) from the first machine, ...; repeat m times;

=> m / N

## Implementation

Because the reservoir sampling has **O(N) time complexity** and **O(M) space complexity**, it is usually adopted in streaming systems where statistical sampling is required. For example, random output n lines from a large-scale dataset;

##### Limitations

Reservoir sampling makes the assumption that the desired sample fits into main memory, often **implying that k is a constant independent of n**. 

> ref: wiki: [reservoir sampling#limitations](https://en.wikipedia.org/wiki/Reservoir_sampling#Limitations) 

In applications where we would like to select a large subset of the input list (say a third, i.e. `k=n/3`), other methods need to be adopted. Distributed implementations for this problem have been proposed.

## Reference

1. [Reservoir sampling](https://en.wikipedia.org/wiki/Reservoir_sampling);
2. [randomized algorithms](https://en.wikipedia.org/wiki/Randomized_algorithm);