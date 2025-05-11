# Intro:

>The process is just a running program.

The program itself is a lifeless thing: it just sits there on the disk, a bunch of instructions waiting to spring into action.

>How OS runs many processes at one time?

The OS creates this illusion of by virtualizing the CPU. By running one process, then stopping it and running another, and so forth, the OS can promote the illusion that many virtual CPUs exist when in fact there is only one physical CPU (or a few).

This basic technique, known as **time sharing** of the CPU, allows users to run as many concurrent processes as they would like; the potential cost is performance, as each will run more slowly if the CPU(s) must be shared.

>How to implement virtualization?

to implement it well, the OS will need both some low-level machinery and some high-level intelligence:

1. **mechanisms:** are low-level methods or protocols that implement a needed piece of functionality.
2. **Policies:** are algorithms for making some kind of decision within the OS.


# The Process:

- The abstraction provided by the OS of a running program is something we will call a process.
- we can summarize a process by taking an inventory of the different pieces of the system it accesses or affects during the execution.

**machine state:** 
- what a program can read or update when the program is running?
- what parts of the machine are important to the execution of this program?

>memory is an component of machine state which can describe a process: Instructions lie in memory; the data that the running program reads and writes sits in memory as well. Thus the memory that the process can address (called its address space) is part of the process.

## Process API:

- **Create**: An operating system must include some method to create new processes. When you type a command into the shell, or double-click on an application icon, the OS is invoked to create a new process to run the program you have indicated.
- **Destroy**: As there is an interface for process creation, systems also provide an interface to destroy processes forcefully. Of course, many processes will run and just exit by them selves when complete; when they don’t, however, the user may wish to kill them, and thus an interface to halt a runaway process is quite useful. 
- **Wait**: Sometimes it is useful to wait for a process to stop running; thus some kind of waiting interface is often provided. 
- **Miscellaneous Control:** Other than killing or waiting for a process, there are sometimes other controls that are possible. For example, most operating systems provide some kind of method to suspend a process (stop it from running for a while) and then resume it (continue it running). 
- **Status:** There are usually interfaces to get some status information about a process as well, such as how long it has run for, or what state it is in.

## Process Creation:

>how programs are transformed into processes?

1. The first thing that the OS must do to run a program is to load its code and any static data into memory, into the address space of the process. Programs initially reside on disk in some kind of executable format; 

![[Loading From Program To Process.png]]

2. OS allocates Some memory for the program’s run-time stack (The OS may also allocate some memory for the program’s heap. Like in C we use malloc).
3. The OS will also do some other initialization tasks, particularly as related to input/output (I/O).

>Now we are ready and the OS transfers control of the CPU to the newly-created process, and thus the program begins its execution.

## Process States:

A process can be in one of three states:

![[Process States.png]]

1. **Running**: In the running state, a process is running on a processor. This means it is executing instructions.
2. **Ready**: In the ready state, a process is ready to run but for some reason the OS has chosen not to run it at this given moment.
3. **Blocked**: In the blocked state, a process has performed some kind of operation that makes it not ready to run until some other event takes place. A common example: when a process initiates an I/O request to a disk, it becomes blocked and thus some other process can use the processor.
