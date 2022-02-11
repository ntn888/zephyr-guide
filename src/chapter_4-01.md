# Semaphores

The easiest way for threads to communicate with each other is through shared data structures. This process is especially easy when all threads exist in a single address space and can reference elements, such as global variables, pointers, buffers, linked lists, and ring buffers, ie use global variables. Although sharing data simplifies the exchange of information, you must ensure that each task has exclusive access to the data to avoid contention and data corruption.

To illustrate the problem, the figure below shows a hypothetical example of two threads reading and writing the same variable. In this example, thread A reads the variable and then writes a new value to it, but the write operation takes two memory cycles. If thread B reads the same variable between the two write cycles, it will see an inconsistent value.

![Interleaved memory cycles with two threads](./images/race-condition.svg)

To solve this problem, the threads have to use a lock that will allow only one thread to access the variable at a time.

### Test-and-Set Operations

The simplest way to mitigate this situation. Two functions could agree that to access a resource, they must check a global variable and if the variable is 0, the function has access to the resource. To prevent the other function from accessing the resource, however, the first function that gets the resource sets the variable to 1, which is called a test-and-set (or TAS) operation. We would also have to disable the interrupts however. Pseudocode:

```
Disable interrupts;
if (‘Access Variable’ is 0) {
    Set variable to 1;
    Reenable interrupts;
    Access the resource;
    Disable interrupts;
    Set the ‘Access Variable’ back to 0;
    Reenable interrupts;
} else {
    Reenable interrupts;
    /* You don’t have access to the resource, try back later; */
}
```

## Using Semaphores

A semaphore is a key that your code acquires in order to continue execution. If the semaphore is already in use, the requesting task is suspended until the semaphore is released by its current owner. In other words, the requesting task says: “Give me the key. If someone else is using it, I am willing to wait for it!” Two types of semaphores exist: binary semaphores and counting semaphores. As its name implies, a binary semaphore can only take two values: 0 or 1. A counting semaphore allows values between 0 and 255, 65,535, or 4,294,967,295, depending on whether the semaphore mechanism is implemented using 8, 16, or 32 bits, respectively. The actual size depends on the kernel used. Along with the semaphore’s value, the kernel also needs to keep track of tasks waiting for the semaphore’s availability.

Three operations are done with semaphores:
- initialise (create)
- wait (pend)
- signal (post)

A value must be provided when a semaphore is created. (Waiting list always starts empty). A desired task does a ```WAIT``` operation. Semaphore is checked (if > 0), then decremented and handed over. Else the task waits until a predefined timeout. If the semaphore was busy, when it becomes available the task releases by performing a ```SIGNAL``` operation, the control is yeilded to the waiting task (semaphore is not incremented!). Depending on the RTOS:
- highest priority task is picked
- or FIFO

In a primitive design, the task must know it's about to access a semaphore. So it may be better to *encapsulate* a semaphore.