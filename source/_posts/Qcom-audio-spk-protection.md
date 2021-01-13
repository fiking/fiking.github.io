---
title: Qcom audio spk protection
tags:
  - Linux driver
  - audio
categories:
  - Linux
description: 
date: 2021-01-08 08:40:47
---

  系统起来的时候，会起一个线程，进行校准。有一个系统属性会去设定校准时间。

 <!-- more -->

```c
static int spkr_calibrate(int t0_spk_1, int t0_spk_2)
{
	pcm_dev_rx_id = platform_get_pcm_device_id(uc_info_rx->id, PCM_PLAYBACK);
}
 
static void* spkr_calibration_thread()
{
	status = spkr_calibrate(t0_spk_1, t0_spk_2);
}
 
 
void audio_extn_spkr_prot_init(void *adev)
{
	(void)pthread_create(&handle.spkr_calibration_thread, 
				(const pthread_attr_t *) NULL, 	
				spkr_calibration_thread, &handle);
 
}
 
void *platform_init(struct audio_device *adev)
{
	 audio_extn_spkr_prot_init(adev);
}
 
static int adev_open(const hw_module_t *module, const char *name,
                     hw_device_t **device)
{
	  adev->platform = platform_init(adev);
}
 
static struct hw_module_methods_t hal_module_methods = {
    .open = adev_open,
};
 
struct audio_module HAL_MODULE_INFO_SYM = {
    .common = {
        .tag = HARDWARE_MODULE_TAG,
        .module_api_version = AUDIO_MODULE_API_VERSION_0_1,
        .hal_api_version = HARDWARE_HAL_API_VERSION,
        .id = AUDIO_HARDWARE_MODULE_ID,
        .name = "QCOM Audio HAL",
        .author = "The Linux Foundation",
        .methods = &hal_module_methods,
    },
};
```

