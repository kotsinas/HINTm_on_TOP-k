# HINTm on TOP-k

An enhanced version of the HINTm interval index, designed to efficiently handle top-k queries.

## Overview

Welcome to the source code repository of my master thesis in the field of temporal data management. This project is a fork of an older version of HINTm, contains the implementation of the methods developed for my master thesis and focuses on answering Top-k queries over temporal data.

## Abstract

Today's data-driven world has made it essential to manage and analyze large volumes of temporal data efficiently. This thesis addresses the problem of identifying the top 𝑘 time intervals that best intersect with a query interval within a given temporal data domain. In pursuit of addressing this issue with maximal efficiency, we further develop the HINTm index to support ranking queries. 

HINTm, is a Hierarchical Index for Intervals in arbitrary domains designed for main memory and defines a hierarchical domain decomposition which assigns each interval to at most two partitions per level. It has previously been recognized as the most efficient interval index in the literature, has undergone numerous optimizations to avoid unnecessary comparisons and expedite range query responses over extensive collections of intervals. Building on its optimizations, this work adapted HINTm to effectively handle top 𝑘 queries.

The ranking criterion is defined by the absolute interval intersection, enabling the identification of intervals that intersect better with a given query interval. Except from the naive approach that simply traverses the index and scans its partitions for results, various methods were developed to prioritize partitions that contain larger intervals first. In reference, “Top-down”, “Depth-first”, “Ordered” and “Sorted” traversals aim to optimize the processing speed of top 𝑘 queries. Additionally, a pruning mechanism was implemented to bypass scanning index partitions that are guaranteed not to contain intervals of the final set. This pruning mechanism, termed "Upper bounds", was deployed in two distinct versions. The first version assigns a static Upper bound to each index partition based on the partition's endpoints. The second, an updated version, incorporates the metadata information of the maximum interval
within each partition. 

Extensive experiments were conducted on four datasets with varying characteristics, measuring the number of queries executed per second. These experiments aimed to
understand system scalability concerning different query extents and values of 𝑘. The results indicate that larger query extents and higher values of 𝑘 are associated
with reduced throughput. However, the application of the “Upper bounds” accelerates the overall process. Finally, metadata Upper bounds provide even better performance, always with respect to the diverse characteristics of datasets being utilized.

### Thesis pdf

[Ranking Queries over Range Data](https://olympias.lib.uoi.gr/jspui/bitstream/123456789/38152/1/Master%20Thesis%20Kotsinas.pdf)

### Original HINTm Repository

[HINT: A Hierarchical Index for Intervals in Main Memory](https://github.com/pbour/hint)

## Dependencies

To successfully compile and run the code, you need the following dependencies installed on your system:
- g++/gcc
- Boost Library

## Data

The `samples` directory includes the BIKES dataset used in experiments and a query file containing 10k queries. Ensure that these data files are in place before running the code.
- NYC-Bikes_2020_after1940_withBirthYear_Sven.dat
- BIKES_qe0.1%_qn10K.qry

## Compilation

To compile the code, use the following command:
```sh
make hint_m
```

## Parameters

The HINTm interval index includes numerous parameters that reference various functionalities. For detailed information on these functionalities, please visit [https://github.com/pbour/hint](https://github.com/pbour/hint) and [https://www.cs.uoi.gr/~nikos/sigmod22.pdf](https://www.cs.uoi.gr/~nikos/sigmod22.pdf). This section provides guidance on successfully executing top-k queries using HINTm.

- `-m` Parameter that defines the levels of the hierarchical index. The optimal `-m` value depends on the temporal domain being examined. 

- `-o` Parameter that defines the optimizations used during top-k query execution. In this version, this parameter is fixed to `-o subs+sort+ss+cm` for every execution.

- `-k` Specifies the number of top results to return for each query; the default value is 1.

- `-r` Specifies the number of runs per query; the default value is 1.

- `-q` Specifies the index traversal method. 
Options:
  - Naive: `-q gOVERLAPS_topk_naive`
  - Top-Down: `-q gOVERLAPS_topk_topdown`
  - Naive with upper bounds: `-q gOVERLAPS_topk_naiveb`
  - Top-Down with upper bounds: `-q gOVERLAPS_topk_topdownb`
  - Depth-First: `-q gOVERLAPS_topk_depthfirst`
  - Ordered: `-q gOVERLAPS_topk_orderedcl`
  - Sorted: `-q gOVERLAPS_topk_sortedcl`

## Execution 

The execution of `$ ./query_hint_m.exec` given the proper parameters and appling the data in the form that files have in the `samples` directory, finds for every interval query in the .qry file the k intervals of the .dat file that overlap better with it.

### Example

- For every query in `BIKES_qe0.1%_qn10K.qry` finds the 10 intervals in `NYC-Bikes_2020_after1940_withBirthYear_Sven.dat` with the greater intersection. Top-Down method with upper bounds is applied. 
```sh
$ ./query_hint_m.exec -m 16 -o subs+sort+ss+cm -k 10 -q gOVERLAPS_topk_topdownb samples/NYC-Bikes_2020_after1940_withBirthYear_Sven.dat samples/BIKES_qe0.1%_qn10K.qry
```

## Accuracy 

To ensure the accuracy of the results, a brute force method involving a nested double for-loop is employed. This highly naive approach identifies the top k intervals for each query and subsequently sums the top k overlaps for each query. The total sum of all overlaps must match the 'Total SUM result' of the final report displayed on the screen for every method and dataset tested, assuming identical parameters are applied in each test. This procedure guarantees the accuracy of the execution.

### -v

By applying the `-v`, verbose mode is activated, and the top k intersections are printed on the screen for each query. When running in this mode, the 'Total SUM result' will be 0.

## Static Upper Bounds

By default, the code uses metadata upper bounds. To run the program with static upper bounds, you need to uncomment the `STATIC BOUNDS` flag in def_global.h. After adjusting the flag, remember to use `make clean` and then recompile with `make hint_m`.

