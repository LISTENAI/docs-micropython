# 编译相关

## Windows 下报编译 `berkele-db-1.xx` 出错

<img
  src={require('../../images/symlink_build_failed.png').default}
  width="600px"
/>

在 `berkele-db-1.xx` 这一引用库中，有部分代码文件是通过软链接的形式创建的，而如果你在使用 `lisa mpy use-sdk` 命令的时候 Git 正确的创建成功，那么编译过程就会出错。

这是因为，在 Windows 中安装 Git 时，默认的安装流程没有勾选 **Enable symbolic links** 选项，这是让 Git 可以创建软连接文件必须开启的选项。可以尝试重新打开 Git 安装程序，到下图这一界面时，勾选对应选项进行安装。

<img
  src={require('../../images/git_install_guide.png').default}
  width="600px"
/>

## 编译提示 `warning: the choice symbol LV_*** is defined with a prompt outside the choice`

<img
  src={require('../../images/lvgl_build_failed.png').default}
  width="600px"
/>

这是在 Zephyr SDK for csk 自 `v1.0.3.beta` 之后引入共存的 LVGL8 导致的，在 MicroPython SDK 中维护着一份可转译到 MicroPython 调用的 LVGL 库，这两份 LVGL 中声明的 Kconfig 项本质上是一样的，所以编译工具会报 Kconfig 重复的警告并导致编译终止。

要解决这一警告，应当避免使用 Zephyr SDK 中的 LVGL （即 `CONFIG_LVGL8=y` 选项），并且执行以下命令暂时屏蔽对其引用，而后删除 `build/` 目录后重新编译即可。

```bash
lisa zep config manifest.group-filter ,-lvgl8
```

> 若当你不需要使用 MicroPython 且需要使用 Zephyr SDK 内的 LVGL8 时，可通过 `lisa zep config -d manifest.group-filter` 来恢复。