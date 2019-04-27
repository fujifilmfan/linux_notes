Chapter 26: Kernel Modules
--------------------------
  
[26.3: Learning Objectives](#263-learning-objectives)  
[26.4: Advantages of Kernel Modules](#264-advantages-of-kernel-modules)  
[26.5: Module Utilities](#265-module-utilities)  
[26.6: Module Loading and Unloading](#266-module-loading-and-unloading)  
[26.7: modprobe](#267-modprobe)  
[26.8: Some Considerations with Modules](#268-some-considerations-with-modules)  
[26.9: modinfo](#269-modinfo)  
[26.10: Module Parameters](#2610-module-parameters)  
[26.11: Kernel Module Configuration](#2611-kernel-module-configuration)  
[26.12: Kernel Module Demo](#2612-kernel-module-demo)  
[Lab 26.1: Kernel Modules](#lab-261-kernel-modules)  
[Paths and Commands](#paths-and-commands)  
  
### 26.3: Learning Objectives
----
By the end of this chapter, you should be able to:
* List the advantages of utilizing kernel modules.
* Use **insmod**, **rmmod** and **modprobe** to load and unload kernel modules.
* Use **,modinfo**, to find out information about kernel modules. 

### 26.4: Advantages of Kernel Modules
----
* even with the use of kernel modules, Linux has a **monolithic** kernel architecture rather than a **microkernel** one
* Solaris and AIX also use modules

### 26.5: Module Utilities
----
**Utilities Used with Kernel Modules**

Utility      | Function
-------      | --------
**lsmod**    | List loaded modules.
**insmod**   | Directly load modules.
**rmmod**    | Directly remove modules.
**modprobe** | Load or unload modules, using a pre-built module database with dependency and location information.
**depmod**   | Rebuild the **mod**ule **dep**endency database; needed by modprobe and modinfo.
**modinfo**  | Display information about a module.
  
### 26.6: Module Loading and Unloading
----
* `$ lsmod` list loaded modules (113 on my machine currently)
* `$ sudo /sbin/rmmod <module_name>` remove a module
    * the full path name and **.ko** extension are not required
* `$ sudo /sbin/insmod <pathto>/<module_name.ko>` load a module
* `/lib/modules/<kernel-version>` normal filesystem location for kernel modules
    * always have file extensions of **.ko**
    * must match the running kernel version

### 26.7: modprobe
----
* **modprobe** is usually used instead of **insmod** and **rmmod**
    * `$ sudo /sbin/modprobe <module_name>` load a module
    * `$ sudo /sbin/modprobe -r <module_name>` unload a module
* to work, modules must be installed in the proper location, usually under `/lib/modules/$(uname -r)`
    * `$ uname -r` gives the current kernel version (3.10.0-957.10.1.el7.x86_64 on my machine)
    * `$ uname ...` prints system information
* `$ depmod ...` generate or update the file `/lib/modules/$(uname -r)/modules.dep`

### 26.8: Some Considerations with Modules
----
* a module being used by another module cannot be unloaded; **lsmod** can reveal this:
    ```
    Module                  Size  Used by
    ebtable_broute         12731  1 
    bridge                151336  1 ebtable_broute
    stp                    12976  1 bridge
    llc                    14552  2 stp,bridge
    ```
* a module being used by a process cannot be unloaded
* when a module is loaded with **modprobe**, the system will automatically load other modules that need to be loaded first
* when a module is unloaded with **modprobe -r**, the system will automatically unload other modules being used the by module, if they are not being simultaneously used by any other loaded modules

### 26.9: modinfo
----
* `$ /sbin/modinfo <my_module>` find out about kernel modules
* `$ /sbin/modinfo <pathto>/<my_module.ko>` find out about kernel modules
* `/sys/module/<my_module>` also shows information about kernel modules
* `/sys/module/<my_module>/parameters` shows module parameters

### 26.10: Module Parameters
----
`$ sudo /sbin/insmod <pathto>/e1000e.ko debug=2 copybreak=256` load module specifying parameter values
`$ sudo /sbin/modprobe e1000e debug=2 copybreak=256` load module specifying parameter values when the module is in the proper system location

### 26.11: Kernel Module Configuration
----
* `/etc/modprobe.d` has files that control some parameters when loading with **modprobe**, such as module name **aliases** and automatically supplied options
    * can also blacklist specific modules to avoid them being loaded
    * settings apply to modules as they are loaded or unloaded
* format: one command per line; blank lines or those starting with # are ignored; a backslash causes line continuation

### 26.12: Kernel Module Demo
----

### Lab 26.1: Kernel Modules
----

### Paths and Commands
----
  
#### Paths  

Topics | Path | Notes | Reference
------ | ---- | ----- | ---------
kernel, modules | `/lib/modules/<kernel-version>` or `/lib/modules/$(uname -r)` | normal filesystem location for kernel modules | LFS201 26.6
kernel, modules | `/lib/modules/$(uname -r)/modules.dep` | module dependency table | LFS201 26.7
kernel, modules | `/sys/module/<my_module>` | also contains information about kernel modules | LFS201 26.9
kernel, modules | `/sys/module/<my_module>/parameters` | contains module parameters | LFS201 26.9
kernel, modules | `/etc/modprobe.d` | has files that control some parameters when loading with **modprobe** | LFS201 26.11

#### Commands  

Topics | Command | Notes | Reference
------ | ------- | ----- | ---------
kernel, modules | `$ lsmod` | list loaded modules | LFS201 26.6
kernel, modules | `$ sudo /sbin/rmmod <module_name>` | remove a module | LFS201 26.6
kernel, modules | `$ sudo /sbin/insmod <pathto>/<module_name.ko>` | load a module | LFS201 26.6
kernel, modules | `$ sudo /sbin/modprobe -r <module_name>` | unload a module | LFS201 26.7
system | `$ uname -r` | gives the current kernel version  | LFS201 26.7
system | `$ uname ...` | prints system information | LFS201 26.7
kernel, modules | `$ depmod ...` | generate or update the file `/lib/modules/$(uname -r)/modules.dep` | LFS201 26.7
kernel, modules | `$ /sbin/modinfo <my_module>` | find out about kernel modules | LFS201 26.9
kernel, modules | `$ /sbin/modinfo <pathto>/<my_module.ko>` | find out about kernel modules | LFS201 26.9
kernel, modules | `$ sudo /sbin/insmod <pathto>/e1000e.ko debug=2 copybreak=256` | load module specifying parameter values | LFS201 26.10
kernel, modules | `$ sudo /sbin/modprobe e1000e debug=2 copybreak=256` | load module specifying parameter values when the module is in the proper system location | LFS201 26.10
