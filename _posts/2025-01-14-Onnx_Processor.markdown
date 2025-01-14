---
layout: post
title: "Transforming Onnx into Harwdware"
date: 2025-01-14
categories: [Computer Architecture, RISC-V]
author: João Pedro Belga
---

# Motivation

A few days ago, while re-inspecting our results in the Cartesi environment, I was investigating why the ONNX model fails to work on RISC-V environments. We identified the root cause, as described in this [issue](https://github.com/microsoft/onnxruntime/issues/22530): the kernel was improperly implemented for the RISC-V environment.

# Initial Findings

Before attempting any fixes, I needed to understand how an `.onnx` file operates. It essentially describes a computational graph where each node represents an operation. These operations are not basic but involve advanced linear algebra computations, such as convolutions and matrix multiplications.

As I delved deeper, my thoughts kept returning to Tomasulo's Algorithm, a concept I had studied during a computer architecture course last year.

# Tomasulo's Algorithm

Tomasulo's Algorithm is one of the most significant advancements in modern computation. It enabled out-of-order execution, a cornerstone of modern processors. The algorithm dynamically schedules instructions based on data dependencies, resolving hazards and optimizing execution pipelines.

Its core mechanisms include:
- Reservation stations for managing operations and operands.
- Register renaming to avoid data hazards.
- Dynamic instruction dispatch to improve parallelism and resource utilization.

# Application to ONNX and ML Execution

This algorithm's principles can be adapted to machine learning execution by generalizing ML operations into specialized linear algebraic kernels. The goal is to eliminate the need for manual HDL coding or HLS tools by enabling the design of ML models in high-level frameworks like PyTorch. These models could then be compiled into HDL and implemented as IP cores in hardware projects.

This ambitious goal can be broken down into manageable steps, starting with implementing basic kernels and activation functions.

# Next Steps

While refreshing my linear algebra knowledge, I realized that most operations could be reduced to a matrix multiplication kernel combined with efficient memory management. The challenge now lies in devising an optimal execution strategy for these kernels.

# Kernel Design Concept

Inspired by the potential of systolic arrays, I envisioned an architecture featuring a processing element (PE) array, where data flows through a queue implemented as a shift-register chain. This design enables efficient pipelined computation.

However, after further research, I discovered that such architectures already exist in the form of Tensor Processing Units (TPUs). TPUs use systolic arrays where each PE performs a multiply-accumulate (MAC) operation. While this validates my initial approach, TPUs are specialized for ML workloads and operate as PE kernels rather than Von Neumann machines.

The challenge now is to merge these paradigms, creating a kernel-based architecture that integrates seamlessly with an instruction set architecture. This hybrid design would enable ML-specific computations with less area consumption, bridging the gap between high-level design and low-level hardware implementation.

# Moving Forward

To bring this concept to life, I will:
1. Develop basic linear algebra kernels and activation functions.
2. Design a systolic array prototype and validate its functionality.
3. Investigate memory management techniques to optimize data flow and utilization.
4. Explore integration with RISC-V or similar ISAs to enable hybrid operation modes.

This project is undoubtedly ambitious, but it has the potential to redefine how ML models are translated into hardware, offering a scalable and efficient path for hardware-accelerated AI computation.

