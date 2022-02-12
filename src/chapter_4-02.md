# Mutex

In contrast a mutex allows exclusive access to the resource from the thread. This is why it's called binary semaphore. You either have the lock or you don't. It is practically similar to initiating a semaphore with a value of one. Since we've seen the concepts already in the previous topic, you'll be equipped to read the page on [Mutexes](https://docs.zephyrproject.org/latest/reference/kernel/synchronization/mutexes.html) in the official documentation.

As an aid lookup the test sample on mutex available in the Zephyr source ```zephyr/tests/kernel/mutex/sys_mutex/```.