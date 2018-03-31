## chapter 8 exceptional control flow

At the operating system level, the kernel transfers control from one user process to another via `context switches`. At the application level, a process can send a `signal` to another process that abruptly transfers control to a `signal handler` in the recipient.

### 8.1 Exceptions

Exceptions are a form of exceptional control flow that are implemented partly by the hardware and partly by the operating system.

In any case, when the processor detects that the event has occurred, it makes an indirect procedure call(the exception), through a jump table called an `exception table`, to an operating system subroutine(the exception handler)that is specifically designed to process this particular kind of event.

When the exception handler finishes processing, one of three things happens, depending on the type of event that caused the exception:

1. The handler returns control to current instruction, the instruction that was executing when the event occurred.
2. The handler returns control to the next instruction, the instruction that would have executed next had the exception not occurred.
3. The handler aborts the interrupted program.

#### 8.1.1 Exception Handling

Each type of possible exception in a system is assigned a unique nonnegative integer exception number.

> NOTE: Exception handler run in kernel mode, which means they have complete access to all system resources.

#### 8.1.2 Classes of Exceptions

Exceptions can be divided into four classes:

- interrupts
- traps
- faults
- aborts

##### Interrupts

*Interrupts* occur *asynchronously* as a result of signals from I/O devices that are external to the processor.

The effect is that the program continues executing as though the interrupt had never happened.

##### Traps and System Calls

*Traps* are *intentional* exceptions that occur as a result of executing an instruction. The most important use of traps is to provide a procedure-like interface between user programs and the kernel known as a *system call*.

##### Faults

Otherwise, the handler returns to an abort routine in the kernel that terminates the application program that caused the fault.

##### Aborts

Abort handlers never return control to the application program.

#### 8.1.3 Exceptions in Linux/IA32 Systems

C programs can invoke any system call directly by using the syscall function.

> NOTE: All parameters to Linux system calls are passed through general purpose registers rather than the stack. By convention, register %eax contains the syscall number, and registers %ebx, %ecx, %edx, %esi, %edi, and %ebp contain up to six arbitrary arguments.

### 8.2 Processes

#### 8.2.1 Logical Control Flow

#### 8.2.2 Concurrent Flows

A logical flow whose execution overlaps in time with another flow is called a *concurrent flow*, and the two flows are said to *run concurrently*. For example, process A and B run concurrently, as do A and C. On the other hand, B and C do not run concurrently, because the last instruction of B executes before the first instruction of C.

The general phenomenon of multiple flows executing concurrently is known as *concurrency*. The notion of a process taking turns with other processes is also known as *multitaking*. Each time period that a process executes a portion of its flow is called a *time slice*.

If two flows are running concurrently on different processor cores or computers, then we say that they are *parallel flows*, that they are *running in parallel*, and have *parallel execution*.

#### 8.2.3 Private Address Space

#### 8.2.4 User and Kernel Modes

A process running in kernel mode can execute any instruction in the instruction set and access any memory location in the system.

The only way for the process to change from user mode to kernel mode is via an execution such as interrupt, a fault, or a trapping system call.

> NOTE: Linux provides a clever mechanism, call the /proc filesystem, that allows user mode processes to access the contents of kernel data structures.

#### 8.2.5 Context switches

Another example is the sleep system call, which is an explicit request to put the calling process to sleep.

### 8.3 System Call Error Handling

When Unix system-level functions encounter an error, they typically return -1 and set the global integer variable errno to indicate what went wrong.

### 8.4 Process Control

#### 8.4.1 Obtaining Process IDs

#### 8.4.2 Creating and Terminating Processes

From a programmer's perspective, we can think of a process as being in one of three states:

- Running
- Stopped
- Terminated

The exit function terminates the process with an exit *status* of status. (The other way to set the exit status is to return an integer value from the main routine.)

The child gets an identical(but separate)copy of the parent's **user-level** virtual address space, including the text, data, and bss segments, heap, and user stack. The child also gets identical copies of any of the parent's open file descriptors, which means the child can read and write any files that were open in the parent when it called fork. The most significant difference between the parent and the newly created child is that have different PIDs.

The fork function is interesting(and often confusing)because it is called *once* but it returns *twice*: once in the calling process(the parent), and once in the newly created child process. In the parent, fork function returns the PID of the child. In the child fork function returns a value of 0.

#### 8.4.3 Reaping Child Process

When a process terminates for any reason, the kernel does not remove it from the system immediately. Instead, the process is kept around in a terminated state until it is *reaped* by its parent. A terminated process that has not yet been reaped is called a *zombie*.

If the parent process terminates without reaping its zombie children, the kernel arranges for the init process to reap them. The init process has PID of 1 and is created by the kernel during system initialization.

A process waits for its children to terminate or stop by calling the waitpid function.

```
#include <sys/types.h>
#include <sys/wait.h>

// return PID of child if OK, 0(if WNOHANG)or -1 on error
pid_t waitpid(pid_t pid, int *status, int options);
```
> NOTE: If a process in the wait set has already terminated at the time of the call, then waitpid returns immediately. In either case, waitpid returns the PID of the terminated child that caused waitpid to return, and the terminated child is removed from system.

##### Determining the Members of the Wait Set

The members of the wait set are determined by the pid argument:

- If pid > 0, then the wait set is the singleton child process whose process ID is equal to pid.
- If pid = -1, then the wait set consists of all of the parent's child processes.

```
// Calling wait(&status) is equivalent to calling waitpid(-1, &status, 0).

#include <sys/types.h>
#include <sys/wait.h>

pid_t wait(int *status);
```

#### 8.4.4 Putting Processes to Sleep

```
#include <unistd.h>

// returns seconds left to sleep
unsigned int sleep(unsigned int secs);
```

Another function that we will find usefull is the pause function, which puts the calling function to sleep until a signal is received by th process.

```
#include <unistd.h>

// always return -1
int pause(void);
```

#### 8.4.5 Loading and Running Programs

```
#include <unistd.h>

// does not return if OK, returns -1 on error
int execve(const char *filename, const char *argv[], const char *envp[]);
```

Execve returns to the calling program only if there is an error such as not being able to fine filename. So unlike fork, which is called once but returns twice, execve is called once and never returns.

After execve loads filename, it calls the startup code described in Section 7.9. The startup code sets up the stack and passes control to the main routine of the new program, which has a prototype of the form

```
int main(int argc, char **argv, char **envp);
```

or equivalently,

```
int main(int argc, char *argv[], char *envp[]);
```

> NOTE: Programs vs. Processes
> The execve function loads and runs a new program in the context of the current process. While it overwrites the address space of the current process, it does not create a new process. The new program still has the same PID, and it inherits all of the file descriptors that were open at the time of the call to the execve function.

#### 8.4.6 Using fork and execve to Run Programs

### 8.5 Signals

In this section, we will study a higher-level software form of exceptional control flow, known as s Unix *signal*, that allows processes and kernel to interrupt other processes.

A *signal* is small message that notifies a process that an event of some type has occurred in the system.

Low-level hardware exceptions are processed by kernel's exception handlers and would not normally be visible to user process. Signals provide a mechanism for exposing the occurrence of such exceptions to user processes.

#### 8.5.1 Signal Terminology

A signal that has been sent but not yet received is called a *pendding signal*. At any point in time, there can be at most one pending signal of a particular type. If a process has pending signal of type k, then any subsequent signal of type k sent to that process are not queued; They are simply discarded.

#### 8.5.2 Sending Signals

Using the signal function to install a *signal handler* function(handler)that is called **asynchronously**, interrupting the infinite while loop in main, whenever the process receives a SIGALRM signal.

#### 8.5.3 Receiving Signals

Each signal type has a predefined *default action*, which is one of the following:

- The process terminates.
- The process terminates and dumps core.
- The process stops until restarted by a SIGCONT signal.
- The process ignores the signal.

> NOTE: The only exceptions are SIGSTOP and SIGKILL whose default actions cannot be changed.

```
#include <signal.h>

typedef void (*sighandler_t)(int);

// returns ptr to previous handler if OK, SIG_ERR on error(does not set errno)
sighandler_t signal(int signum, sighandler_t handler);
```

The signal fucntion can change the action associated with a signal signum in one of three ways:

- If handler is SIG_IGN, then signals of type signum are ignored.
- If handler is SIG_DFL, then the action for signals of type signum reverts to the default action.
- Otherwise, handler is the address of a use-defined function, called a *signal handler*, that will be called whenever the process receives a signal of type signum.

Changing the default action by passing the address of a handler to the signal function is known as *installing the handler*.

The invocation of the handler is called *catching the signal*.

The execution of the handler is referred to as *handling the signal*.

#### 8.5.4 Signal Handling Issues

However, subtle issues arise when a program catches multiple signals.

- *Pending signal are blocked.* Unix signal handlers typically block pending signals of the type currently being processed by the handler. For example, suppose a process has caught a SIGINT signal and its currently running its SIGINT handler. If another SIGINT signal is sent to the process, then the SIGINT will become pending signal, but will not be received until after the handler returns.

- *Pending signal are not queued.* There can be at most one pending signal of any particular type. Thus, if two signals of type k are sent to destination process while signal k is blocked because the destination process is currently executing a handler for signal k, then the second signal is simply discarded; it is not queued. The key idea is that the existence of a pending signal merely indicates that at least one signal has arrived.

- *System calls can be interrupted.* System calls such as wait, read, and accept that can potentially block the process for a long period of time called slow system calls. On some systems, slow system calls that are interrupted when a handler catches a signal do not resume when the signal handler returns, but instead return immediately to the user with an error condition and errno set to EINTER.

> NOTE: The crucial lesson is that signals cannot be used to count the occurrence of events in other processed.

### 8.6 Nonlocal Jumps

C provides a form of user-level exceptional control flow, called a *nonlocal jump*, that transfers control directly from one function to another currently executing function without having to go through the normal call-and-return sequence.

```
#include <setjmp.h>

int setjmp(jmp_buf env);
int sigsetjmp(sigjmp_buf env, int savesigs);

// returns 0 from setjmp, nonzero from longjmps
```

The setjmp function saves the current *calling environment* in the env buffer, for later use by longjmp, and returns a 0. The calling environment includes the program counter, stack pointer, and general purpose registers.

```
#include <setjmp.h>

// never returns
void longjmp(jmp_buf env, int retval);
void siglongjmp(sigjmp_buf env, int retval);
```

The setjmp function is called once, but returns multiple times: once when the setjmp is first called and the calling environment is stored int env buffer, and once for each corresponding longjmp call. On the other hand, the longjmp function is called once, but never returns.

> NOTE: The sigsetjmp and siglongjmp functions are versions of setjmp and lognjmp that can be used by signal handlers.
