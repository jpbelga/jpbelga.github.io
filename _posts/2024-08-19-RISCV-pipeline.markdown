---
layout: post
title: "Building a Simple Scalar Multicycle RISC-V Processor"
date: 2024-08-19
categories: [Computer Architecture, RISC-V]
author: João Pedro Belga
---

## Introduction

In my previous post, I might have been overly optimistic. That optimism forked into two different paths—one that brought a sense of accomplishment and another that led to some stormy realizations.

### First Path: The Onur Mutlu Lectures

I definitely want to meet this sir one day. Good content still available in the internet, you just need to dig a deeper hole, but they are there. The pole in one of the best universities of the world is not in vain, this man redeemed it. Not only related with wisdom, but knowing how to pass it forward.

### Second Path: Am I Really Willing to Write So Much Code?

As I delved into the lectures, the concepts begun to get solid in my mind, and I kept questioning me what that I implement that would not be a waste of time. The ease of the quests that I firstly was chasing aren't the same, but the overall prizes are. What I'm trying to say is, after I learned what is an OoO Superscalar processor my will of implementing it just faded away. To much complexity for a blog post that only serves to as a future flashback of me spending hours writing Verilog code. I really prefer to spend this time expanding my knowledge, even in Computer Architecture. Don't want to say that practicing isn't worth, just saying that I have to spend my time wiser. 

### The Conclusion: How Will I Practice Then?

The answer is simple—by keeping it easier. Let's focus on practicing tooling and doing more than just writing code.

## The New Project: A Simple Scalar Multicycle RISC-V with a Simple Branch Prediction (PC = PC+4 Always)

Now, let's dive into the architecture of this simple scalar multicycle RISC-V processor. The figure below illustrates the proposed design, which is a five-stage pipelined processor. However, the focus here is on the simplicity of the design, suitable for someone who wants to understand and practice without diving too deep into complex implementations like Out-of-Order execution.

### Architecture Overview

![RISC-V Processor Architecture](/assets/DataPath.png)

The architecture follows the standard five stages of a pipeline:

1. **Instruction Fetch (IF):** 
   - The Program Counter (PC) starts by fetching the instruction from memory. The PC is updated by 4 after each fetch, following the simple branch prediction strategy (PC = PC + 4). 
   - This stage outputs the instruction to the next stage and prepares the next PC value for the following instruction.

2. **Instruction Decode (ID):**
   - The fetched instruction is decoded here. 
   - The instruction’s opcode, function codes, and operands are extracted, and the control signals required for subsequent stages are generated. 
   - Simultaneously, the register file is accessed to read the source registers' data. The Immediate Generator also computes the immediate values used in operations.

3. **Execution (EX):**
   - In this stage, the ALU performs arithmetic or logic operations based on the control signals generated during the ID stage. 
   - The result is forwarded to either the memory stage (if it’s a load/store instruction) or directly to the write-back stage (if it’s an arithmetic operation). 
   - For branches, the ALU determines the branch target address, and the PC is updated accordingly if the branch is taken.

4. **Memory Access (MEM):**
   - If the instruction involves a memory operation (load/store), this stage interacts with data memory.
   - The address calculated in the EX stage is used to read from or write to memory. The data is then forwarded to the next stage.

5. **Write-Back (WB):**
   - The final stage writes the result back into the register file. This could be the result of an ALU operation, data loaded from memory, or any other operation that updates a register.

### Pipeline Hazards and Solutions

To handle hazards, two essential units are incorporated:

- **Hazard Detection Unit:** Ensures that the pipeline doesn’t execute instructions that depend on the results of previous ones that haven’t been completed yet. If a hazard is detected, the pipeline is stalled by inserting a no-operation (NOP) instruction.
  
- **Forwarding Unit:** This unit reduces the performance impact of data hazards by forwarding the results from later stages of the pipeline to earlier stages that require them.

### Branch Prediction

The branch prediction strategy here is minimalistic—always assume that the next instruction is at PC + 4, regardless of the actual outcome of the branch. This is a straightforward approach that simplifies the implementation, but it also means that every branch misprediction results in a pipeline flush and a restart from the correct address, which could impact performance. However, this trade-off is acceptable in a learning context where simplicity is more valuable than peak efficiency.

### Conclusion

This simplified scalar multicycle RISC-V processor, combined with a straightforward branch prediction mechanism, strikes a balance between being manageable to implement and complex enough to provide meaningful practice. It’s a good starting point for anyone interested in computer architecture, offering insights into the functioning of processors while keeping the implementation challenges within reasonable bounds.
