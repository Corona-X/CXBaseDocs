> Corona-X Preliminary API A.1<br>
> November 2, 2016<br>
> Tyler Besselman<br>

[TOC]

#Introduction To Corona-X

##What Constitutes as an "Operating System"?

The term "Operating System" (OS) has been used to refer to a wide range of things. For the purpose of this document, it can be loosely taken as a collective set of software which secures and controls all hardware and software in a modern Computer System (CS). The Operating System ensures that hardware is accessed in a safe and secure manor; securely manages system and user-level software; and presents the user with a method through which to control the Computer System. Additionally, the Operating System may, but is not required to, make available a set of standard Application Programming Interfaces (APIs) in one or more programming languages through developers may create user applications, extend system software, or both.

##Most used Operating Systems today

###Desktop Operating Systems
####Microsoft Windows (Desktop Version)
- **Source Model:** Closed Source
- **Main Developer:** Microsoft
- **Kernel Design:** Monolithic/Hybrid, Hypervisor
- **Current Version:** 10 (NT)
- **Description:** The modern incarnation of Microsoft PC-DOS. A powerful desktop and server Operating System which is currently the most used system in the world. Runs on Intel and AMD processor systems which support the x86 instruction set (32 or 64 bit). Supports multitasking, paging, and all of the features that would be expected of a modern system.

####Mac OS X (macOS v10)
- **Source Model:** Closed Source, Open-Source Components
- **Main Developer:** Apple Computer
- **Kernel Design:** Hybrid; Includes source code from FreeBSD 4 and Mach v4
- **Current Version:** 10.12
- **Description:** This is the tenth version of the Macintosh Operating System redesigned from the ground up. This release has been certified compliant with the Single-UNIX Specification (SUS), making it one of the last true UNIX systems in use today. Runs on Intel processor systems which support the x86 instruction set (64 bit only). Supports multitasking, paging, and all of the features that would be expected of a modern system.

####GNU/Linux
- **Source Model:** Open-Source, Free Software
- **Main Developer:** GNU Project, kernel.org, mainly community run with no single "leader"
- **Kernel Design:** Monolithic
- **Current Version:** Various for various components. The Linux Kernel is current version 4.8.0 "Psychotic Stoned Sheep"
- **Description:** This system is more of a group of software which is distributed in many distributions, or "distros." Each distribution includes a different version and selection of software, but most of them share a few core components, such as the Linux kernel and the GNU Compiler Collection (GCC). This Operating System supports an extremely large selection of systems, the only true requirement is that the processor has a Memory Management Unit (MMU). There is no standard distribution, but the Linux Kernel supports everything expected of a modern system.

####FreeBSD, OpenBSD, NetBSD, DragonFlyBSD, *BSD
- **Source Model:** Open-Source, some Systems include Proprietary Drivers/Firmware/Blobs
- **Main Developer:** Community Based; Project leaders for each project
- **Kernel Design:** Monolithic
- **Current Version:** Various, FreeBSD is currently on major release 11
- **Description (FreeBSD):** The last system truly derived from the original UNIX systems (save maybe Mac OS X, but this system is much more of a "true UNIX" system). Many industry-standards come from this project, which is mainly run by extremely experienced developers. The System Supports a few common CPU architectures, including both the 32 and 64 bit versions of many Intel, AMD, and ARM processors. This system is an extremely powerful system for users who have a lot of experience with computer systems, but its lack of a user-friendly default install (no Graphical User Interface!) leave it lacking. The system kernel supports everything expected of a modern system and even more.

###Mobile Operating Systems
####Android OS (Linux/BSD)
- **Source Model:** Open-Source base; most Original Equipment Manufacturers (OEMs) maintain a proprietary source tree for installation on their systems.
- **Main Developer:** Google (Alphabet Inc)
- **Kernel Design:** Monolithic (Usually the Linux Kernel; rarely the FreeBSD Kernel)
- **Current Version:** 7.0
- **Description:** The most commonly used Mobile Operating System by some measures, this system began with an amazing promise: an Open-Source standard to which all cell phones would comply. Unfortunately, with most OEMs maintaining a private source tree which may not even be recognizable in the binary form, there are many inconsistencies between different devices even if both are running the same version of Android. Additionally, Google privately develops new versions of the System and does not release them to the public until they are released on their devices. This system is a very modern mobile OS, supporting everything users would expect (except perhaps application consistency between versions).

####iOS
- **Source Model:** Closed Source, Some Open-Source Components Shared with OS X
- **Kernel Design:** Hybrid (XNU from OS X)
- **Current Version:** 10.1.1
- **Description:** The other major mobile Operating System, iOS presents the user with a very simple, easy to use interface by default. The system itself is not able to be configured much, save the small selection of settings supplied by apple in their Preferences Application. Runs on 32 and 64 bit ARM machines (ARMv7 and ARMv8). This system runs only on the iPhone line of phones and the iPod touch line. Apple now designs a large part of the ARM specification, allowing this Operating System to take full advantage of the hardware features of the base Instruction Set Architecture (ISA) it runs on. Supports a wide range of native features

####Windows Mobile
- **Note:** I know almost nothing about this system. I assume it's simply windows 7 ported to a phone with a new UI. Probably uses the same old NtOSKernel that Windows has used since NT (Windows 2000).

##Problems with Modern Operating Systems

###For System Developers

####Unavailability of Code
- Some Systems are entirely closed source. This means that System Developers either cannot program for the system at all, or that they can only program through a set of private headers which are exported for them. It is almost impossible to write good system code without having the base source code which you are extending, so this is really a detriment.
- Many common Systems are released in source version only in part much later than the proprietary release takes place. This is effectively the same as the first point.
- Usually, the most important and interesting pieces of System Code are kept in the private source tree kept by the company which develops the OS, making external System Developers unable to help with the furthering of innovation within the System itself.

####Shared Code Between Systems
- In almost every, if not every, Operating System in use today, code is shared between many developers working on many projects. Code is pulled in from external libraries and projects, which leads to many complications. Chiefly, there may be discrepancies between style, coding standard, and overall design of code modules within a single Operating System. These issues not only make the job of the System Developer more difficult, but also may make the resulting system less stable, secure, or speedy.
- This may also lead to the next issue.

####License Issues
-	When Code changes hands, or is otherwise edited, the license is often changed. Even taking a small amount of code from a copywriter file may require the developer to use the previous license for the code.
- This may lead to files with multiple licenses or incompatible licenses, which can obviously be somewhat annoying for System Developers (not to mention the possibility of reduced innovation because of a somewhat restrictive license).

####Incompatable APIs with Differing Implementations
- Many Systems use multiple different System APIs throughout their lifetime. Usually, when APIs are added to the System, the old APIs are simply left intact inside the System. What the developers will commonly do will either be to re-implement the old API using the new API, or, more commonly, implement the new API using the old API. This leads to inefficiencies in one of the two APIs, as well as code differences which can be quite annoying to System Developers.
- When a new API is added, additionally, all System Software must be reimplemented in the new API before the old API can be properly deprecated. This is extremely annoying.

####Old Code
- No System in Common use today has been programmed entirely in the 21st century.
- Code from the 20th century is still used.
- Old code is not often updated and may not run as effectively as it could on modern hardware.

####Necessity to Support Legacy Functionality
- Systems today support legacy hardware in their core
- This legacy functionality must be tested in every release to ensure it was not broken by some change made during the release cycle.
- Legacy Support Code may be exploited or broken much more easily than core code due to the nature of the functionality which it exposes. Because it is not updated very often, and it is likely not as thoroughly checked, legacy support code may introduce many issues into the resulting system.

####Data Formats
- Most systems are limited to a single executable format, filesystem format, data formats, etc.
- System Developers need to have a framework through which they can write code to understand all different types of information in a streamlined manner.

####OS Updates
- Most systems do not include methods through which only parts of the system may be updated.
- Most systems take a decently long time to update

###For Application Developers

####Reliability
- Application Developers want to ensure that their Applications will not crash the System in the case of errors.
- Application Developers want to be able to use hardware acceleration in a reliable way.
- Applications Developers want Reliability without hindering Speed or Security.

####Speed
- Application Developers want to run their Applications as fast as possible.
- Application Developers do not want to hinder the performance of any other applications.
- Applications Developers want Speed without hindering Reliability or Security.

####Security & Debugging
- Application Developers want to be able to securely store information in such a way that no other application may access the data without explicit permission from either their application or the user. They want to be able to configure how exactly the data may be accessed.
- Application Developers want to be able to debug their applications easily and effectively, but they do not want their system to be less secure while debugging.
- Application Developers want to be able to debug their applications easily and effectively, but they do not want their application to be less secure because of debugging code.
- Application Developers want a method through which their Applications can be identified as legitimately downloaded and installed.
- Application Developers want a method through which changes in their application package can be detected and either corrected, or the application can be killed. They want this at launch, run, and quit time.
- Applications Developers want Security without hindering Speed or Reliability.

####API Inconsistencies
- Application Developers want the output of all System API functions to be as specified in the documentation or standard for the API. Even though this seems trivial, some systems are not entirely accurate when it comes to API standards/specifications.
- Application Developers want the output of their program to be the same when programming with different System APIs.

####Unclear/Inaccessible API Documentation
- System Developers want API Documentation to be easily accessibly and easy to use.
- System APIs should be understandable for even beginner developers.
- System Header Files (C Family) should be well commented. They often are not.

####Lack of Documentation for the System's Environment
- The underlying environment of the System should be laid out clearly in the System Documentation. This is almost never the case, even if the system is open source.
- Exactly how each API function works should be documented and published with the system developer's documentation.
- Performance Information for System API Functions should be published in this documentation.

####OS Updates
- A program that functions on one version of the Operating System should be able to function on any later version of the Operating System. The reverse should not necessarily be true.
- A document should be released as part of the updated developer's documentation which details the exact changes made to each API function in the update.

####Portability/Internationalization/Reliability
- Application Developers want to be able to easily port their Applications to different systems/system types.
- Application Developers want to be able to easily add multiple languages to their applications.
- Application Developers want their applications to function properly on different hardware.
- This is not always found in current systems, especially the first two wants.

###For End Users

####Reliability
- End Users don't want their System to Crash. Ever.
- This ^^

####Speed
- End Users want their System to be Fast. Period.
- This ^^

####Security
- End Users want their System and Data to be Secure. End of Story.
- This ^^

####Application Behavior Differences (Based on Differing System APIs)
- If a System implements multiple System APIs or versions of System APIs, there should be no way for a user to graphically tell which System API the application is currently running on.
- The experience between any two APIs should not be noticeably different.
- The exception to this rule is the case in which the application is written to specifically display this information to the user in some way, in which case it is permissible

####Tradeoff Between Customizability/Confusion
- In many, if not most, Operating Systems today, there is a trade off between more customizability and the confusion of the End Users. This should not be the case.
- Users should be able to configure their experience as much as possible (the limit of course is the violation of any of the other goals set forth here. The exception to this limit is development or debug builds of the system or individual components, which may violate some of these principles).
- The configuration which is presented to the end users should be easy to access and setup, as well as extremely clear about the exact function each option performs.

####Format Incompatibilities/Upgrades
- Users want the system to be able to access and understand data of any format they have it in.
- The system should provide methods for reading and accessing data in many formats.

####Functionality
- End Users want the System to do what they want.
- This is really summing up everything above, but it is entirely accurate.

####OS Updates
- End Users don't want to have to do anything to update their System.
- End Users don't want to have to wait for their System to update.
- End Users want to decide weather or not they want updates (but generally we should enable updates for them because they generally don't really understand why updating is such a big deal)

##Corona-X Overview

Corona-X is a new Operating System designed from the group up to address all of these issues and more. Being written in the 21st century, it aims to bring cutting-edge Operating System design theory to modern computer systems. One extremely important focus of the system is to the security, reliability, and performance demanded by many people today. The entire system is designed to work together in a sleek, stylish, and usable manner, and it is designed such that each components of the system overall is tightly integrated and tuned to every other component of the System proper to achieve extreme functionality and customizability without over-complicating the design.

###Corona-X Goals

**Note:** Each goal is presented in order of importance. That is, goals listed first take precedence over goals listed later.

1. **SRP:** Achieve extreme <a href="#security_section">Security</a>, <a href="#reliability_section">Reliability</a>, and <a href="performance_section">Performance</a> through proper design and optimization of code.
2. **Functionality:** Guarantee that all components of the System function as intended. Additionally, guarantee that all components of the System function together to create a single experience.
3. **Documentation:** Provide entirely accurate documentation of both intended and actual functionality of every aspect of the System (See <a href="#documentation">Documentation</a>).
4. **Code Clarity:** Generate a System codebase which conforms to the project's given <a href="#coding">Coding Standards</a>. Ensure that code is clear in meaning and commented properly where necessary to enhance overall readability.
5. **Experiment:** Foster an environment in which risks can be taken; delve into unexplored areas of OS Development; change the world, and have fun with it!

###Project Background

The Corona-X project is an Operating System development project started by the author of this document, Tyler Besselman. The project was started after many years of both user and system level programming. I had decided to attempt to make an Operating System almost immediately after learning basic programming as a challenge to myself. After doing some research, I realized the scale of the goal I had in mind, and I decided it was not such a good idea. Recently, however, after programming on most major systems and experiencing first hand a large portion of the issues listed in the <a href="#problems-with-modern-operating-systems">Problems with Modern Operating Systems</a> section, I decided to actually set out on this project to try to help curb the mess that is modern Systems development. I had, in my studies of programming, read many research papers and articles online about different OS designs, and became quite versed in system development vernacular. The designs of this system are a reflection of the knowledge that I have picked up over the last few years. In my designs, I used many of the theoretical models I have studied in an attempt to create a system with a minimal number of flaws.

###Project Overview

The project is still run by Tyler Besselman. He's great, you should email him at beeselmane@gmail.com if you want to talk to him. He likes to talk so email him whatever you feel like about the project (or about something else, he doesn't much care).

The project will be hosted on GitHub; all source code will be public and contained in a single organization. Those who contribute code will only be given direct write access to these repositories once they are fully trusted with that responsibility, but anyone is fully welcome to propose any set of patches they believe would be useful to accomplishing the System's <a href="corona-x-goals">Goals</a>.

###<a href="#system_overview">System Overview</a>

##Solving problems with Corona-X

###For System Developers

####Unavailability of Code
- This problem is avoided entirely.
- Every piece of code in the Corona-X system will be publicly released.
- The only possible exception to this rule would be a legal one. If it is not legally allowable to release a piece of source code, it will not be released; however, this circumstance should be avoided wherever possible (This shouldn't really be an issue).

####Shared Code Between Systems
- This problem is avoided entirely.
- All code is developed together; no code will be shared from any other System.

####License Issues
- This problem is avoided entirely.
- All code in the project is developed from scratch, so it will all fall under the same license
- **Note:** A license for the code has not yet been selected, but once a license is selected, all code will be released under this license.

####Incompatable APIs with Differing Implementations
- This problem is avoided.
- APIs in Corona-X will all be implemented in one of two ways:
	1. The API will be implemented in a server, meaning that it will be implemented with the Kernel's Server API.
	2. The API will be a translation from the SystemServer's Core API (Implemented via method 1).

####Old Code
- This problem is avoided entirely.
- The oldest file in the entire system was originally written sometime in October 2014. This file is `Corona-X.h`, which includes macros for architecture/compiler/host system/language/etc. detection, and it is updated extremely often.
- While it is not an explicit goal of the project, it would be almost impossible to fulfill any of the goals without frequently updating the code of the System to be the best it could possible be.

####Necessity to Support Legacy Functionality
- This problem is avoided entirely.
- Corona-X does not mandate legacy support. While some may inevitably view this as a flaw, the scalability of the System does allow for legacy support to be added afterward at almost no performance cost (Especially because of the high latency most legacy devices incur).

####Data Formats
- This problem is avoided.
- The Corona-X concept of Servers, in combination with the Kernel's System Tree concept, allow system developers to implement a near-endless amount of data translating software.
- The entire System is extremely scalable, lending well to extension in the future.

####OS Updates
	- <font color="#FF0000">This problem is still outstanding.</font>
	- Possible solutions include:
		1. Include some method through which patches can be applied to the System Binaries on-disk while the System is still Running (This one is alright, but it's really hard to implement securely [especilly while maintaining the integrity of the running System]).
		2. Don't update the System (I don't like this one).

###For Application Developers

####Reliability

####Speed

####Security & Debugging

####API Inconsistencies

####Unclear/Inaccessible API Documentation

####Lack of Documentation for the System's Environment

####OS Updates

####Portability/Internationalization/Reliability

###For End Users

####Reliability

####Speed

####Security

####Application Behavior Differences (Based on Differing System APIs)

####Tradeoff Between Customizability/Confusion

####Format Incompatibilities/Upgrades

####Functionality

####OS Updates

---

#<span id="system_overview">High-Level System Overview</span>

---

#<span id="security_section">Security</span>

---

#<span id="speed_section">Speed</span>

---

#<span id="reliability_section">Reliability</span>

---

#<span id="documentation">Documentation</span>

---

#<span id="coding">Coding Standards</span>

##C

##C++

##Objective-C

##C#

##Java

**Note to Self:** Link to the Proper Document!!

---
