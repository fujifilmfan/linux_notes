Chapter 27: Devices and udev
----------------------------

[27.3: Learning Objectives](#273-learning-objectives)  
[27.4: Device Nodes](#274-device-nodes)  
[27.5: Major and Minor Numbers](#275-major-and-minor-numbers)  
[27.6: udev](#276-udev)  
[27.7: udev Components](#278-udev-components)  
[27.8: udev and Hotplug](#279-udev-and-hotplug)  
[27.9: The udev Device Manager](#279-the-udev-device-manager)  
[27.10: udev Rule Files](#2710-udev-rule-files)  
[27.11: Creating udev Rules](#2711-creating-udev-rules)  
[27.12: Some Examples of Rules Files](#2712-some-examples-of-rules-files)  
[27.13: Using udev Demo](#2713-using-udev-demo)  
[Lab 27.1: udev](#lab-271-udev)  
[Paths and Commands](#paths-and-commands)  
  
### 27.3: Learning Objectives
----
By the end of this chapter, you should be able to:
* Explain the role of **device nodes** and how they use **major** and **minor** numbers.
* Understand the need for the **udev** method and list its key components.
* Describe how the **udev** device manager functions.
* Identify **udev** rule files and learn how to create custom rules.
  
### 27.4: Device Nodes
----
From [Device file](https://en.wikipedia.org/wiki/Device_file):
* "**character special files** or **character devices** provide unbuffered, direct access to the hardware device"
    * "sometimes known as **raw devices** to avoid the confusion surrounding the fact that a character device for a piece of block-based hardware will typically require programs to read and write aligned blocks"
* "**block special files** or **block devices** provide buffered access to hardware devices, and provide some abstraction from their specifics"
    * "unlike character devices, block devices will always allow the programmer to read or write a block of any size (including single characters/bytes) and any alignment"
    * Linux creates only block devices (?)
* character and block devices have filesystem entries associated with them; network devices in Linux do not
    * these **device nodes** can be used by programs to communicate with devices, using normal I/O system calls such as **open()**, **close()**, **read()**, and **write()**
    * network devices work by transmitting and receiving **packets**, constructed by breaking up streams of data and reassembled into streams when received
* `/dev` holds device nodes
* `$ sudo mknod [-m mode] /dev/name <type> <major> <minor>` generic syntax to create a device node
* `$ sudo mknod -m 666 /dev/mycdrv c 254 1` creating a device node example
  
### 27.5: Major and Minor Numbers
----
* the **major** and **minor** numbers identify the driver associated with the device, with the driver uniquely reserving a group of numbers
* in most cases (but not all), device nodes of the same type (block or character) with the same major number use the same driver
* example:
    * `$ ls -l /dev/sda*`
        ```
        brw-rw---- 1 root disk 8, 0 Apr 25 11:04 /dev/sda
        brw-rw---- 1 root disk 8, 1 Apr 25 11:04 /dev/sda1
        brw-rw---- 1 root disk 8, 2 Apr 25 11:04 /dev/sda2
        ```
    * **8** is the major number, and **0, 1, 2** the minor numbers
* the **minor** number is used by the device driver to differentiate between different devices it may control or how they are used
    * may either be different instances of the same kind of device (like first and second sound card or hard disk partition) or 
    * different modes of operation of a given device (such as different density floppy drive media)
* **mknod()** and **stat()** return information about major and minor numbers
    * `$ stat /dev/sda1`
        ```    
        File: ‘/dev/sda1’
        Size: 0         	Blocks: 0          IO Block: 4096   block special file
        Device: 5h/5d	Inode: 21288       Links: 1     Device type: 8,1
        Access: (0660/brw-rw----)  Uid: (    0/    root)   Gid: (    6/    disk)
        Access: 2019-04-25 11:04:47.393000391 -0700
        Modify: 2019-04-25 11:04:47.393000391 -0700
        Change: 2019-04-25 11:04:47.393000391 -0700
        Birth: -
        ```
  
### 27.6: udev
----
* managing device nodes became clumsy in the 2.4 kernel version series with >10,000 nodes in `/dev`
* ideally, names would be used instead of numbers, but the numbers are required by **POSIX**
* **udev** creates device nodes on the fly when they are needed, so no need to maintain a huge directory of device nodes
    * **u** is for **user**; most of the work is done in user-space
    * replaced **devfs** and **hotplug**
    * features **persistent device naming**, not based on the order of device connection; controlled by **udev rules**
  
### 27.7: udev Components
----
* **udev** runs as a daemon (either **udevd** or **systemd-udevd**) and monitors a **netlink** socket
* when a device is added or removed, the **uevent** kernel facility sends a message through the socket to **udev**, which takes appropriate action
* three components of **udev**
    1. **libudev** library that allows access to info about the devices
    2. **udevd** or **systemd-udevd** daemon that manages the `/dev` directory
    3. **udevadm** uitility for control and diagnostics
* best to start with a pure system in which `/dev` is empty upon initial kernel boot and nodes are only added as needed
    * one must boot using an **initramfs** image, which may contain a set of preliminary device notes as well as the **udev** infrastructure
  
### 27.8: udev and Hotplug
----
* the information needed to create nodes with the right names, numbers, permissions, etc. is registered in the **sysfs** pseudo-filesystem mounted at `/sys` and a set of config files
* `/etc/udev/udev.conf` is the main configuration file with info on where to place nodes, default permissions, etc.
* `/etc/udev/rules.d` and `/usr/lib/udev/rules.d` hold rules for naming
  
### 27.9: The udev Device Manager
----
* **udev**  parses `/etc/udev/rules.d/*.rules` and `/usr/lib/udev/rules.d/*.rules` when it gets a message from the kernel about devices being added or removed
* actions include:
    * device node naming
    * device node and symbolic links creation
    * setting file permissions and ownership for the device node
    * taking other actions to initialize and make device available
* the rules are completely customizable
  
### 27.10: udev Rule Files
----
* by default, when udev reads the rules files it looks for files that have a suffix of **.rules**
* if it finds more than one file, it reads them one by one, lexicographically
  
### 27.11: Creating udev Rules
----
* format for a **udev** rule: `<match><op>value [, ...] <assignment><op>value [, ... ]`
    * the first part consists of one or more match pairs denoted by == ; these try to match a device's attributes and/or characteristics to some value
    * the second part consists of one or more assignment key-value pairs that assign a value to a name, such as a file name, group assignment, even file permissions, etc.
* if no matching rule is found, it uses the default device node name and other attributes
  
### 27.12: Some Examples of Rules Files
----
* `$ cat /usr/lib/udev/rules.d/99-fitbit.rules`
    ```
    SUBSYSTEM=="usb", ATTR{idVendor}=="2687", ATTR{idProduct}=="fb01", SYMLINK+="fitbit", MODE="0666"
    ```
* `$ cat /usr/lib/udev/rules.d/98-kexec.rules`
* `$ cat /usr/lib/udev/rules.d/80-kvm.rules`
* `$ cat /usr/lib/udev/rules.d/99-fuse.rules`
  
### 27.13: Using udev Demo
----

### Lab 27.1: udev
----
1. Create and implement a rule on your system that will create a symlink called `myusb` when a **USB** device is plugged in.

2. Plug in a **USB** device to your system. It can be a pen drive, mouse, webcam, etc.
Note: If you are running a virtual machine under a hypervisor, you will have to make sure the **USB** device is seen by the
guest, which usually is just a mouse click which also disconnects it from the host.
3. Get a listing of the `/dev` directory and see if your symlink was created.
    * yes: `myusb -> bus/usb/001/004`
    * `$ lsusb`
        > Bus 001 Device 004: ID 05ac:12a8 Apple, Inc. iPhone5/5C/5S/6  
4. Remove the **USB** device. (If it is a drive you should always **umount** it first for safety.)
    * I couldn't find it on the system to **umount** it, so the phone restarted when I unplugged it.
5. See if your symbolic link still exists in `/dev`.
    * it's gone
  
### Paths and Commands
----
  
#### Paths  

Topics | Path | Notes | Reference
------ | ---- | ----- | ---------
devices | `/dev` | holds device nodes | LFS201 27.4
devices | `/sys` | contains **sysfs** pseudo-filesystem, which has device node information | LFS201 27.8
devices | `/etc/udev/udev.conf` | main configuration file with info on where to place nodes, default permissions, etc. | LFS201 27.8
devices | `/etc/udev/rules.d` and `/usr/lib/udev/rules.d` | hold rules for naming device nodes | LFS201 27.8
  
#### Commands  

Topics | Command | Notes | Reference
------ | ------- | ----- | ---------
devices | `$ sudo mknod [-m mode] /dev/name <type> <major> <minor>` | generic syntax to create a device node | LFS201 27.4
devices | `$ sudo mknod -m 666 /dev/mycdrv c 254 1` | creating a device node example | LFS201 27.4
devices | `$ ls -l /dev/sda*` | view major and minor numbers for sda* devices | LFS201 27.5
devices | `$ stat /dev/sda1` | returns device information for `/dev/sda1` | LFS201 27.5
devices | `$ cat /usr/lib/udev/rules.d/99-fitbit.rules` | read rules for a Fitbit device, for example | LFS201 27.12
  