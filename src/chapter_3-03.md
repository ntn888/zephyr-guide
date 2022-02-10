# Interrupts

We will continue on our discussions with the GPIO with looking at interrupts. First for some prerequisites...

An Interrupt Service Routine (ISR) has the following properties:
- IRQ signal (that triggers the ISR)
- priority level
- interrupt handler function
- argument value passed to the function

Other notable facts:
- Only a single ISR can be defined for an IRQ
- But multiple ISRs can utilise the same function
- Zephyr supports interrupt nesting. ie, higher priority interrupt can interrupt a running ISR

Use the following functions to setup an ISR:
```
IRQ_CONNECT(MY_DEV_IRQ, MY_DEV_PRIO, my_isr, MY_ISR_ARG, MY_IRQ_FLAGS);
irq_enable(MY_DEV_IRQ);
```

See [Implementation](https://docs.zephyrproject.org/latest/reference/kernel/other/interrupts.html#id5) for an example.

### Disabling interrupts

To prevent IRQs when a particular thread is running use the functions inside that thread to block (and unblock) any interrupts.
```
irq_lock()
irq_unlock()
```
Note that if this thread is pre-empted by another one and is not the active thread, interrupts can occur.

## GPIO Interrupts

```
#include <zephyr.h>
#include <drivers/gpio.h>
#include <sys/util.h>
#include <inttypes.h>

/* 1000 msec = 1 sec */
#define SLEEP_TIME_MS   1000

/* The devicetree node identifier for the "led0" alias. */
#define LED0_NODE DT_ALIAS(led0)

static const struct gpio_dt_spec led = GPIO_DT_SPEC_GET(LED0_NODE, gpios);
static const struct gpio_dt_spec specb = GPIO_DT_SPEC_GET(DT_ALIAS(butn0), gpios);

static struct gpio_callback button_cb_data;

void button_pressed(const struct device *dev, struct gpio_callback *cb,
		    uint32_t pins)
{
	gpio_pin_toggle_dt(&led);
}

void main(void)
{
	int ret;

	if (!device_is_ready(led.port)) {
		return;
	}
	if (!device_is_ready(specb.port)) {
		return;
	}

	ret = gpio_pin_configure_dt(&led, GPIO_OUTPUT_ACTIVE);
	if (ret < 0) {
		return;
	}
	ret = gpio_pin_configure_dt(&specb, GPIO_INPUT);
	if (ret < 0) {
		return;
	}
	ret = gpio_pin_interrupt_configure_dt(&specb,
					      GPIO_INT_EDGE_TO_ACTIVE);
	if (ret != 0) {
		printk("Error %d: failed to configure interrupt on %s pin %d\n",
			ret, specb.port->name, specb.pin);
		return;
	}

	gpio_init_callback(&button_cb_data, button_pressed, BIT(specb.pin));
	gpio_add_callback(specb.port, &button_cb_data);

	while (1) {
	}
}

```

The above is a modified example from the samples folder 'basic->button'. It simply toggles the LED when the button is pushed.