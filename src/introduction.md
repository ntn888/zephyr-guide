
# Introduction

Zephyr is currently the prefered RTOS for new embedded product projects. Especially for IoT related projects. Considering that 99% of embedded projects these days is IoT, it is no surprise that Zephyr is the current mainline RTOS taking on the previous mainstay the freeRTOS.

In this text we will go through the basics of Zephyr, enough to get started quickly. Some advanced topics are later covered including a guide porting to a new board.

## Prerequisites

The hardware required to follow along is the [BBC micro:bit v2](https://docs.zephyrproject.org/2.6.0/boards/arm/bbc_microbit_v2/doc/index.html)\*. And obviously a host computer, preferably running a recent edition of Linux. You should have a good grasp of the C language. Previous knowledege in embedded development (MCUs, reading datasheets, protocols, etc) is certainly helpful.

\* Currently the book targets the stm32f103c8t6 bluepill. I'll re-adjust to the micro:bit v2.

## Source Code
The source code for this book is available on the [Github repository](https://github.com/ntn888/zephyr-guide).
