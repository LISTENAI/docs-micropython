# 概述

为了使开发者可以提升开发 CSK 应用的效率，我们也致力于让开发者可以使用更高级的编程语言来进行应用开发。

与此同时，业界内较为广泛的应用是使得 Python 可以在微处理器上运行一个轻量化的版本。 [MicroPython](https://github.com/micropython/micropython) 便是这一方向上较为知名的一种实现。

我们在搭建 MicroPython 应用开发环境的同时，完完全全的保留了 Zephyr 原生应用开发的环境，使得你既可以快速的从原生应用上加入对 MicroPython 的支持，也可以在 MicroPython 应用项目内增加原生代码（如 C 语言）的业务实现。
