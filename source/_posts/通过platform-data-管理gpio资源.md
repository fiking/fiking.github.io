---
title: 通过platform data 管理gpio资源
tags:
  - Linux driver
  - Qcom
categories:
  - Linux
description: 
date: 2021-01-08 08:40:47
---

 ### driver的代码记录。

 <!-- more -->

```c
static int mydrv_probe(struct platform_device *pdev)
{
	struct mydev_gpio_platform_data *pdata = pdev->dev.platform_data;
	struct gpio_desc *red, *green, *blue, *power;
	
	red = gpiod_get_index(dev, "led", 0, GPIOD_OUT_HIGH);
	green = gpiod_get_index(dev, "led", 1, GPIOD_OUT_HIGH);
	blue = gpiod_get_index(dev, "led", 2, GPIOD_OUT_HIGH);
 
	power = gpiod_get(dev, "power", GPIOD_OUT_HIGH);
 
	return 0;
}
 
static struct platform_driver mydrv_driver = {
	.probe		= mydrv_probe,
	.remove		= mydrv_remove,
	.driver		= {
		.name	= "mydev_gpio",
	},
};
 
static int __init mydrv_init(void)
{
	return platform_driver_register(&mydrv_driver);
}
postcore_initcall(mydrv_init);
 
static void __exit mydrv_exit(void)
{
	platform_driver_unregister(&mydrv_driver);
}
module_exit(mydrv_exit);
```

### device 的代码

```c
#include <linux/gpio/machine.h>
#include <linux/platform_device.h>
 
#include "board.h"
#include "head.h"
 
struct gpiod_lookup_table gpios_table = {
	.dev_id = "mydev_gpio",
	.table = {
		GPIO_LOOKUP_IDX("gpio.0", 15, "led", 0, GPIO_ACTIVE_HIGH),
		GPIO_LOOKUP_IDX("gpio.0", 16, "led", 1, GPIO_ACTIVE_HIGH),
		GPIO_LOOKUP_IDX("gpio.0", 17, "led", 2, GPIO_ACTIVE_HIGH),
		GPIO_LOOKUP("gpio.0", 1, "power", GPIO_ACTIVE_LOW),
		{ },
	},
};
 
static struct mydev_gpio_platform_data mydev_platform_data = {
	.name	= "mydev",
	.type	= MYDEV_TYPE_FIRST,
};
 
static struct platform_device mydev_device = {
	.name	= "mydev_gpio",
	.id	= -1,
	.dev	= {
		.platform_data = &mydev_platform_data,
	},
};
 
int __init mydev_init(void)
{
	gpiod_add_lookup_table(&gpios_table);
	platform_device_register(&mydev_device);
}
```



