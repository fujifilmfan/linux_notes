Chapter 29: Containers Overview
-------------------------------

[29.3: Learning Objectives](#293-learning-objectives)  
[29.4: Container Basics](#294-container-basics)  
[29.5: Application Virtualization](#295-application-virtualization)  
[29.6: Containers vs. Virtual Machines](#296-containers-vs-virtual-machines)  
[29.7: Docker](#297-docker)  
[29.8: Docker Steps](#298-docker-steps)  
[29.9: docker Command](#299-docker-command)  
[Lab 29.1: Installing and Testing Apache (httpd)](#lab-291-installing-and-testing-apache-httpd)  
[Paths and Commands](#paths-and-commands)  
  
### 29.3: Learning Objectives
----
By the end of this chapter, you should be able to:
* Understand the basic parameters and methods that define containers.
* Understand the difference between containers and virtual machines.
* Be familiar with Docker and know the steps necessary to use it properly.
* Use the major commands associated with containers and Docker.
  
### 29.4: Container Basics
----
* containers are operating system-level virtual machines
* they make use of kernel additions such as **namespaces** and **cgroups**
* **OS containers** run an image of an operating system with the ability to run **init** processes and spawn multiple applications
* LXC (Linux Containers) is one example
  
### 29.5: Application Virtualization
----
* application virtualization runs one application for each container
* Docker is an example
  
### 29.6: Containers vs. Virtual Machines
----
* virtual machines:
    * a virtual machine functioning as a service may be the best solution if a number of different services and applications need to be tightly integrated
    * may be best if the applications being run were written expecting a complete operating system environment with a wide range of services and other libraries and applications
    * run a complete operating system, and can run many services and applications
    * use more resources than a container
    * each container has its own OS kernel
* containers:
    * usually run one thing
    * more portable
    * can be run inside a VM
    * harder to secure
    * usually start faster
    * multiple containers share one OS kernel
  
### 29.7: Docker
----
* Docker uses many individual images to build up the necessary services to support the target application
* images are packaged into container that may contain:
    * application code
    * runtime libraries
    * system tools
* an application can be packaged up with all of its dependent code and services and deployed as a single unit with the minimum of overhead
  
### 29.8: Docker Steps
----
starting a Docker application:
1. install the Docker service package with your favorite tool
2. start the Docker service
3. search for an appropriate image from Docker Hub or your private repository
4. pull the image
5. run the image
6. finally, test the application
  
### 29.9: docker Command
----
* the **docker** command has more than 40 sub-commands, some with 50 or more options
* Docker is already installed on my VM
  
### Lab 29.1: Installing and Testing Apache (httpd)
----

### Paths and Commands
----
  
#### Paths  

Topics | Path | Notes | Reference
------ | ---- | ----- | ---------


#### Commands  

Topics | Command | Notes | Reference
------ | ------- | ----- | ---------
