---
title: "No Means No, But Yes Means Maybe: Bloom Filters"
description: "Because sometimes being probably right is better than definitely slow."
publishDate: "2026-03-31"
tags: ["algorithm", "computer-science", "system-design", "technology"]
---

*Because sometimes being probably right is better than definitely slow*

Bloom filters are the probabilistic data structures used to check whether an element is in a set, in a space efficient way. It allows us to represent very large sets of elements in a comparatively small amount of memory typically a few *bits* per element.

If bloom filters returns YES – element **might** be in the set (Probabilistic Yes)

If bloom filters returns NO – element is **definitely** not in the set (Deterministic No)

## How Bloom Filters Work

Bloom filter data structure is a bit array of length n. All the bits will be set to zero when its initialized. It stores only the bits instead of the value of the elements.

## Adding Items

To add item into the bloom filter, we hash it with *m* different hash functions and take the modulo n (length of array) to identify the indexes which will be set to 1.

```
h1(book) mod 9 = 0
h2(book) mod 9 = 4
h3(book) mod 9 = 8

h1(pen) mod 9 = 2
h2(pen) mod 9 = 4
h3(pen) mod 9 = 7
```

## Checking Existence of Item in a Set

- To check whether an item belongs to set, we hash it with same *m* hash functions and take the modulo *n* to get the index values.
- If all the bits at these indexes are set to 1, we conclude that the item probably present in the set.
- If all the bits at identified indexes are set to 0, we deterministically say that the item is not present in the set.

## False Positives

Consider element "paper":

```
h1(paper) mod 9 = 0
h2(paper) mod 9 = 4
h3(paper) mod 9 = 7
```

We check the indexes 0, 4 and 7. These indexes are set to 1, so we conclude that element "paper" probably exist in the set, though we never added it in the set.

## Advantages

- Constant time and space complexity
- No false negatives
- Enables privacy by not storing actual items
- Parallelizable operations

## Limitations

- Doesn't natively support delete operation
- Returns false positives occasionally

## Use Cases

- Quick lookup to reduce disk access for non existent keys
- Checking whether a username is taken
- Identifying malicious urls, fraudulent transactions
- Filtering out previously shown posts in recommendation engines
- Log-structured merge tree storage engine used in databases such as Cassandra uses a bloom filter to check if the key exists in the SSTable
- Map Reduce uses the bloom filter for the efficient processing of large datasets

## Conclusion

Bloom filters offer constant time and space complexity for lookups with trade-off of yielding probabilistic result.