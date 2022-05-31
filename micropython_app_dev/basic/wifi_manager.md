# 配置网络

:::caution 高度实验性特性

由于目前 WiFi Manager 模块特性尚未完全稳定，MicroPython 中对应的模块也可能在未来做出频繁改动，这些改动可能是破坏性的。

:::

配置网络通过与 [CSK6 的网络模块](https://docs.listenai.com/chips/600X/application/modules/network) 协作来完成。

## 开启配置

```kconfig
# WiFi
CONFIG_WIFI=y
CONFIG_WIFI_MANAGER=y
CONFIG_CSK_WIFI_STATION=y

CONFIG_NETWORKING=y
CONFIG_NET_L2_ETHERNET=y
CONFIG_NET_DHCPV4=y
CONFIG_NET_IPV4=y
CONFIG_NET_SOCKETS=y
CONFIG_NET_SOCKETS_POSIX_NAMES=y
CONFIG_POSIX_API=y

CONFIG_NET_MGMT=y
CONFIG_NET_MGMT_EVENT=y
CONFIG_NET_MGMT_EVENT_STACK_SIZE=4096

CONFIG_NET_RX_STACK_SIZE=15360
CONFIG_NET_TX_STACK_SIZE=15360
CONFIG_NET_PKT_RX_COUNT=16
CONFIG_NET_PKT_TX_COUNT=16
CONFIG_NET_BUF_RX_COUNT=64
CONFIG_NET_BUF_TX_COUNT=64
CONFIG_NET_CONTEXT_NET_PKT_POOL=y
```

使用过程中可能出现一些默认配置不够用的情况

| 日志提示 | 解决方案 |
| --- | --- |
| Stack Overflow | 适当调大 `CONFIG_SYSTEM_WORKQUEUE_STACK_SIZE` |
| malloc failed | 适当调大 `CONFIG_HEAP_MEM_POOL_SIZE` |

## 在 MicroPython 脚本使用

对于一个完整的配网流程，应当先从搜索热点开始，然后从搜索到的列表中找到目标热点，使用 SSID 和密码进行连接。连接上热点后，使用 DHCP 为网卡分配 ip 。这一过程的简单实现如下

```py
from csk import wifi_mgr
from zephyr import net_mgmt

net_mgmt.init()
wifi_mgr.init()

target_ssid = "your_ssid"
target_pwd = "your_password"


def dhcp_cb(msg):
    if "mask" in msg and msg["mask"] == net_mgmt.NET_EVENT_IPV4_DHCP_BOUND:
        data = msg["data"]
        print("dhcp cb >>> " + str(data))


def scan_done_cb(ap_list):
    if len([ap for ap in ap_list if ap["ssid"] == target_ssid]) > 0:
        wifi_mgr.sta_connect(target_ssid, target_pwd)
        # wifi_mgr.sta_connect({"ssid": target_ssid, "pwd": target_pwd})


def connected_cb(info):
    print(info)
    net_mgmt.dhcpv4_start()


net_mgmt.add_event_cb(dhcp_cb)
wifi_mgr.add_scan_done_cb(scan_done_cb)
wifi_mgr.sta_add_connected_cb(connected_cb)

wifi_mgr.scan_ap()
```

:::tip 提示

`net_mgmt` 模块对应 Zephyr 中的 Network Mangerment ，示例中只处理了 `NET_EVENT_IPV4_DHCP_BOUND` 回调，该回调代表已分配到 ip 。使用 `net_mgmt` 中包含的其他 `NET_EVENT` 开头的回调值可以处理其对应的事件，这些事件对应的含义请参考 Zephyr 对应文档章节 —— [Network Management](https://zephyr-docs.listenai.com/reference/networking/net_mgmt.html)。

:::