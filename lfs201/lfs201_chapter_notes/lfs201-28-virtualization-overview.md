Chapter 28: Virtualization Overview
-----------------------------------
  
[28.3: Learning Objectives](#283-learning-objectives)  
[28.4: What Is Virtualization?](#284-what-is-virtualization)  
[28.5: Virtualization History](#285-virtualization-history)  
[28.6: Hosts and Guests](#286-hosts-and-guests)  
[28.7: Emulators](#287-emulators)  
[28.8: Emulation vs: Virtualization](#288-emulation-vs-virtualization)  
[28.9: Types of Virtualization Hypervisors](#289-types-of-virtualization-hypervisors)  
[28.10: External and in-Kernel Hypervisors](#2810-external-and-in-kernel-hypervisors)  
[28.11: Dedicated Hypervisor](#2811-dedicated-hypervisor)  
[28.12: Hypervisor in the Kernel](#2812-hypervisor-in-the-kernel)  
[28.13: libvirt](#2813-libvirt)  
[28.14: Programs Using libvirt](#2814-programs-using-libvirt)  
[28.15: What Is QEMU?](#2815-what-is-qemu)  
[28.16: Third Party Hypervisor Integration](#2816-third-party-hypervisor-integration)  
[28.17: Image Formats](#2817-image-formats)  
[28.18: KVM and Linux](#2818-kvm-and-linux)  
[28.19: Managing KVM](#2819-managing-kvm)  
[Lab 28.1: Ensuring KVM Is Properly Set Up](#lab-281-ensuring-kvm-is-properly-set-up)  
[Lab 28.2: Using virt-manager with KVM to Install a VM and Run It](#lab-282-using-virt-manager-with-kvm-to-install-a-vm-and-run-it)  
[Lab 28.3: Installing a VM from the Command Line](#lab-283-installing-a-vm-from-the-command-line)  
[Paths and Commands](#paths-and-commands)  
  
### 28.3: Learning Objectives
----
By the end of this chapter, you should be able to:
* Understand the concepts behind **virtualization** and how it came to be widely used.
* Understand the roles of **hosts** and **guests**.
* Discuss the difference between **emulation** and **virtualization**.
* Distinguish between the different types of **hypervisors**.
* Be familiar with how Linux distributions use and depend on **libvirt**.
* Use the qemu hypervisor.
* Install, use and manage KVM.
  
### 28.4: What Is Virtualization?
----
* virtual Machines are a virtualized instance of an entire operating system, and may fulfill the role of a server or a desktop/workstation
* the outside world sees the VM as if it were an actual physical machine, present somewhere on the network; applications running in the VM are generally unaware of their non-physical environment
* other kinds of virtualization:
    * **Network**: the details of the actual physical network, such as the type of hardware, routers, etc. are abstracted and need not be known by software running on it and configuring it: Software Defined Networking or SDN
    * **Storage**: multiple network storage devices are configured to look like one big storage unit, such as a disk: Network Attached Storage or NAS.
    * **Application**: an application is isolated into a standalone format, such as a container, which will be discussed later.
  
### 28.5: Virtualization History
----
  
### 28.6: Hosts and Guests
----
* **host**: underlying physical operating system managing the VM(s)
* **guest** (or **client**): VM that is an instance of a complete OS, running one or more applications
* guests can be migrated from one host to another, sometimes while running
* a host can be migrated into a VM
* tuning:
    * performance tuning on host
    * application tuning on guest
  
### 28.7: Emulators
----
* on an emulator, an application running on the current operating system would appear to another OS as a specific hardware environment
* Qemu is an emulator
  
### 28.8: Emulation vs. Virtualization
----
* an emulator runs completely in software
* useful for running VMs on different architectures
* often used for developing an OS for a new CPU before hardware is available
* performance relatively slow
  
### 28.9: Types of Virtualization Hypervisors
----
* **hypervisors**, also known as **Virtual Machine Monitors (VMM)**, initiate, terminate, and manage guests
* two types:
    * **hardware virtualization** (**full virtualization**): guest system unaware it is virtualized
    * **para-virtualization**: guest system aware it is virtualized
* article: [Xen and the new processors](https://lwn.net/Articles/182080/)
* `/proc/cpuinfo` has CPU information, including virtualization abilities
    * **vmx** flag for IVT-capable chip (Intel)
    * **svm** flag for AMD-V capable chip
  
### 28.10: External and in-Kernel Hypervisors
----
* the hypervisor can be:
    * external to the host operating system kernel: VMware
    * internal to the host operating system kernel: KVM
* KVM is open-source and does not require external third-party hypervisor programs
  
### 28.11: Dedicated Hypervisor
----
* dedicated hypervisor: merging of the hypervisor program into a specially-designed lightweight kernel(?)
  
### 28.12: Hypervisor in the Kernel
----
* KVM added hypervisor capabilities into the Linux kernel, leveraging the facilities of the kernel and enabling the kernel to be a hypervisor
* specific CPU chip functions and facilities required for this
  
### 28.13: libvirt
----
* **libvirt** is a toolkit to manage virtualization platforms
* supported hypervisors:
    * QEMU/KVM
    * Xen
    * Oracle VirtualBox
    * VMware ESX
    * VMware Workstation/Player
    * Microsoft Hyper-V
    * IBM PowerVM (phyp)
    * OpenVZ
    * UML (User Mode Linux)
    * LXC (Linux Containers)
    * Virtuozzo
    * Bhyve (The BSD Hypervisor)
    * Test (Used for testing)
  
### 28.14: Programs Using libvirt
----
* `$ ls -lF /usr/bin/virt*` see which utilities are using *libvirt* (?); my machine:
    ```
    -rwxr-xr-x 1 root root 28120 Mar 14 03:25 /usr/bin/virt-host-validate*
    -rwxr-xr-x 1 root root    59 Oct 30 10:03 /usr/bin/virt-manager*
    -rwxr-xr-x 1 root root  9809 Mar 14 03:25 /usr/bin/virt-pki-validate*
    -rwxr-xr-x 1 root root  2343 Mar 14 03:25 /usr/bin/virt-xml-validate*
    ```
  
### 28.15: What Is QEMU?
----
* QEMU stands for **Q**uick **EMU**lator
    * written by Fabrice Bellard in 2002
    * emulates CPUs by dynamically translating binary instructions between the host architecture and the emulated one
    * can also be used to emulate particular applications instead of entire OSs
* used with **KVM** (**K**ernel **V**irtual **M**achine), it can reach speeds close to that of the host
* used in developing processors not physically produced or released to market
  
### 28.16: Third Party Hypervisor Integration
----
* third party hypervisors:
    * KVM, native to Linux, works well with QEMU
    * Xen, native to Linux
    * Oracle Virtual Box, close relationship with QEMU
  
### 28.17: Image Formats
----
* main two disk image file formats supported by QEMU:
    * **raw** (default)
    * **qcow2** (COW for Copy On Write)
* `$ qemu-img --help | grep formats:` get list of supported formats; my machine:
    ```
    Supported formats: vvfat vpc vmdk vhdx vdi ssh sheepdog rbd raw host_cdrom host_floppy host_device file qed qcow2 qcow parallels nbd iscsi gluster dmg tftp ftps ftp https http cloop bochs blkverify blkdebug
    ```
    * **vdi** used by Oracle Virtual Box
    * **vmdc** used by VMware
* `$ qemu-img convert -O vmdk myvm.qcow2 myvm.vmdk` translate between disk image file formats
  
### 28.18: KVM and Linux
----
* "Shortly after Avi Kivity, the author of KVM, submitted the source code to the Linux development community, Linus merged KVM into his Linux tree. This was surprising to a lot of folks."

### 28.19: Managing KVM
----
No notes  
  
### Lab 28.1: Ensuring KVM Is Properly Set Up
----

### Lab 28.2: Using virt-manager with KVM to Install a VM and Run It
----

### Lab 28.3: Installing a VM from the Command Line
----

### Paths and Commands
----
  
#### Paths  

Topics | Path | Notes | Reference
------ | ---- | ----- | ---------
virtualization | `/proc/cpuinfo` has CPU information, including virtualization abilities | LFS201 28.9

#### Commands  

Topics | Command | Notes | Reference
------ | ------- | ----- | ---------
virtualization | `$ ls -lF /usr/bin/virt*` | see which utilities are using *libvirt* (?) | LFS201 28.14
virtualization | `$ qemu-img --help | grep formats:` | get list of supported formats | LFS201 28.17
virtualization | `$ qemu-img convert -O vmdk myvm.qcow2 myvm.vmdk` | translate between disk image file formats | LFS201 28.17
