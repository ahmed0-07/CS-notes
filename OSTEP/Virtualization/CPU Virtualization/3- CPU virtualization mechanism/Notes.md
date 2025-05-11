# INTRO:

In order to virtualize the CPU, the operating system needs to somehow share the physical CPU among many jobs running seemingly at the same time. The basic idea is simple: run one process for a little while, then run another one, and so forth. By **time sharing** the CPU in this manner, virtualization is achieved.

> But how can we achieve virtualization with high performance while maintaining control?


# Limited Direct Execution

The **“direct execution”** part of the idea is simple: just run the program directly on the CPU.

But this approach has few problems:
1. how can the OS make sure the program doesn’t do anything that we don’t want it to do?
2. how does the OS stop it from running and switch to another process?

## Problem 1: Restricted Operations

>What should a user program do when it wishes to preform some kind of privileged operation?

**system calls:** allow the kernel to carefully expose certain key pieces of functionality to user programs.

- To execute a system call, a program must execute a special trap instruction. This instruction simultaneously jumps into the kernel and raises the privilege level to kernel mode.
- the system can now perform what ever privileged operations are needed, and thus do the required work for the calling process.
- When finished, the OS calls a special return-from-trap instruction, which, as you might expect, returns into the calling user program while simultaneously reducing the privilege level back to user mode.

>how does the trap know which code to run inside the OS?

- The kernel set up a trap table at boot time. When the machine boots up, it does so in privileged mode, and thus is free to configure machine hardware as need be. One of the first things the OS thus does is to tell the hardware what code to run when certain exceptional events occur.

- The OS informs the hardware of the locations of these trap handlers, usually with some kind of special instruction. Once the hardware is informed, it remembers the location of these handlers until the machine is next rebooted, and thus the hardware knows what to do when system calls and other exceptional events take place.


## Problem 2: Switching Between Processes

- if a process is running on the CPU, this by definition means the OS is not running. If the OS is not running, how can the OS change running process? how can it do anything at all?

>So our challenge here, how can the OS regain control?

###### 1- A Cooperative Approach: Wait For System Calls

- In this style, the OS trusts the processes of the system to behave reasonably. Processes that run for too long are assumed to periodically give up the CPU by making a system call and here the OS regains control of the CPU.

>What happens, for example, if a process ends up in an infinite loop, and never makes a system call? What can the OS do then?

###### 2- A Non-Cooperative Approach: The OS Takes Control

- A **timer interrupt** device can be programmed to raise an interrupt every so many milliseconds; when the interrupt is raised, the currently running process is halted, and a pre-configured interrupt handler in the OS runs.

- At this point, the OS has regained control of the CPU, and thus can do what it pleases: stop the current process, and start a different one.

>Now the OS regained control, how it restores the state of a paused process?


# Context switch

- All the OS has to do is save a few register values for the currently-executing process and restore a few for the soon-to-be-executing process

- To save the context of the currently-running process, the OS will execute some low-level assembly code to save the general purpose registers, PC, and the kernel stack pointer of the currently-running process, and then restore said registers, PC, and switch to the kernel stack for the soon-to-be-executing process.

And here is a timeline of an entire process in limited direct execution:

![[Limited Direct Execution Protocol (Timer Interrupt).png]]

