- # Mobile RAX3000-Q(Y) Router Flashing Tutorial

  This tutorial references [skyblog](https://www.skyblogs.xyz/index.php/2023-01-30/提权进入中国移动rax3000q路由器的ssh并安装luci/), [恩山 imlk](https://www.right.com.cn/forum/thread-8111244-1-1.html), and the OpenWrt firmware is provided by the [NWrt](https://www.right.com.cn/forum/forum.php?mod=viewthread&tid=8312936&highlight=rax3000) team. This repository is for personal backup purposes only. For other special requests, please contact NWrt for payment. The firmware is compiled based on the [QSDK](https://wiki.codelinaro.org/en/clo/qsdk/overview) project.

  **Please Note**: Before starting this tutorial, be sure to **back up** the original firmware of the device. You will **bear all consequences** resulting from the flashing process.

  ## I. Obtaining Access

  There are two ways to obtain access: `ssh` and `telnet`. This article will introduce both methods:

  ### 1. Obtaining SSH

  - First, access the backend page (backend address, username, and password can be found on the back of the router).
  - Navigate to "More --> Diagnostics --> Ping".
  - In the "URL or IP address" input box, enter: `$(dropbear${IFS}-p${IFS}22)` to start dropbear.
  - Then use `$(passwd${IFS}-d${IFS}root)` to delete the root password.
  - Use the ssh command to connect to the router: `ssh root@192.168.x.x`, and you will directly enter without being prompted for a password.

  ### 2. Obtaining Telnet

  - First, access the backend page (backend address, username, and password can be found on the back of the router).
  - Navigate to "More --> Diagnostics --> Ping".
  - Use `$(passwd${IFS}-d${IFS}root)` to delete the root password.
  - Re-enter the backend page using the username and password: `superadmin: 83583000`.
  - In the "Management - System Settings" page, you can enable telnet.
  - Note that the telnet port number is `4719`, and you should use `telnet 192.168.10.1 4719` to log in, with the username as root and no password.

  ## II. Flashing Uboot

  - Open `WinSCP` on your computer.

  - Create a new site—select scp as the file protocol—enter the router's IP as the hostname—log in with username root and no password.

  - Place the `nwrt_rax3000qy_uboot.mbn` and `nwrt_rax3000qy_mibib.bin` files from the `uboot` folder into the router's `/tmp` folder.

  - Enter the router terminal and input the following two commands:

    ```bash
    mtd write /tmp/nwrt_rax3000qy_uboot.mbn /dev/mtd11
    
    mtd write /tmp/nwrt_rax3000qy_mibib.bin /dev/mtd1
    ```

  - At this point, uboot has been successfully flashed, and you can disconnect the power.

  ## III. Flashing OpenWrt

  - Set your computer’s network interface to a static IP of `192.168.1.2` with a subnet mask of `255.255.255.0`.

  - Press and hold the `reset` button on the router, plug in the power for 10 seconds, then release the reset button. Enter `192.168.1.1` in your browser to access `Uboot`.

  - Choose any firmware package from

     

    ```
    openwrt-fw
    ```

     

    to flash:

    - The December version includes `passwall`, but has startup issues (**unstable**).
    - The November version does not include `passwall`, but it can be installed from the software source (stable).
    - Other versions please **test yourself**.

  - After flashing, the device will reboot. Wait a moment to enter the system.