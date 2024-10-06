# Flashing Guide for China Mobile RAX3000-Q(Y) Router

This tutorial references [skyblog](https://www.skyblogs.xyz/index.php/2023-01-30/%E6%8F%90%E6%9D%83%E8%BF%9B%E5%85%A5%E4%B8%AD%E5%9B%BD%E7%A7%BB%E5%8A%A8rax3000q%E8%B7%AF%E7%94%B1%E5%99%A8%E7%9A%84ssh%E5%B9%B6%E5%AE%89%E8%A3%85luci/), [Enshan imlk](https://www.right.com.cn/forum/thread-8111244-1-1.html), and the OpenWrt firmware is [NWrt](https://www.right.com.cn/forum/forum.php?mod=viewthread&tid=8312936&highlight=rax3000).

## 1. Obtaining Permissions

There are two ways to obtain permissions, namely through `ssh` and `telnet`, and this article will introduce both methods separately:

### 1. Obtaining SSH

- First, access the backend page (check the router's back for backend address, username, and password).
- Go to "More -> Diagnostics -> Ping" page.
- In the "URL or IP address" input box, enter: `$(dropbear${IFS}-p${IFS}22)` to start dropbear.
- Then use `$(passwd${IFS}-d${IFS}root)` to delete the root password.
- Use the ssh command to connect to the router: `ssh root@192.168.x.x` and you will directly enter without being prompted for a password.

### 2. Obtaining Telnet

- First, access the backend page (check the router's back for backend address, username, and password).
- Go to "More -> Diagnostics -> Ping" page.
- Delete the root password using `$(passwd${IFS}-d${IFS}root)`.
- Log in to the backend with username and password as `superadmin: 83583000` and go to "Management -> System Settings" to enable telnet.
- Note that the telnet port number is `4719`. Use `telnet 192.168.10.1 4719` to log in, with the username as root and no password.

## 2. Flashing Uboot

- Open the `WinSCP` software on your computer.
- Create a new site - select the file protocol as SCP - the hostname is the router's IP - enter username root and login password.
- Place the `nwrt_rax3000qy_uboot.mbn` and `nwrt_rax3000qy_mibib.bin` files from the `uboot` folder into the router's `/tmp` folder.
- Enter the following two commands in the router terminal:

  ```bash
  mtd write /tmp/nwrt_rax3000qy_uboot.mbn /dev/mtd11
  
  mtd write /tmp/nwrt_rax3000qy_mibib.bin /dev/mtd1
  ```

- Uboot has now been flashed successfully. You can disconnect the power.

## 3. Flashing OpenWrt

- On your computer, set the network interface inside the control panel to a static IP of `192.168.1.8` and subnet mask of `255.255.255.0`.
- Press and hold the `reset` button on the router, plug in the power, release the reset after 10 seconds, and enter `192.168.1.1` in the browser to access `Uboot`.
- Choose any firmware package from `openwrt-fw` to flash.
  - The December version comes with `passwall`, but may have startup issues (unstable).
  - The November version does not have `passwall`, but can be installed via software sources (stable).
  - The January version is relatively stable.
- After flashing is complete, the device will restart. Wait patiently for a while to enter the system.