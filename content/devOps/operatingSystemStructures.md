---
title: "Operating System Structures"
date: 2018-11-13
slug: "operatingSystemStructures"
description: "Operating System Structures"
keywords: ["operating system"]
draft: false
#tags: ["operating system"]
math: false
toc: true
---


{{< youtube UxCiuctkKYo >}}

The Operating System (OS) provides a programming interface to hardware services such as Input/Output (I/O) and the CPU.  This programming interface is typically written in a  high-level language (C, C++, etc).

Below are notes based on a Computer Science lecture found on YouTube – Lecture 3: Operating System Structures.  It is from Computer Science 377 from the University of Massachusetts.  Many thanks to Sean Barker (the lecturer) for his excellent work.

## System Calls

The operating system is mostly accessed by programs via a high-level Application Program Interface (API) rather than direct system call use.  The three most common operating system APIs are:

1. `Win32` API for Windows
2. `POSIX` API for POSIX-based systems (UNIX, Linux, and MAC OS)
3. Java API for the Java Virtual Machine

Programming to an API is safer and more convenient for the programmer, while also increasing the portability of the code.

## System Call Implementation

There is usually a number associated with each system call. The System-Call Interface maintains a table that is indexed according to these numbers. The System-Call Interface invokes the intended system call in the operating system’s kernel and returns the status of the system call and any return values. The caller does not need to know how the system call is implemented.

One of the great advantages of programming to an API is that the programmer does not need to know about how the system call is implemented. The programmer simply needs to obey API functions and understand what the operating system will do as a result. Most details of the operating system interface are hidden from the programmer by the API. The API is managed by a run-time support library (a set of functions built into libraries included with the compiler).
System Call Parameter Passing

Often, more information is required than the simple identity of the desired system call.  This information is usually passed to the system call as a parameter.  The exact type and amount of information vary according to the operating system’s API and the system call being employed. There are three (3) general methods used to pass parameters to the operating system:

1. Pass the parameters in registers (simplest).  This works well, but in some cases, there may be more parameters to be passed than registers available.
2. Parameters can be stored in a block (or a table) in memory, and the address of the block is passed as a parameter in a register.  This is the approach that is taken by Linux and Solaris. A layered operating system is not used in practice, it has several flaws which render it mostly theoretical; in practice, a layered OS would not be efficient, it could run out of physical memory, and have other problems.s.
3. Parameters can be placed, or pushed, onto the stack by the program and popped off the stack by the operating system.

It should be noted that the second and third methods, block and stack, do not limit the number or length of parameters being passed.

## Basic Operating System Structures

The kernel is the protected part of the operating system that runs in kernel mode and protects the critical operating system data structures and device registers from user programs.

There is an ongoing debate about what functionality belongs in the kernel.  For example, is a web-browser part of the kernel or a user program? The general consensus is that a web-browser is a user program, but Microsoft includes Internet Edge (or Internet Explorer) as part of its Windows operating systems.

## Layered Operating System Design

Another approach to operating system design is the layered approach.  Each layer of the kernel uses the layer below it to build the overall structure, with each layer providing new functionality to the kernel.  Central to the design of this approach is that each layer will only talk to the layer above and below it.

The advantages of the layered approach are that it is simple and modular – there are only two (2) possible communications points.  This makes it easier to debug for the designer, as there is only one layer to debug at a time.

The disadvantages of the layered operating system design are that it causes extra overhead because the layers are forced to convey messages to each other whenever the two communicating are not next to one another (pure overhead).  A layered operating system is only theoretical, in practice, it is highly unlikely to be efficient. For example, it is likely that a layered operating system would run out of physical memory. It is worth noting that a layered operating system is very similar to the design of the network stack.

## Microkernel

A microkernel is an operating system design that concentrates on having the smallest kernel possible.  It provides communication (message passing) and other basic functionality only.  Other operating system functionality is implemented as user-space processes.  A microkernel may be more resilient to crashes because most of the system processes are outside of the microkernel.  It could also be more secure than other designs.

Communication can be a problem because each system process has to send messages through the kernel to other system processes. This causes very high processing overhead, making it a microkernel operating system design inefficient.

### Microkernel Features

The goal of a microkernel is to minimize its size by implementing as much of the operating system in user-level processes as possible.  The advantages of this strategy include better reliability.

A layered operating system is not used in practice, it has several flaws which render it mostly theoretical; in practice, a layered OS would not be efficient, it could run out of physical memory, and have other problems. Unfortunately, microkernels suffer mediocre performance. The first microkernel was Hydra (Carnegie Mellon University [CMU] 1970). More modern operating systems that use a microkernel include Chorus (France) and Mach (CMU).

## Modular Operating System Design

Most modern operating systems implement kernel modules in their structure. Modules take advantage of object-oriented principles.  Each core component is in a separate module. Each module talks to other modules over known interfaces.  Modules are loaded into the kernel as they are needed. Overall, the modular approach has most of the advantages of a layered structure but is more flexible.

## Summary

Operating System designers are always asking themselves “How do we make the operating system more efficient, more reliable and more extensible?”

The design and implementation of an operating system involve constant tradeoffs between simplicity and performance.  As a general rule, designers strive for simplicity except when there is a strong reason to make a particular component more complicated in order to achieve acceptable performance.
