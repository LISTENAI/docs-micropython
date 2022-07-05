# 安装 SDK 相关

## Windows 下遇到 `process.title cannot be used as a valid name.`

![](../../images/use_sdk_failed.png)

这是因为你正在通过 CMD （命令提示符）执行命令，这是极度不推荐的，在这一微软官方不再推荐使用的方式上可能遇到的诸多问题可能都没有解决方案。

对于 `lisa mpy` 所支持的所有命令，请打开一个 PowerShell 终端来执行。