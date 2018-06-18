LF101 Introduction to Linux Notes
=====

Chapter 3: Linux Basics and System Startup
-----

## Notes 3

### Introduction/ Learning Objectives
By the end of this chapter, you should be able to:
* Identify Linux filesystems.
* Identify the differences between partitions and filesystems.
* Describe the boot process.
* Install Linux on a computer.

### Section 1: The Boot Process
the Linux **boot process** is the procedure for initializing the system; everything that happens from power on until the UI is fully operational
1. **BIOS** (**Basis Input/Output System**)
  * initializes hardware, including screen and keyboard, and tests main memory (**POST** or **POwer On Self Test**)
  * BIOS software stored on ROM chip on motherboard
  * after this, boot process controlled by OS
2. **MBR** (**Master Boot Record**)
  * after **POST** control passes from **BIOS** to the boot loader
  * boot loader usually stored on hard disk in the boot sector (for traditional **BIOS/MBR** systems; first sector of the hard disk, known as **MBR**; MBR is 512 bytes) or **EFI** partition (for more recent **(Unified) Extensible Firmware Interface** or **EFI/UEFI** systems)
  * up to this stage, machine does not access any mass storage media, so information on date, time, and most important peripherals are loaded from the **CMOS values**
  * Linux boot loaders:
  	* **GRUB** (**GR**and **U**nified **B**oot loader)
  	* **ISOLINUX** (for booting from removable media)
  	* **DAS U-Boot** (for booting on embedded devices/appliances)
  * boot loader responsible for loading the kernel image and the intial RAM disk or filesystem into memory
3. Boot Loader in Action
  * First stage:
    * MBR systems:
      * boot loader examines partition table and finds a bootable partition
      * then searches for second stage boot loader like **GRUB** and loads it into **RAM**
    * EFI/UEFI method:
    	* UEFI firmware reads its **Boot Manager** data to determine with UEFI application to launch and from where (from which disk and partition the EFI partition can be found)
    	* then launches the UEFI application like **GRUB**
    	* more complicated but more versatile than the older MBR methods
  * Second stage:
    * second stage boot loader resides under `/boot`
    * a splash screen is displayed, which allows us to choose which OS to boot
    * after selection, boot loader loads the kernel of the selected OS into RAM and passes control to it
  * kernel's first job is usually to uncompress itself
  * then kernel will check and analyze the system harware and initialize any hardware device drivers built into the kernel
4. Initial RAM Disk
  * the **initramfs** filesystem image contains programs and binary files that perform all actions needed to mount the proper root filesystem
  * provides device drivers for mass storage controllers with a facility called **udev** (for **U**ser **Device**), which figures out devices present, **drivers** they need, and loading them
  * root filesystem found, checked for errors, and mounted
  * the **mount** program instructs OS that FS is ready for use and associates it with the **mount point**, a particular point in the overall heirarchy of the FS
  ? What is an example of a mount point?
  * if success, **initramfs** is cleared from RAM and the **init** program on the root FS (`/sbin/init`) is executed
  * **init** handles mounting and pivoting to final real root FS; needed special hardware drivers must be in **initramfs** image
5. Text-Mode Login
  * text-mode login prompts, which enable you to type your username and password, are started by **init** (will not see these with a GUI at first)
  * most distros start six text terminals and one graphics terminal starting with **F1** or **F2**
  	* access using **ALT** plus a **function** key
  	* within a graphical environment, switch to text console with **CTRL-ALT +** the appropriate function key (**F7** or **F1** lead to the GUI)
  ? What is a 'terminal' in this context?
  * **bash** (the **GNU Bourne Again Shell**) is the usual, default command shell


### Section 2: Kernel, init, and Services
### Section 3: Linux Filesystem Basics
### Section 4: Linux Distribution Installation
### Summary

Chapter 4: Graphical Interface
-----
### Introduction/ Learning Objectives
### Section 1: Graphical Desktop
### Section 2: Session Management
### Section 3: Basic Operations
### Summary

Chapter 5: System Configuration from the Graphical Interface
-----
### Introduction/ Learning Objectives
### Section 1: System, Display, Date and Time Settings
### Section 2: Network Manager
### Section 3: Installing and Updating Software
### Summary