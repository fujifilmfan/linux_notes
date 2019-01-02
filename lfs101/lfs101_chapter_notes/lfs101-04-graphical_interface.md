Chapter 4: Graphical Interface
-----

[4.0: Introduction/ Learning Objectives](#40-introduction-learning-objectives)  
[4.1: Graphical Desktop](#41-graphical-desktop)  
[4.2: Session Management](#42-session-management)  
[4.3: Basic Operations](#43-basic-operations)  
[4.4: Summary](#44-summary)  

### 4.0: Introduction/ Learning Objectives
By the end of this chapter, you should be able to:
  * Manage graphical interface sessions.
  * Perform basic operations using the graphical interface.
  * Change the graphical desktop to suit your needs.

### 4.1: Graphical Desktop

#### Introduction
* **CLI**: Command Line Interface
* **GUI**: Graphical User Interface

#### X Window System
* loaded as final step in boot process
* **display manager** service keeps track of provided displays, loads the **X server**, handles graphical logins, starts desktop environment
* **X server** provides graphical services to application, sometimes called **X clients**
* old, so might be replaced by **Wayland**, already adopted in **Fedora 25**
* three components of the desktop environment:
  * session manager: starts and maintains the components of the graphical session
  * window manager: controls placement and movement of windows, window title-bars, and controls
  * utilities

#### GUI Startup
* **X** display manager:
  * starts at the end of the boot process
  * responsible for starting graphics system
  * logging in the user
  * starting the user's desktop environment
* default display manager for **GNOME** is called **gdm** (**lightdm** used on Ubuntu, **kdm** associated with KDE)

#### GNOME Desktop Environment
* GNOME bundled as default for RHEL, Fedora, CentOS, SUSE Linux Enterprise, and Debian
* KDE also a common desktop environment, often used in SUSE and openSUSE
* other alternatives: **Unity** (from Ubuntu, based on GNOME), **XFCE**, and **LXDE**

#### Graphical Desktop Background
#### Customizing the Desktop Background
#### Changing the Desktop Background in CentOS
#### Changing the Desktop Background in openSUSE
#### Changing the Desktop Background in Ubuntu
#### Try-It-Yourself: Changing the Desktop Background
#### gnome-tweak-tool
* exposes many more setting options than the default settings utility
* can also be used to add third-party extenstions
* use **Alt-F2** to enter a command

#### Changing the Theme
* can do this via settings or gnome-tweak-tool
* this link doesn't seem to work anymore: http://art.gnome.org/themes/

### Lab 4.1: Customizing the Desktop

### 4.2: Session Management
### 4.3: Basic Operations
### 4.4: Summary