---
layout: single
title: "Design patterns in systems with limited memory"
author: "Danni"
toc: true
lang: en
tags:
  - System Design
  - Distributed System
---

> Reading Small Memory Software: Patterns for systems with limited memory

2020 so far has been a train wreck. Without any classes on campus, I did manage to spend some time focusing on learning design pattern in computer software and systems in general: a goal I set a year ago but have never had time to finish.

**Small Memory Software** is a classic for those wishing to learn more on system design patterns & memory efficiency. Its first version was published around 2000s. However, though the memory capcity engineers are familiar with has changed greatly, there are still many principles applicable and useful for any software development that relies on the efficient use of memory and other resources. I first discovered this book through online forum while some senior engineers recommened as "at least it's worthwhile to read a few chapters to know if you have seen the same memory constraints in the past". I was quite skeptical in the beginning since the book seems nothing special and pretty old for materials in the tech industry. So I read the [introduction](http://smallmemory.com/1_IntroductionChapter.pdf) before making any purchases. It was a fun experience, espcially for those who have had similar issues: It was like a series of "yeah same" moments linked together and made coherent. Few hours into reading, I bought the physical book.

### Why still read this book?

So first, why we still should read this book? Computer memory used to be expensive, but now the company, even the individuals can easily afford a model with good memory. But as the computing power of mobile devices advances, it is not uncommon to rely more on our cellphones with a huge number of applications. The need for developers of such applications to support large request amount increases beyong imagination. So, yes, small memory softwares are back. 

In this post we will be fousing on key components like **RAM**, **ROM** and **secondary storage**. Of course there are other constraints like network, processing power, graphics that can slow down the entire system in real life but there have been more patterns in detecting paramsters mentioned before.

### Small Archietecture


|   |  **Embedded Systems** | **Mobile devices** | **PC** | **server farms** | 
| ------------- |:-------------:| :-------------:|:-------------:|-----:|
| **typical applications** | Device control, protocol conversion, etc | Diary, Address book, Phone, Email |Word processing spreadsheet, small database,accounting. |E-commerce, large database applications, accounting, stock control.|
| **UI** | NA | GUI; libraries in ROM | GUI, with several possible libraries as DLLs on disk | Implemented by clients, browsers or terminals |
| **Network** | None, Serial Connection, or industrial LAN | TCP/IP over a wireless connection | 10MBps LAN | 100 MBps LAN | 
| **IO**| As needed – often the main purpose of device. | Serial connections | Serial & parallel ports, modem, etc. |  Any, accessed via LAN  |

### Allocations

> pdf: [allocations](http://smallmemory.com/6_AllocationChapter.pdf)

##### Fragmentation

For dynamic memory allocation, there are two types: **internal fragmentation** and **external fragmentation**(And **Data Fragmentation**, as some would mention). The cause of such cases usueally happens when the user processes are loaded and removed from RAM, stored in blocks, making the main memory not enough for loading new process even though there are available memory spaces: small size memory blocks.

The memory would eventually run out, no matter which memory allocation scheme we choose, so what we could do for better memory management is to choose memory plan accordingly.

1. **Fixed size Client Memories**: makes user to take responsibility to take memory problems but harder to provide full features of the app, sometimes may result in lower user engagement rate.

2. **Signal an error**: It is quite easy to inform the client side of the error but it is more important to handle the error correctly, think about the parital failure pattern. This approach usually gives us more options to handle memory problems.

3. **Reduce quality to reduce quantity**: reduce quality can maintain the system throughput. One popular example is reduceing the quality of the image we store, or reducing sampling frequency.

4. **Delete old objects**: This is known as common practice. For example, if you load your pics for far too long in Instgram, most likely you would refresh or reopen the app, which means *Fresh Work Before Stable*: terminating old connections that have lower chance to be answered, delete old ones for new objects to arrive. 

5. **Defer new requests**/ **IGNORE**.

![Allocation Patterns](/img/post/small_mem/ALLOCATION.png)

> I will be updating this post regularly till I finish the whole book. If you have any questions, feel free to discuss in the comment section below.