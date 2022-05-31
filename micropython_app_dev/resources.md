# 资源占用



## 基础

在一个使用了 `mpconfigport_full.h` 的 MicroPython 固件中，资源的占用情况大概为

| 项 | 大小 |
| --- | --- |
| 固件大小 | 344 K |
| 运行内存占用 | 1.6 K |

我们通过 `CONFIG_MICROPY_HEAP_SIZE` 来配置 MicroPython 运行内存的可用大小，默认情况下使用 `16 * 1024`，分配在 SRAM 空间中。

通过下述函数可以获取当前的堆内存占用信息。

```py
import micropython
micropython.mem_info()
```

## 加入 LVGL

| 项 | 大小 |
| --- | --- |
| 固件大小 | 838 K |
| 初始化后运行内存占用 | 1.8 K |

对于运行内存来说，当界面上新增控件后占用内存将随之增加，具体的运行内存大小需要根据实际情况来考量。如果希望在使用 LVGL 时对内存有一个监控的话，可通过开启 `CONFIG_LV_Z_MEM_POOL_MPY_HEAP` 来让 LVGL 与 MicroPython 共用内存，而后即可通过 MicroPython 的内存监控达到目的。

## 其他

当我们需要用到其他额外的能力时，需要为这些模块开启某些配置，而后固件的大小也会有响应的变化，这种情况下应对具体固件具体分析，此处不做过多讨论。