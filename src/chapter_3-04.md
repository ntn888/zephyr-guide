# Timers

Basic timing, there is nothing much to it. We just need a couple of functions for basic timing. If you are familiar with tick based timing in another RTOS, it's exactly the same.

```
// k_uptime_get_32();        // the original 64bit function is blocking and inefficient, use 32bit if possible

int time_stamp;
int milliseconds_spent;

time_stamp = k_uptime_get_32();

<do stuff>

milliseconds_spent = k_uptime_delta(&time_stamp);

```

## Hardware Timers with Interrupts

< study the samples/drivers/counters/alarm >