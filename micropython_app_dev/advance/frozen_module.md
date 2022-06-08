# 冻结模块

冻结模块，是指交叉编译后打包到固件映像中的 Python 模块。这一存在形式减少了 RAM 需求，因为代码直接从闪存执行。

MicroPython 支持模块以「冻结模块」的形式存在于固件中，而要引入冻结模块则要通过 [MicroPython 清单](https://listenai.github.io/MicroPythonDocCN/reference/manifest.html) 声明。

**下文以引入 `uasyncio` 模块为例。**

## 获取设备信息

> 由于冻结模块实际上是在 PC 上预先进行了编译操作，因此需要在编译时就知悉目标设备的平台、配置信息。

在已经烧录了 MicroPython 固件的设备中，通过串口进入 REPL 中，执行以下代码可以获取设备特征。

```python
import sys
sys_mpy = sys.implementation.mpy
arch = [None, 'x86', 'x64',
    'armv6', 'armv6m', 'armv7m', 'armv7em', 'armv7emsp', 'armv7emdp',
    'xtensa', 'xtensawin'][sys_mpy >> 10]
print('mpy version:', sys_mpy & 0xff)
print('mpy flags:', end='')
if arch:
    print(' -march=' + arch, end='')
if not sys_mpy & 0x200:
    print(' -mno-unicode', end='')
print()
```

在 CSK6002 中，执行上述代码会输出 

```bash
mpy version: 5
mpy flags: -march=armv7emsp
```

## 增加清单

在 `boards/` 目录下新增 `manifest.py` 文件，在其中声明

```python
include("$(MPY_DIR)/extmod/uasyncio/manifest.py")
```

> 该文件中的更多可用函数、用法，可参阅 [MicroPython 清单](https://listenai.github.io/MicroPythonDocCN/reference/manifest.html)

## 编译配置

在项目目录下的 `CMakeLists.txt` 中，增加

```cmake
# ...
set(MICROPY_FROZEN_MANIFEST ${MICROPY_PORT_DIR}/boards/manifest.py)
set(MICROPY_CROSS_FLAGS -march=armv7emsp)

include($ENV{MICROPY_SDK}/ports/zephyr/lib_micropython.cmake)

# ...
```

注意

- `MICROPY_CROSS_FLAGS` 保持和 [获取设备信息](#获取设备信息) 打印的 `mpy flags` 后的值保持一致
- 保持这两个变量在 `include($ENV{MICROPY_SDK}/ports/zephyr/lib_micropython.cmake)` 前

:::tip 引用
若对 MicroPython 内部如何处理冻结模块感兴趣，可参考以下文档
- [MicroPython 清单](https://listenai.github.io/MicroPythonDocCN/reference/manifest.html)
- [MicroPython .mpy 文件](https://listenai.github.io/MicroPythonDocCN/reference/mpyfiles.html)
:::