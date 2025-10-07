# Flashing Guide for China Mobile RAX3000-Q(Y) Router

[中文版](readme.md)

This tutorial references [skyblog](https://www.skyblogs.xyz/index.php/2023-01-30%E6%8F%90%E6%9D%83%E8%BF%9B%E5%85%A5%E4%B8%AD%E5%9B%BD%E7%A7%BB%E5%8A%A8rax3000qE8%B7%AF%E7%94%B1%E5%99%A8%E7%9A%84ssh%E5%B9%B6%E5%AE%89%E8%A3%85luci/) and[Right Forum (imlk)](https://www.right.com.cn/forum/thread-8111244-1-1.html).
The OpenWrt firmware is compiled based on the [QSDK](https://wiki.codelinaro.orgen/clo/qsdk/overview) project and provided by the [NWrt](https://www.right.comcn/forum/forum.php?mod=viewthread&tid=8312936&highlight=rax3000) team.
This repository is for **personal backup purposes only**. For other specificneeds, please contact NWrt.

**<u>Important Notice</u>**:
Before proceeding, make sure to **back up** the original firmware of yourrouter. You are **solely responsible** for any risks or damages that may occurduring the flashing process.

---

## 1. Gaining Access Privileges

**Warning:**
The privilege escalation methods described below may **stop working** afterfirmware updates.
If you fail to gain access, please check the [Issues page](https://github.comsfxfs/rax3000qy-OpenWrt/issues), refer to [other tutorials](https://hugo.utermuxdev/default/rax3000q-latest/), or search for alternative methods.
You are also welcome to open a new issue for help.

There are two main methods to gain access: `SSH` and `Telnet`.
(There is also a serial UART method that requires disassembling the router, butit is **not** covered in this guide.)

---

### 1.1 Enabling SSH (Option 1)

1. Log in to the router’s admin page (check the default address, username, andpassword on the router’s label).
2. Go to **More → Diagnostics → Ping**.
3. In the "URL or IP address" input box, enter:

   ```
   $(dropbear${IFS}-p${IFS}22)
   ```

   to start the dropbear SSH service.
4. Then run the following command to remove the root password:

   ```
   $(passwd${IFS}-d${IFS}root)
   ```
5. You can now connect to the router using SSH:

   ```
   ssh root@192.168.x.x
   ```

   You should be logged in directly without being prompted for a password.

---

### 1.2 Enabling Telnet (Option 2)

1. Log in to the router’s admin page (address, username, and password are on therouter’s label).
2. Go to **More → Diagnostics → Ping**.
3. Run the following command to remove the root password:

   ```
   $(passwd${IFS}-d${IFS}root)
   ```
4. Log back into the admin page using:

   ```
   Username: superadmin  
   Password: 83583000
   ```
5. Go to **Management → System Settings** and enable **Telnet**.
6. The Telnet port number is **4719**.
   Connect using:

   ```
   telnet 192.168.10.1 4719
   ```

   Username: `root`
   Password: *(leave empty)*

---

## 2. Flashing U-Boot

1. On your PC, open **WinSCP**.
2. Create a new site:

   * File protocol: `SCP`
   * Host name: router IP address
   * Username: `root`
   * Password: *(leave empty if none)*
3. Upload the following files from the `uboot` folder to the router’s `/tmp`directory:

   * `nwrt_rax3000qy_uboot.mbn`
   * `nwrt_rax3000qy_mibib.bin`
4. In the router terminal, execute the following commands:

   ```bash
   mtd write /tmp/nwrt_rax3000qy_uboot.mbn /dev/mtd11
   
   mtd write /tmp/nwrt_rax3000qy_mibib.bin /dev/mtd1
   ```
5. The U-Boot flashing process is now complete. You may disconnect the power.

---

## 3. Flashing OpenWrt Firmware

1. On your computer, set the network interface to a static IP:

   * IP Address: `192.168.1.2`
   * Subnet Mask: `255.255.255.0`
2. Hold down the **reset** button on the router, plug in the power, and releasethe reset button after **10 seconds**.
3. In your browser, navigate to `192.168.1.1` to enter the **U-Boot webinterface**.
4. Choose any firmware from the `openwrt-fw` folder to flash:

   * **Version 2023-12**: includes `passwall`, but may fail to boot (**unstable**)
   * **Version 2023-11**: does not include `passwall`, can be installed manually (**stable**)
   * Other versions: **test at your own risk** (in theory, newer = more stable)
5. After flashing, the router will reboot automatically. Wait patiently until itfinishes booting.

   * Default username: `root`
   * Default password: `password`