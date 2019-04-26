Chapter 25: Kernel Services and Configuration
---------------------------------------------
  
[25.3: Learning Objectives](#253-learning-objectives)  
[25.4: Kernel and Operating System](#254-kernel-and-operating-system)  
[25.5: Main Kernel Tasks](#255-main-kernel-tasks)  
[25.6: Kernel Command Line](#256-kernel-command-line)  
[25.7: Kernel Boot Parameters](#257-kernel-boot-parameters)  
[25.8: sysctl](#258-sysctl)  
[25.9: sysctl Demo](#259-sysctl-demo)  
[Lab 25.1: System Tunables with sysctl](#lab-251-system-tunables-with-sysctl)  
[Lab 25.2: Changing the Maximum Process ID](#lab-252-changing-the-maximum-process-id)  
[Paths and Commands](#paths-and-commands)  
  
### 25.3: Learning Objectives
----
By the end of this chapter, you should be able to:
* Grasp the main responsibilities the kernel must fulfill and how it achieves them.
* Explain what parameters can be set on the kernel command line and how to make them effective either for just one system boot, or persistently.
* Know where to find detailed documentation on these parameters.
* Use **sysctl** to set kernel parameters either after the system starts, or persistently across system reboots. 
  
### 25.4: Kernel and Operating System
----
* narrowly defined, Linux is only the **kernel** of the **operating system**
* the kernel connects the hardware to the software, manages resources, handles connected devices using **device drivers**, and makes devices available to the OS
* systems running *only* a kernel can be found in some **embedded devices**
* tuning kernel configurations:
    * kernel **command line** alows specification of **start-up** options
    * **sysctl** allows specification of **runtime** options
  
### 25.5: Main Kernel Tasks
----
The main responsibilities of the kernel include:
* System initialization and boot up
* Process scheduling
* Memory management
* Controlling access to hardware
* I/O (Input/Output) between applications and storage devices
* Implementation of local and network filesystems
* Security control, both locally (such as filesystem permissions) and over the network
* Networking control
  
### 25.6: Kernel Command Line
----
* parameters are passed to the system at boot on the **kernel commmand line**
    * normally found on the `kernel` (or `linux16`) line in the GRUB config file
    * `linux  /boot/vmlinuz-4.19.0 root=UUID=7ef4e747-afae-48e3-90b4-9be8be8d0258 ro quiet \           crashkernel=384M-:128M` sample kernel command line found in `/boot/grub2/grub.cfg`
    * `linux16 /boot/vmlinuz-4.19.4 root=LABEL=RHEL7 LANG=en_US.UTF-8 ro rhgb quiet` another sample kernel command line (also found in GRUB config)
    * everything after `vmlinuz` is an option; options not understood by the kernel are passed to **init** (`pid = 1`)
* `$ cat /proc/cmdline` see what command line a system was booted with
    * my system: `BOOT_IMAGE=/boot/vmlinuz-3.10.0-957.10.1.el7.x86_64 root=UUID=76e6adc1-cbce-4eb4-82b8-0f89f42f82de ro crashkernel=auto rhgb quiet LANG=en_US.UTF-8`
    * `ro` is for mounting the root device read-only on boot
    * `crashkernel` allows Linux to switch to a 'crash kernel' upon panic
  
### 25.7: Kernel Boot Parameters
----
Detailed documentation of the many available parameters can be found at:
* kernel source in the file `Documentation/kernel-parameters.txt`
* [The kernel’s command-line parameters](https://www.kernel.org/doc/html/latest/admin-guide/kernel-parameters.html)
* in kernel documentation package with a name like **kernel-doc** or **linux-doc**
* `$ man bootparam`
  
**Some boot Parameters**
  
Parameter       | Function
---------       | --------
root            | root filesystem
ro              | mounts root device read-only on boot
vconsole.keymap | which keyboard to use on the console
crashkernel     | how much memory to set aside for kernel crashdumps
vconsole.font   | which font to use on the console
rhgb            | for graphical boot 
quiet           | disables most log messages
LANG            | is the system language
  
### 25.8: sysctl
----
* **sysctl** can read and tune kernel parameters at run time
* `$ sysctl -a` shows current values (1046 lines of output on my system!)
* each value corresponds to a pseudofile under `/proc/sys` with slashes instead of dots; the following two statements are equivalent:
    * `$ sudo sh -c 'echo 1 > /proc/sys/net/ipv4/ip_forward'`
    * `$ sudo sysctl net.ipv4.ip_forward=1` sets a value with the **sysctl** interface
* `$ man 8 sysctl` get **sysctl** help
* `$ man 2 sysctl` get information about using the `sysctl()` function from programs
* if settings are place in `/etc/sysctl.conf`, then settings can be fixed at boot time
    * `$ man sysctl.conf` for help
* `$ sudo sysctl -p` causes immediate digestion of the file, setting all parameters as found; this is also part of the boot process
* on **systemd** distributions (like CentOS 7), actual settings are in `/usr/lib/sysctl.d/00-system.conf` but original file is still supported
  
### 25.9: sysctl Demo
----

### Lab 25.1: System Tunables with sysctl
----

### Lab 25.2: Changing the Maximum Process ID
----

### Paths and Commands
----
  
#### Paths  

Topics | Path | Notes | Reference
------ | ---- | ----- | ---------
boot, kernel | `/boot/grub2/grub.cfg` | GRUB configuration file | LFS201 25.6
boot, kernel | `/proc/cmdline` | Linux command line used at boot | LFS201 25.6
boot, kernel | `Documentation/kernel-parameters.txt` | parameter documentation in kernel source | LFS201 25.7
boot, kernel | `/proc/sys` | contains current kernel values | LFS201 25.8
boot, kernel | `/etc/sysctl.conf` | config file allowing settings to be fixed at boot time | LFS201 25.8
boot, kernel | `/usr/lib/sysctl.d/00-system.conf` | settings location on **systemd** distros | LFS201 25.8

#### Commands  

Topics | Command | Notes | Reference
------ | ------- | ----- | ---------
boot, kernel | `linux  /boot/vmlinuz-4.19.0 root=UUID=7ef4e747-afae-48e3-90b4-9be8be8d0258 ro quiet \ crashkernel=384M-:128M` | kernel command line | LFS201 25.6
boot, kernel | `linux16 /boot/vmlinuz-4.19.4 root=LABEL=RHEL7 LANG=en_US.UTF-8 ro rhgb quiet` | kernel command line | LFS201 25.6
boot, kernel | `$ cat /proc/cmdline` | see what command line a system was booted with | LFS201 25.6
boot, kernel | `$ man bootparam` | kernel boot parameter documentation | LFS201 25.7
boot, kernel | `$ sysctl -a` | shows current values | LFS201 25.8
boot, kernel | `$ sudo sh -c 'echo 1 > /proc/sys/net/ipv4/ip_forward'` | sets kernel runtime values | LFS201 25.8
boot, kernel | `$ sudo sysctl net.ipv4.ip_forward=1` | sets kernel runtime values with the **sysctl** interface | LFS201 25.8
boot, kernel | `$ man 8 sysctl` | get **sysctl** help | LFS201 25.8
boot, kernel | `$ man 2 sysctl` | get information about using the `sysctl()` function from programs | LFS201 25.8
boot, kernel | `$ man sysctl.conf` | for **sysctl.conf** help | LFS201 25.8
boot, kernel | `$ sudo sysctl -p` | causes immediate digestion of the file, setting all parameters as found; this is also part of the boot process | LFS201 25.8
