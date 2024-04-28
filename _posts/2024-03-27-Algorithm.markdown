---
layout: post
title:  "Algorithm"
date:   2024-04-27 08:55:25
categories: Algorithm
---

# The time complexity of the algorithm
* Call function recursively. Example 1:
```Python
def func(n):
    if n <= 1: return n

    return func(n - 1) + func(n - 1)

counts = {
    '0': 1,
    '1': 1,
    '2': 2,
    '3': 2 * 2,
    '4': 2 * 2 * 2,
}
```
So for this example, the time complexity of the algorithm is 2^n or 2**n (exponent, exponential).

* Call function recursively. Example 2:
```Python
def func(n):
    if n <= 1: return n

    return func(n // 2)

counts = {
    '0': 0,
    '1': 1,
    '2': 2,
    '3': 2,
    '4': 2 + 1,
    '5': 2 + 1,
    '6': 2 + 1,
    '7': 2 + 1,
    '8': 2 + 1 + 1,
    '9': 2 + 1 + 1,
    '10': 2 + 1 + 1,
    '11': 2 + 1 + 1,
    '12': 2 + 1 + 1,
    '13': 2 + 1 + 1,
    '14': 2 + 1 + 1,
    '15': 2 + 1 + 1,
    '16': 2 + 1 + 1 + 1,
    '17': 2 + 1 + 1 + 1,
}
```
So for this example, the time complexity of the algorithm is log_2(n) 以2为底n的对数 (logarithm, logarithmic).
