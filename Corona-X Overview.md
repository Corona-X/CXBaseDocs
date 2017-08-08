> Corona-X Preliminary API A.1<br>
> Tyler Besselman<br>
> 8.8.2017<br>

[toc]

Introduction
============

Corona-X is a Next-Generation Operating System which I have been designing for about a year. The system itself is of my own design, although I would like to meet people who may provide some insight into possible flaws and improvements that can be seen and made. The design philosophy I have taken for the system is to first do an in-depth analysis of modern day as well as classical Operating Systems and their design. Then, I attempt to isolate the key features of the systems that work extremely well and take note of them. I then read research on any other theories I may not have already enumerated. Finally, to create the overall design, I synthesize a design which incorporates as many of the features which have been proven to work well as well as some of my own ideas into a single, seamless piece of software.

The kernel, which is the current focus of the project, blends contemporary and modern theory and designs for kernels with my own ideas on the effective operation of a system within the goals of the project. Other pieces of the system are specified in lesser detail; however, this paper will be addressing the kernel and privileged software level almost exclusively. Programming has been on and off since my Freshman year of high school. A large portion of the code in the current code base is from October and November of 2016, as the project was largely still in the researching stage until that point (Research is still ongoing, however, a large portion of the plan for the privileged level of software was complete enough to begin the implementation in October 2016).

Project Goals
=============

**Note**: These are listed in order of importance to the project.

1. **SRP:** Achieve extreme **Security**, **Reliability**, and **Performance** through proper design and optimization of code.
2. **Functionality:** Guarantee that all components of the System function as intended. Additionally, guarantee that all components of the System function together to create a single experience.
3. **Documentation:** Provide entirely accurate documentation of both intended and actual functionality of every aspect of the System.
4. **Code Clarity:** Generate a System codebase which conforms to the project's given Coding Standards (These are given externally). Ensure that code is clear in meaning and commented properly where necessary to enhance overall readability.
5. **Experiment:** Foster an environment in which risks can be taken; delve into unexplored areas of OS Development; change the world, and have fun with it!

Privileged Level Overview
=========================

The Kernel
----------

The Kernel of Corona-X is in large part an exokernel. System resources are simply protected and securely multiplexed. Solely at the kernel level, this is somewhat uninteresting, but the method through which the exokernel concept of 'libOSes' is extremely interesting.

The kernel implements libOSes in the microkernel structure of 'servers.' The latency in the microkernel message passing between servers is mitigated by two concepts: exokernel temporary shared regions and having a full contained system (or a large part of a system) inside each server.

Temporary shared regions are simply areas in memory which are shared between each process over the course of a message call. When one server messages another, a certain region in the memory space of the message-er can be shared for the duration of the call with the message-ee. This region can be of any size because the entire Virtual Memory context is not necessarily swapped out on a system call. The server providing the processing for the message can then write directly into the address space of the server which passed the initial message. This scheme cuts down on latency a great deal.

Another way the latency of message passing is cut down is in the method through which servers are given control of the system. On a message call, 3 things occur: the remote procedure's address is read, the current server's timeslice is forfeit to the remote server, and the remote procedure is called. Shared regions must be set up manually by the caller (to optimize cases where they are not utilized).

The second method of optimizing the performance of the system overall is the to merge some monolithic kernel ideas in. Statistically speaking, it has been found that the overwhelming majority of kernel bugs in monolithic or hybrid kernels (linux, BSD, XNU, NT) are in the driver layer. In a traditional microkernel, every piece of the system is split up and placed into a separate server. In the design of the Corona-X kernel, I have used these 2 facts to devise a system which should compartmentalize the majority of bugs and security faults while still performing near or at the level of a monolithic kernel. In this scheme, the "native" user system is split into 3 separate pieces: the Hardware (I/O) Server, the System Server, and the Fault Server. The Hardware and System Servers are each split further into 2 pieces. These servers are together referenced simply as the "Core System Layer." The function of each servers is discussed later.

The final thing to mention about the kernel is how disks, graphics processors, and network interfaces are handled. These resources are not managed as they would be in a full-fledged exokernel. All of these resources are owned by the Hardware Server, which acts as an exokernel itself, simply allocating these resources out to certain other servers. The reason for this is that the Hardware Server understands the semantics of accessing and dealing with these resources, which is necessary for proper allocation of them. Disks are split into partitions which can each be marked as 1 of 2 things: allocatable or fixed. Fixed partitions are allocated to and entirely owned by a single server. Allocatable partitions may have their blocks allocated to by any server in the same way as memory. Graphics Processors cannot be shared easily. This is inherent in their design; multiple programs cannot write conflicting pieces of data to a GPU at the same time and expect the correct results. The GPU is controlled by the Hardware Server. The Hardware Server operates as if it was an exokernel itself, allowing other servers direct access to the GPU. Network interfaces work in the same way. All of these functions may be extending by extending the hardware server.

The Core System Layer
---------------------

The Core System Layer is comprised of the main servers which provide the default user interface and API in Corona-X. While custom servers may be written, these servers are expected to be the main area under which developers and users operate. This layer of the system controls the vast majority of computing resources in the system at any given time.

###The Hardware (I/O) Server

This is the only server which by default understands Hardware Semantics. It is split into 2 pieces, one of which is user extendable. This server acts as an exokernel for certain system resources.

All hardware accessed in the Core System Layer is accessed through this server.

All core hardware (CPU, Power Management, Bus Interconnects) have drivers which are implemented in the non-extendable I/O Server. Any non-vital hardware will have drivers in the extendable version.

###The System Server

This is the heart of the Core System Layer. It is split into 2 pieces, SystemServer and SystemUIServer. SystemServer handles all primitive data processing for the system, whereas SystemUIServer presents the default User Interface for the system.

###The Fault Server

This server is much simpler and less frequently used than the other servers which are part of the Core System Layer. It simply handles any faults or exceptions not handled elsewhere. It is in charge of printing an error message on an unrecoverable error.

Unprivileged Runtime Overview
=============================

- Object Runtime
- Notification-Based Programs

Code and Documentation
======================

The full version of this document, as well as the rest of the documentation for the system which is publicly available at this point is available at http://beeselmane.com/h/cxdox.php.

The full code for the system is available in its current state at http://github.com/Corona-X. The project uses google repo and is therefore split into separate repositories for different components of the system proper.

Note: All of the software and documentation online is extremely volatile. Not all documentation is available on the online portal at this point, unfortunately. The kernel code itself is still in extremely basic form and may or may not be available by the time this document is made available.

Moving Forward
==============

Obviously, this is quite a large project which will require numerous people over the course of many years to create a fully functional system the likes of one of the mainstream Operating Systems utilized today. In order to accomplish this, I have created both a short and a long term road map for the development of the system, a short overview of which I will give below.

In the Short Term
-----------------

1. Finish writing up the initial documentation for the bootloader, kernel, and main servers for the system.
2. Create a functioning (bootable) kernel and upload it to github.
3. Collect some base statistics for system calls & context switches in the experimental kernel and compare them to some popular systems of today.
4. Write drivers for core system hardware.
5. Find some other people who are interested in developing a new Operating System and get some help designing and implementing the system.

In the Long Term
----------------

1. Create a community around the system and its development.
2. Add support for the ARMv8 microprocessor architecture.
3. Implement the POSIX API or some other System APIs.
4. Create a centralized application store for the platform.
5. Add more frameworks and functionality to the system.
