# GPIO

We will first discuss the GPIO - the most basic peripheral on an MCU.

As basic functionality of GPIOs, we set the output levels using the following commands:
```
    gpio_pin_toggle_dt(const struct gpio_dt_spec *spec);
```
```
    gpio_pin_set_dt(const struct gpio_dt_spec *spec, int value);
```
Value 0 sets the pin in logical 0 / inactive state. Any value other than 0 sets the pin in logical 1 / active state.

    int i = gpio_pin_get_dt(const struct gpio_dt_spec *spec);
1 – If pin logical value is 1 / active. 0 – If pin logical value is 0 / inactive.

the *_dt functions are a variant for ones without the suffix. For example, gpio_pin_toggle_dt(spec) is:
```
gpio_pin_toggle(spec->port, spec->pin);
```

## GPIO Config

We must initialse and configure a GPIO pin before we can use it. Use the following command:

    gpio_pin_configure_dt(const struct gpio_dt_spec *spec, gpio_flags_t extra_flags);

Remember to set the correct Pin direction [GPIO_INPUT | GPIO_OUTPUT_INACTIVE | GPIO_OUTPUT_ACTIVE] using the flags.

## Init Structure

Before we could use the above GPIO API, the gpio struct must be defined and assigned:


    static const struct gpio_dt_spec myled = GPIO_DT_SPEC_GET(<node_id>, gpios);



The reference page for [GPIO](https://docs.zephyrproject.org/latest/reference/peripherals/gpio.html) details the above functions and also lists other flags that can be used.

### Example - add an external button
This will require writing an overlay file. And use the GPIO API.

First create the ```boards/``` folder in a new app directory. See [Installation](./chapter_1.md#Installation) for creating an empty app template. And in the directory create the file "stm32_min_dev_blue.overlay", with the following contents. This will be the device tree for the external button addition.
```
/ {
	gpio_keys {
		compatible = "gpio-keys";
		butn: butn {
			label = "Key";
			gpios = <&gpioa 0 GPIO_ACTIVE_LOW>;
		};
	};

	aliases {
		butn0 = &butn;
	};
};
```

Wire up a button to the stm32 board with a pull up resistor like this:

< image for connecting button to PA0 >

Using the concepts discussed above; here is the final code for button prompted blink:


```
#include <zephyr.h>
#include <drivers/gpio.h>

/* 1000 msec = 1 sec */
#define SLEEP_TIME_MS   1000

/* The devicetree node identifier for the "led0" alias. */
#define LED0_NODE DT_ALIAS(led0)

/*
 * A build error on this line means your board is unsupported.
 * See the sample documentation for information on how to fix this.
 */
static const struct gpio_dt_spec led = GPIO_DT_SPEC_GET(LED0_NODE, gpios);
static const struct gpio_dt_spec specb = GPIO_DT_SPEC_GET(DT_ALIAS(butn0), gpios);

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

	while (1) {
		if (gpio_pin_get(specb.port, specb.pin)) {
			gpio_pin_set(led.port, led.pin, 1);
		}
			
		else {
			gpio_pin_set(led.port, led.pin, 0);
		}
	}
}
```

We will now discuss the code above in detail.

The first line includes the 'zephyr.h' file which in turn includes the kernel. Which enables the kernel subsystem as we saw in the previous chapter.

The second line speaks for itself. It includes the 'gpio' related API functions that we will use here.

Further down we use the function ```GPIO_DT_SPEC_GET``` to initialise a LED device C structure, as we have seen in the previous section. We do the same for 'specb' the struct for the button pin.

Then inside main the first thing we do is do the mandatory check to see if the device is ready for use. This indicates whether the provided device pointer is for a device known to be in a state where it can be used with its standard API. Using a device that does not return true for this check, results in undefined behaviour.

This is followed by the pin configuration as we have seen earlieer. Inside the while loop is the program logic. It simply relays the pin state of the button to the LED pin.

This is a very basic demonstration of push button with no debounce mechanism. The led may inadvertently toggle multiple times for a single push.