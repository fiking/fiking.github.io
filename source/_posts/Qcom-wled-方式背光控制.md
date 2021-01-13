---
title: Qcom wled 方式背光控制
tags:
  - linux driver
  - Qcom cpu
categories:
  - linux
description: 
date: 2021-01-08 08:40:47
---

### 注册部分

```c
// kernel/msm-3.18/drivers/leds/leds-qpnp-wled.c
static int qpnp_wled_probe(struct spmi_device *spmi)
{
    .................................

    INIT_WORK(&wled->work, qpnp_wled_work);
    wled->ramp_ms = QPNP_WLED_RAMP_DLY_MS;
    wled->ramp_step = 1; 


    wled->cdev.brightness_set = qpnp_wled_set;
    wled->cdev.brightness_get = qpnp_wled_get;


    wled->cdev.max_brightness = WLED_MAX_LEVEL_4095;


    rc = led_classdev_register(&spmi->dev, &wled->cdev);
    .................................
}
```

<!-- more -->

### 应用部分

```c
// kernel/msm-3.18/drivers/leds/leds-qpnp-wled.c
static void qpnp_wled_set(struct led_classdev *led_cdev,  enum led_brightness level)
{
    .................................

    schedule_work(&wled->work);

    ................................

}
```

```c
static void qpnp_wled_work(struct work_struct *work)
{
    ...........................................

    // 设置亮度

    rc = qpnp_wled_set_level(wled, level);

    ..........................................

    // 当打开或关闭背光时，设置使能寄存器

    rc = qpnp_wled_module_en(wled, wled->ctrl_base, !!level);
    .........................................

}
```

```c
static int qpnp_wled_set_level(struct qpnp_wled *wled, int level)
{
    ................................
    // 写低位

    reg = level & QPNP_WLED_BRIGHT_LSB_MASK;
    rc = qpnp_wled_write_reg(wled, &reg, QPNP_WLED_BRIGHT_LSB_REG(wled->sink_base, 
                                                                  wled->strings[i]));
    // 写高位
    
    reg = level >> QPNP_WLED_BRIGHT_MSB_SHIFT;
    reg = reg & QPNP_WLED_BRIGHT_MSB_MASK;
    rc = qpnp_wled_write_reg(wled, &reg, QPNP_WLED_BRIGHT_MSB_REG(wled->sink_base, 
                                                                  wled->strings[i]));

    ...................................
} 
```

```c
static int qpnp_wled_module_en(struct qpnp_wled *wled,  u16 base_addr, bool state)
{
    rc = qpnp_wled_read_reg(wled, &reg,
                            QPNP_WLED_MODULE_EN_REG(base_addr));
    if (rc < 0)
        return rc;
    reg &= QPNP_WLED_MODULE_EN_MASK;
    reg |= (state << QPNP_WLED_MODULE_EN_SHIFT);
    rc = qpnp_wled_write_reg(wled, &reg,
                             QPNP_WLED_MODULE_EN_REG(base_addr));
    
    if (rc)
        return rc;
}
```

```c
static int qpnp_wled_write_reg(struct qpnp_wled *wled, u8 *data, u16 addr)
{
    // 写 pmi 寄存器
    rc = spmi_ext_register_writel(wled->spmi->ctrl, wled->spmi->sid, addr, data, 1);

}
```

### 卸载部分

```c
// kernel/msm-3.18/drivers/platform/msm/spmi/spmi.c
int spmi_ext_register_writel(struct spmi_controller *ctrl,  u8 sid, u16 addr, u8 *buf, int len)
{
}
```



 

