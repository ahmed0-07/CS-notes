
# Operating System:

>A software that that manages computer hardware and software resources, and provides interfaces to deal with applications.

### Goals:

>1. providing protection between apps:
   we wish to allow many programs to run at the same time, we want to make sure that the malicious or accidental bad behavior of one does not harm others; we certainly don’t want an application to be able to harm the OS itself
  
>2. Provide efficient and fair access to resources
## The 3 pieces of OS:

>1. **Virtualization:**
> The OS abstracts hardware resources (like CPU and memory) to make them easier to use.
> It allows a single physical resource to be divided into multiple "virtual" resources that behave like separate physical devices.

>2. **Concurrency:**
> Concurrency refers to the ability of an OS to execute multiple tasks or processes simultaneously or in overlapping time periods.

>3. **Persistence:**
>Data is "persistent" when it’s stored in non-volatile storage so that it can be retrieved later, even after the computer is turned off.
>Persistence is critical for long-term data storage and ensuring that important data isn’t lost when a system shuts down or crashes.

## Codes:

1. **CPU Virtualization:**

```
#include <stdio.h>
#include <stdlib.h>
#include <sys/time.h>
#include <assert.h>
#include "common.h"

int main(int argc, char *argv[]) {
    if (argc != 2) {
        fprintf(stderr, "usage: cpu <string>\n");
        exit(1);
    }

    char *str = argv[1];

    while (1) {
        Spin(1);
        printf("%s\n", str);
    }

    return 0;
}

```

>this code just take input and print it every second for infinity.

**what if we run 4 instances from this program?**
```
./cpu A & ./cpu B & ./cpu C & ./cpu D &
```

**output**
```
[1] 7353 //process id
[2] 7354
[3] 7355
[4] 7356
A
B
D
C
A
B
D
C
A
```

>OS can run many programs at the same time by Turning a single CPU into a seemingly infinite number of CPUs and thus allowing many programs to seemingly run at once is what we call virtualizing the CPU.

2. **Memory Virtualization:**
```
#include <unistd.h>
#include <stdio.h>
#include <stdlib.h>
#include <assert.h>
#include "common.h"

int main(int argc, char *argv[]) {

    int *p = malloc(sizeof(int));
    assert(p != NULL); // Ensure memory allocation was successful
    
    printf("(%d) address pointed to by p: %p\n", getpid(), p);
    
    *p = 0;
    
    while (1) {
        Spin(1);
        *p = *p + 1;

        printf("(%d) p: %d\n", getpid(), *p); 
    }
    
    return 0;
}

```

>this code increase the value of address at p every second

**what if we run 2 instances from this program?**
```
./mem & ./mem
```

**output**
```
 [1] 24113
 [2] 24114
 (24113) address pointed to by p: 0x200000
 (24114) address pointed to by p: 0x200000
 (24113) p: 1
 (24114) p: 1
 (24114) p: 2
 (24113) p: 2
 (24113) p: 3
 (24114) p: 3
 (24113) p: 4
 (24114) p: 4

```

>we see that we are using the same address but in the actual physical memory we are not
