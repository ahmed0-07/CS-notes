# The **fork()** System Call:

The `fork()` system call is used to create a new process, the process that is created is an (almost) exact copy of the calling process. That means that to the OS, it now looks like there are two copies of the program which is running, and both are about to return from the fork() system call. 

>The newly-created process doesn’t start running at main(), but form the fork() call.
## what fork() return in the code?

- **parent code:** the PID of the child process
- **child code:** zero

>The two process (parent and child) will run concurrently so we will not know which process will end first.

So we introduce the `Wait()` system call.

# The **wait()** System Call:

The **wait()** system call is used to wait for the child process to be done first.

>Now we now know that the child will always print first. Why do we know that? Well, it might simply run first, as before, and thus print before the parent. However, if the parent does happen to run first, it will immediately call wait(); this system call won’t return until the child has run and exited. Thus, even when the parent runs first, it politely waits for the child to finish running, then wait() returns, and then the parent prints its message.


# The **exec()** System Call:

This system call is useful when you want to run a program that is different from the calling program.

## What it does exactly?

given the name of an executable program and some arguments, it loads code (and static data) from that executable and overwrites its current code segment (and current static data) with it; the heap and stack and other parts of the memory space of the program are re-initialized.

Then the OS simply runs that program, passing in any arguments as the `argv`of that process. Thus, it does not create a new process; rather, it transforms the currently running program into a different running program (the new executable program).

>After the `exec()`, it is almost as if the original program never ran; a successful call to exec() never returns, the original program is completely replaced by the new program. The new program has no memory or knowledge of the original program, and the original program’s code is effectively erased from the process’s memory.

# Example

```
#include <stdio.h>
#include <unistd.h>
#include <sys/wait.h>

int main() {
    pid_t pid = fork(); // Create a child process

    if (pid < 0) {
        // fork() failed
        fprintf(stderr, "Fork failed!\n");
        return 1;
    } else if (pid == 0) {
        // Child process
        printf("Child process: About to run wc\n");
        execlp("wc", "wc", "p3.c", NULL); // Replace child with wc
        // If execlp() returns, it means it failed
        fprintf(stderr, "exec() failed!\n");
        return 1;
    } else {
        // Parent process
        printf("Parent process: Waiting for child to finish\n");
        wait(NULL); // Wait for the child to finish
        printf("Parent process: Child finished\n");
    }

    return 0;
}
```

Lets break this code:

- `fork()` creates a new process.
- The parent process will wait the child process using `wait()`.
- The child will call `exec()` and change the current process (the child) with the new program (wc).    
- If `exec()` fails (e.g., the executable file doesn’t exist), it returns, and you can handle the error.
    
- After a successful `exec()`, the original program's code is gone, so `exec()` never returns.


# How Shell works?

The shell is just a user program. It shows you a prompt and then waits for you to type something into it. You then type a command (i.e., the name of an executable program, plus any arguments) into it; in most cases, the shell then figures out where in the file system the executable resides, calls fork() to create a new child process to run the command, calls some variant of exec() to run the command, and then waits for the command to complete by calling wait(). When the child completes, the shell returns from wait() and prints out a prompt again, ready for your next command.

## Example

```
prompt> wc p3.c > newfile.txt
```

1. A new child process is created using `fork()`
2. The shell closes standard output and opens the file newfile.txt.
3. Call `exec()` and run `wc` with arguments `p3.c` 
4. Then back to the parent process (shell) and print `prompt>` again.