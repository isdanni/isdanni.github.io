---
layout: single
title: "Clojure in Java Developers' eyes & Lisp?"
author: "Danni"
toc: true
toc_sticky: true
tags:
  - Lisp
  - Java
  - Clojure
---

> Is it easier to learn Clojure after Java & How GC/VM tuning helped

# Why I decided to learn Clojure

Some time ago I came across a [really old blog](https://www.infoq.com/articles/lisp-for-jvm/) online, in the comments someone surprisingly suggested some amazing features from another language. And, no suprise, my first encounter with Clojure. Later, one developer I followed online did a really thorough talk on "Why we should still learn Lisp and why you should learn Clojure". She made the language seems the best way to escape certain boring features some popular languages like Java have, so I thought, why not give it try? 

The first time I tried the tutorial & Clojure syntax, I was like "Da F* is this" because its syntax is too weird, especially for someone used to sequential programming. But few days later I crawled back cuz I couldn't stand being beaten at the first try. And my learning journey starts since. 

# How I fell in love

One thing I love about certain programming lanuages is the speed. How fast they compile, how fast they execute, etc. That's also one of the reasons I decided to learn [GO](https://golang.org/). Because having worked as a Python & Django developer for some time(It's too easy to not use :)) one naturally values the efficiency of developing sprint and tends to avoid burnout.

I kinda wander around [Scala](https://www.scala-lang.org), Java and Go for a while based only on abstractions. However, whenever I write Scala it still feels like I'm writing Java. Wiritng GO is much better so I'm stil doing that but eventually I fell in love with Clojure. It might start a bit slow because there are many classes to load but once loading finished, the speed is relatively great. Someone might say Ruby is also great for fast development but if improving the projects in Ruby to Clojure it will have much better performance

Also, Lisp is so old that it almost have any features modern languages nowsdays have. Functional programming languag is great because of its immutable data structures, targeting at JS & .NET. And since it's really really OLD, the Clojure community is incredibly welcoming and loves to see the growth so almost everyone(or just everyone) would be willing to help late-comers.

# Java & JVM

It's ok to hate Java but love JVM. Because I do. And many other developers, even the person who invented Clojure himself, do.  

JVM is the best thing that helped programmers to build software in Java. But when certain memory leaks happen, we still need to tune it little bit(even tho it rarely happens nowadays). When we have GC problem(Cuz I had so many times lol), it is usually because the wrong tuning in JVM or simply some logic bugs in your programs. 

#### JVM Tuning best practice

> ref: [article](https://backstage.forgerock.com/knowledge/kb/article/a35746010)

1. Establish a baseline using a valid testing process;

2. Documente each change;

3. Use `CMS GC`;

4. Total `heap size` cannot exceed available physical RAM size;

5. Use GC logs;

```java
25.466: [Full GC )25.466: [CMS: 68756K­>75361K(1987392K), 0.2548820 secs] 96571K­->75361K(2064064K), [CMS Perm : 61069K­>60722K(61376K)], 0.2551850 secs] [Times: user=0.25 sys=0.01, real=0.25 secs]
26.185: [Full GC (System.gc())26.185: [CMS: 75361K­>73283K(1987392K), 0.2398450 secs] 76136K-­>73283K(2064064K), [CMS Perm : 60723K­>60723K(101204K)], 0.2400120 secs] [Times: user=0.24 sys=0.00, real=0.24 secs]
27.293: [Full GC (System.gc())27.293: [CMS: 73283K­>73291K(1987392K), 0.2111960 secs] 74096K­>73291K(2064064K), [CMS Perm : 60723K­>60723K(101396K)], 0.2112730 secs] [Times: user=0.21 sys=0.00, real=0.22 secs]
68.081: [Full GC (Heap Inspection Initiated GC)68.081: [CMS: 73291K­->73187K(1987392K), 0.2172670 secs] 77634K­>73187K(2064064K), [CMS Perm : 60742K­>60742K(101396K)], 0.2174520 secs] [Times: user=0.22 sys=0.00, real=0.21 secs]

```

#### Clojure Tuning Tips

After practies with JVM, it's much easier to adopt some Clojure tuning methods: 

1. Concurrency helps?

2. Minimise memory allocation;
    - Do **NOT** abuse using lists, maps, non-primitive numbers, etc;
    - Extra overhead can result in longer GC pauses;

3. Go back to program algorithms if nothing works;

Also, I would like to recommend a book, really old, but I just started because it's too amazing to miss. 👏👏👏

![Small Memory Software - Patterns for Systems with limited memory](/assets/images/post/clojure/Small-Memory-Software.jpg)


As Rich Hickey once said in a talk - ["An INtroduction for Lisp Programmers"](https://github.com/matthiasn/talk-transcripts/blob/master/Hickey_Rich/ClojureIntroForLispProgrammers.md):

> So why the JVM? Well, it is coming around that, just like you would say, "I want this language and I want it to talk to this operating system. I will port it to these operating systems as targets", virtual machines are targets. In fact, they are the new targets. They are the new platforms. Just like you would say, "Oh, Lisp is a platform" "Unix is a platform".

> For businesses, these VMs are platforms, because they abstract away platforms, or what we used to call platforms: the hardware, the instruction set, the CPUs, no one wants to deal with that. No one wants to deal with the OS level stuff any more.

> So we have now got another layer of abstraction, which are these virtual machines. The two big ones are .NET and Java. And they provide a lot of facilities, more than just, "how do you access the file system?", including type systems. What is interesting, and surprising a little bit, is that the type systems in these VMs are dynamic. They have dynamic enforcement, dynamic creation of types. They are very dynamic, even though the languages that typically target them, Java and C#, are not particularly dynamic.

> The big story, of course, is this huge set of libraries you get to tap into. All this code that other people wrote.

> They also provide a bunch of high level services like garbage collection and memory management, resource management, meaning that garbage collection is no longer a language problem. Garbage collection is a platform problem, which is good.

# Collections UML 

The return value of all sorts of functions like `coll, list, seq, sequential, counted...` is really confusing. The UML below is generated with intellij. 

> CR: [Loretta He](https://github.com/lorettahe)

![Clojure Collection UML](/assets/images/post/clojure_collection.jpg)
