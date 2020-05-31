---
layout: single
title: "Hash-flooding DoS"
author: "Danni"
toc: true
toc_sticky: true
tags:
  - Security
---

> Why, how to prevent, application in Java?

Hash-flooding attack is one of the most important attacks in cyber security -- also an intuitive way to distinguish the ways software engineers and security engineers think.

## Why Hash-flooding Attack ?

Hash-flooding Attack is `Denial of Service` attack. Once the backend interface has any vulnerability, the whole server will be out of service.

What is "vulnerability" we are talking about?

We have to mention `Hash`, the simple data structure with which almost every software is implimented. People choose it because it has super fast lookup time: `O(1)` average and `O(n)` in worst case.

> **Hash tables suffer from O(n) worst time complexity due to two reasons:** <br><br>1. If too many elements were hashed into the same key: looking inside this key may take O(n) time.<br>2. Once a hash table has passed its load balance - it has to rehash [create a new bigger table, and re-insert each element to the table].

That means, when we want to constantly insert `n` elements in the hash table, if the key does not hae same hash value, it will take around `O(n)` time; but if the key's hash values constatntly conlide, **worst case will take around `O(n^2)` time**, which makes the avrage running time and worst case running time very different from each other.

That's why Scott A. Crosby and Dan S. Wallach proposed an idea in 2003 that, if there exists a way to make use of the vulnerability of such data structure (the worst case complexity) to create worst case senario manually.


## java.lang.String.hashCode()

This method returns a hash code for this string. The hash code for a String object is computed as:

`s[0]*31^(n - 1) + s[1]*31^(n - 2) + ... + s[n - 1]`

Using int arithmetic, where s[i] is the ith character of the string, n is the length of the string, and ^ indicates exponentiation. (The hash value of the empty string is zero.)
> ![Hash](/assets/images/post/hash/hash.jpg)

Using this algorithm, we can easily create a set of strings that have the same hash value. Once given to the server to make the hash table, the server will be down using such cheap method.

> A experiment in 2011 indicated that, attacking a Tomcat server will only cost `6KB/s` to paralize an Intel i7 processor; `1GB/s` to paralize 100000 intel i7 processor; which makes hash-flooding attack a more ecnomically efficient way to do DoS attack.

## How to prevent ?

Now we have already established condition for attackers to do hash-flooding attack: know <u>all the details of algorithms</u> so they can easily make a set of conflicting keys.

So what if we make algorithm not completely available to attackers?

Now we can introduce `Hash Seed`, a secrecr parameter generated randomly each time a hash table is created. Hash algorithms using this type of Hash seed is called `Keyed Hash Function`.

As to date, many research facilities and organizations have designed new hash functions like: `SipHash`, `MurmurHash`, `CityHash` ...

## CTF: Hash Collisions

In CTF, hash collision is when two pieces of data or text have the same cryptographic hash. This is very rare. What’s significant about collisions is that they can be used to crack password hashes. Passwords are usually stored as hashes on a computer, since it’s hard to get the passwords from hashes.

## References

[1] Scott A. Crosby and Dan S. Wallach. 2003. Denial of service via algorithmic complexity attacks. In Proceedings of the 12th conference on USENIX Security Symposium - Volume 12 (SSYM'03), Vol. 12. USENIX Association, Berkeley, CA, USA, 3-3.

[2] !(Hash table runtime complexity (insert, search and delete))https://stackoverflow.com/questions/9214353/hash-table-runtime-complexity-insert-search-and-delete