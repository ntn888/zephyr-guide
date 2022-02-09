# The Kernel API - Threads

We will now look at the minimum Kernel API required to get going with the Peripherals. We will now look at the Threads API in this section.

## Thread Creation

```
define MY_STACK_SIZE 500
#define MY_PRIORITY 5

extern void my_entry_point(void *, void *, void *);

K_THREAD_STACK_DEFINE(my_stack_area, MY_STACK_SIZE);
struct k_thread my_thread_data;

k_tid_t my_tid = k_thread_create(&my_thread_data, my_stack_area,
                                 K_THREAD_STACK_SIZEOF(my_stack_area),
                                 my_entry_point,
                                 NULL, NULL, NULL,
                                 MY_PRIORITY, 0, K_NO_WAIT);
```

The preceding code spawns a thread immediately. We will study the arguments of the above function one at a time.

&my_thread_data:

my_stack_area:

my_entry_point: the entry point function (user defined), that takes upto 3 arguments. It's passed 'NULL' in this example.

MY_PRIORITY: the priority assigned for this thread. We will discuss priorities in detail below.

timeout: the amount of time in milliseconds to wait before the kernel actually starts the thread. Here it's 'K_NO_WAIT'; ie 0.

## Thread Priorities

< todo >

## Thread termination

Once a thread is started it runs forever. A thread may terminate by returning to the caller. ie 'return x'. The thread must be responsible for releasing any held resourses.
To wait until another thread terminates use ```k_thread_join()```.
```k_thread_abort()``` (ungracefully)  terminates the thread. Can be also used from within an external thread. It's not recommended to use this function as it leads to unfreed resources.

## Notable system threads

These are *essential threads* that are always present in an application.

### main thread

By default has a priority of 0 (highest pre-emptive). Performs kernel initialisation and runs the ```main()``` function.

### idle thread

Executes when no work. Puts the processor to auto- powersave. Always has the lowest priority.

## Delays

< todo >