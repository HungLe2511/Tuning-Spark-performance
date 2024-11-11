# Prerequisite
Using basic:
-  Python
-  pyspark

# Tuning PySpark

Tuning PySpark is the process of optimizing performance and reducing runtime for Spark jobs, especially beneficial when working with large datasets. PySpark tuning helps in utilizing resources efficiently, reducing operational costs, speeding up processing, and improving scalability. This project provides a guide to popular tuning methods for PySpark to achieve maximum performance.

## Table of Contents

1. [Introduction](#introduction)
2. [Memory Tuning](#memory-tuning)
   - Adjusting memory for executors and driver
   - Configuring memory settings and using memory management techniques
3. [Shuffle Tuning](#shuffle-tuning)
   - Reducing time and cost of shuffle operations
   - Adjusting number of partitions and optimizing shuffle operations
4. [Parallelism Tuning](#parallelism-tuning)
   - Increasing the number of concurrent tasks to fully utilize cluster resources
5. [Broadcasting](#broadcasting)
   - Optimizing performance with broadcast variables
6. [Data Serialization](#data-serialization)
   - Choosing efficient data formats and serialization techniques
7. [Caching and Persistence](#caching-and-persistence)
   - Using cache and persist functions to boost performance
8. [Troubleshooting and Debugging](#troubleshooting-and-debugging)
   - Tools and methods for monitoring and debugging
9. [References](#references)

## 1. Introduction

This project covers various methods for optimizing PySpark performance. PySpark tuning includes adjusting memory, optimizing shuffle operations, managing parallelism, and more. By tuning each aspect, we can enhance performance and scalability.

## 2. Memory Tuning

![Codespace](image/Capture.PNG)

In Spark, memory within each executor is divided into main components serving different purposes. Here is a detailed explanation of each memory component in Spark:

1. `Execution Memory`
   
Function: Used to store intermediate data during computation tasks such as join, sort, aggregation, and shuffle. When a task requires temporary storage for calculations, this data is held in Execution Memory.

Management: Execution Memory is dynamic and can share space with Storage Memory (thanks to Unified Memory). It will reduce the size of Storage Memory if additional memory is needed for computations.

Note: Lack of Execution Memory can cause “spilling” — data is temporarily written to disk, which can reduce performance.

3. `Storage Memory`
Function: Used to store cached (buffered) data, broadcast variables, and objects that need to be retained across tasks (such as cached DataFrames or RDDs). Storage Memory is crucial for applications that reuse the same data multiple times.
Management: Like Execution Memory, Storage Memory is also dynamic and can relinquish space to Execution Memory when necessary. If Storage Memory exceeds its capacity, Spark will evict or free up the least-used data.
Note: Ensuring sufficient Storage Memory for cached data helps prevent Spark from evicting data or moving it to disk, which can impact performance.
4. `Reserved Memory`
Function: Reserved specifically for Spark's internal tasks, such as storing system objects and managing executor information. Reserved Memory is typically quite small and not configurable, but it ensures that Spark always has enough memory for these internal needs.
Management: Reserved Memory does not share space with other memory blocks. The size of Reserved Memory is usually fixed and occupies a small portion of the total memory.
Note: This memory is not adjustable by the user, as it is crucial to Spark’s stability and for meeting its system requirements.
5. `Unified Memory Management`
Since Spark version 1.6, Unified Memory Management allows Storage Memory and Execution Memory to share the same memory pool. This increases the efficiency of Spark’s memory resources:

When Storage Memory is not fully used, the remaining portion can be allocated to Execution Memory and vice versa.
Memory can be split based on the parameters spark.memory.fraction (the total fraction used by Execution and Storage) and spark.memory.storageFraction (the ratio between Execution and Storage within this portion).
