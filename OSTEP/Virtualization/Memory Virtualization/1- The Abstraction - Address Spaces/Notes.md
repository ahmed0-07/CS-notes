# The address space

- It is the running program’s view of memory in the system.
- Each virtual address space contains all of a program’s instructions and data, which can be referenced by the program via virtual addresses. (Code, stack, heap and others).

![[Screenshot (152).png]]

- The VM system is responsible for providing the illusion of a large, sparse, private address space to each running program.
- The OS, with some serious hardware help, will take each of these virtual memory references and turn them into physical addresses, which can be presented to the physical memory in order to fetch or update the desired information. 
- The OS will provide this service for many processes at once, making sure to protect programs from one another, as well as protect the OS. 

The entire approach requires a great deal of mechanism as well as some critical policies to work.

# Goals:

- **transparency** : The OS should implement virtual memory in a way that is invisible to the running program. Thus, the program shouldn’t be aware of the fact that memory is virtualized; rather, the program behaves as if it has its own private physical memory.
- **efficiency** 
- **protection** : The OS should make sure to protect processes from one another as well as the OS itself