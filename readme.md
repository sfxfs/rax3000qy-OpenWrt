# 移动 RAX3000-Q(Y) 路由器刷机教程

[English Version](readme_en.md)

本教程参考 [skyblog](https://www.skyblogs.xyz/index.php/2023-01-30/%E6%8F%90%E6%9D%83%E8%BF%9B%E5%85%A5%E4%B8%AD%E5%9B%BD%E7%A7%BB%E5%8A%A8rax3000q%E8%B7%AF%E7%94%B1%E5%99%A8%E7%9A%84ssh%E5%B9%B6%E5%AE%89%E8%A3%85luci/) 、[恩山 imlk](https://www.right.com.cn/forum/thread-8111244-1-1.html) ，OpenWrt 固件为 [NWrt](https://www.right.com.cn/forum/forum.php?mod=viewthread&tid=8312936&highlight=rax3000)。

## 一、取得权限

有两种取得权限的方式，分别是 `ssh` 和 `telnet`，本文将分别介绍两种方式：

### 1. 取得 SSH

- 首先进入后台页面 (后台地址、用户名和密码请看路由器背面)
- 进入「更多 --> 诊断 --> ping」页面
- 在”URL或者IP地址”的输入框中输入：`$(dropbear${IFS}-p${IFS}22)` 来启动 dropbear
- 再通过 `$(passwd${IFS}-d${IFS}root)` 来删除 root 的密码
- 使用 ssh 命令来连接路由器：`ssh root@192.168.x.x` 后将会直接进入而不会询问密码

### 2. 取得 Telnet

- 首先进入后台页面 (后台地址、用户名和密码请看路由器背面)
- 进入「更多 --> 诊断 --> ping」页面
- 通过 `$(passwd${IFS}-d${IFS}root)` 来删除 root 的密码
- 重新在浏览器后台页面输入用户名和密码为：`superadmin：83583000` 后进入后台
- 在「管理 - 系统设置」页面可开启 telnet
- 注意telnet端口号为 `4719`，应使用 `telnet 192.168.10.1 4719` 登陆，用户名为 root，密码无

## 二、刷入 Uboot

- 电脑端打开 `WinSCP` 软件

- 新建站点——文件协议选择scp——主机名就是路由器ip——输入用户名 root 和密码登录

- 把 `uboot` 文件夹内的 `nwrt_rax3000qy_uboot.mbn` 与 `nwrt_rax3000qy_mibib.bin` 放到路由器 `/tmp` 文件夹下

- 进入路由器终端 输入如下两个命令：

  ```bash
  mtd write /tmp/nwrt_rax3000qy_uboot.mbn /dev/mtd11
  
  mtd write /tmp/nwrt_rax3000qy_mibib.bin /dev/mtd1
  ```

- 此时 uboot 已经刷入完毕，可以断开电源。

## 三、刷入 OpenWrt

- 电脑进入控制面板内的网络接口设置网口为静态 ip `192.168.1.8` 子网掩码 `255.255.255.0`
- 在路由器按住 `reset`，插上电源 10 秒后松开 reset，浏览器输入 `192.168.1.1` 进入 `Uboot`
- 选择 `openwrt-fw` 内的任意一个刷机包刷入即可
  - 12 月的版本带 `passwall` ,但有无法启动的问题（不稳定）
  - 11 月版本不带 `passwall` ,可通过软件源安装（稳定）
  - 1 月版本也相对较稳定
- 刷入完毕后机器将会重启，耐心等待一会即可进入系统

