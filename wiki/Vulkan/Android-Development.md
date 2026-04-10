# Android-Development

Android 设备上通过 ADB 开启 Vulkan 校验层和调试属性。

---

## 开启校验层

```shell
adb shell setprop debug.vulkan.layers VK_LAYER_KHRONOS_validation
```

## 查看 Vulkan 属性

```shell
adb shell getprop | rg vulkan
```
