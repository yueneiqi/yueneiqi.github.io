---
layout: post
title: "【论文笔记】Polynesia: Enabling High-Performance and Energy-Efficient Hybrid Transactional/Analytical Databases with Hardware/Software Co-Design"
date: 2023-10-03
author: 玉树
categories: [DBMS]
tags: [Paper, HTAP, PIM]
comments: true
toc: true
---

## Brief

Nowadays, many applications need to perform real-time data analysis using an HTAP system. An ideal HTAP system should have three properties:

1. Data freshness and consistency.
2. Workload-specific optimization.
3. Performance isolation.

However, prior works cannot achieve all these properties. The paper divides the system into transactional and analytical processing islands, which enables workload-specific optimizations and performance isolation. It introduces `Polynesia`, a novel hardware/software cooperative design for in-memory HTAP databases. `Polynesia` implements custom algorithms and hardware to reduce the costs of data freshness and consistency. It also exploits PIM for analytical processing to alleviate data movement. In result, `Polynesia` has average transactional/analytical throughput improvements of 1.7x/3.7x and 48% reduction on energy consumption, compared to three state-of-the-art HTAP systems.

## Design and Evaluation

The key idea of `Polynesia` is partition computing resources into two types of isolated and specialized processing islands. By isolating transactional islands from analytical islands, `Polynesia` apply workload-specific optimizations to each island, avoid high main memory contention, and design efficient data freshness and consistency mechanism without incurring high data movement costs.

![Problems and Goal](https://s2.loli.net/2024/02/25/GKTAEStz3amvBiW.png)

![High-level organization of Polynesia
hardware](https://s2.loli.net/2024/02/25/pQlWUgw5iGZVBKF.png)

![Analytical Engine: Data Placement](https://s2.loli.net/2024/02/25/jiVbfPya8IKpMcl.png)

![System Analysis](https://s2.loli.net/2024/02/25/KblFhZgLiOGjezc.png)

## Reference

- Boroumand, A., Ghose, S., Oliveira, G.F., Mutlu, O., 2022. Polynesia: Enabling High-Performance and Energy-Efficient Hybrid Transactional/Analytical Databases with Hardware/Software Co-Design, in: 2022 IEEE 38th International Conference on Data Engineering (ICDE). IEEE, pp. 2997–3011. [[paper]](https://ghose.web.illinois.edu/papers/22icde_polynesia.pdf)[[video1-short]](https://www.youtube.com/watch?v=Ua1pZwBRat0)[[video2-long]](https://www.youtube.com/watch?v=3IHmaDjtWcE)
