Chapter 36: Firewalls
---------------------

[36.1: Firewalls](#361-firewalls)  
[36.2: Chapter 36 Introduction](#362-chapter-36-introduction)  
[36.3: Learning Objectives](#363-learning-objectives)  
[36.4: What Is a Firewall?](#364-what-is-a-firewall)  
[36.5: Packet Filtering](#365-packet-filtering)  
[36.6: Firewall Generations](#366-firewall-generations)  
[36.7: Firewall Interfaces and Tools](#367-firewall-interfaces-and-tools)  
[36.8: Why We Are Not Working with iptables](#368-why-we-are-not-working-with-iptables)  
[36.9: firewalld](#369-firewalld)  
[36.10: firewalld Service Status](#3610-firewalld-service-status)  
[36.11: Zones](#3611-zones)  
[36.12: Zone Management](#3612-zone-management)  
[36.13: Source Management](#3613-source-management)  
[36.14: Service Management](#3614-service-management)  
[36.15: Port Management](#3615-port-management)  
[Lab 36.1: Installing firewalld](#lab-361-installing-firewalld)  
[Lab 36.2: Examining firewall-cmd](#lab-362-examining-firewall-cmd)  
[Lab 36.3: Adding Services to a Zone](#lab-363-adding-services-to-a-zone)  
[Lab 36.4: Using the firewall GUI](#lab-364-using-the-firewall-gui)  
[Paths and Commands](#paths-and-commands)  
  
### 36.3: Learning Objectives
----
By the end of this chapter, you should be able to:
* Understand what firewalls are and why they are necessary.
* Know what tools are available both at the command line and using graphical interfaces.
* Discuss about [sic] **firewalld** and the **irewall-cmd** programs.
* Know how to work with **zones**, **sources**, **services** and **ports**.

### 36.4: What Is a Firewall?
----
* a **firewall** is a network security system that monitors and controls all network traffic
    * it applies **rules** on both incoming and outgoing network connections and packets
    * it builds flexible barriers (i.e., firewalls) depending on the level of trust of a given connection
    * can be hardware or software based
    * found both in network routers and individual computers, or network nodes
* many firewalls also have routing capabilities
  
### 36.5: Packet Filtering
----
* almost all firewalls are based on **packet filtering**
    * information is transmitted​ across networks in the form of packets; each packet has:
        * header
        * payload
        * footer
    * the header and footer contain information about destination and source addresses, what kind of packet it is, and which protocol it obeys, various flags, which packet number this is in a stream, and ​all sorts of other metadata about transmissions
    * the actual data is in the payload
* packet filtering intercepts packets at one or more stages in the network transmission, including application, transport, network, and datalink
* a firewall establishes a set of **rules** by which each packet may be:
    * accepted or rejected based on content, address, etc.​
    * mangled in some way
    * redirected to another address
    * inspected for security reasons
  
### 36.6: Firewall Generations
----
* early firewalls were based on **packet filtering**
    * the content of each network packet was inspected and was either dropped, rejected, or sent on
    * no consideration was given about the **connection state**: what stream of traffic the packet was part of
* the next generation of firewalls were based on **stateful filters**, which also examine the **connection state** of the packet, to see if it is a new connection, ​part of an already existing one, or part of none
    * **Denial of service** attacks can bombard this kind of firewall to try and overwhelm it
* the third generation of firewalls is called **Application Layer Firewalls**, and are aware of the kind of application and protocol the connection is using
    * they can block anything which should not be part of the normal flow.
  
### 36.7: Firewall Interfaces and Tools
----
* configuring your system's firewall can be done by:
    * using relatively low-level tools from the command line, combined with editing various configuration files in the `/etc` subdirectory tree:
        * **iptables**
        * **firewall-cmd**
        * **ufw**
    * using robust graphical interfaces:
        * **system-config-firewall**
        * **firewall-config**
        * **gufw**
        * **yast**
* the lower-level tools change less often than the graphical ones, tend to be have a larger set of capabilities, vary little from distribution to distribution
  
### 36.8: Why We Are Not Working with iptables
----
* \[**iptables**\](?) currently interfaces the same kernel firewall implementation code as **firewalld**
* **iptables** requires much more time to get to useful functionality
  
### 36.9: firewalld
----
* **firewalld** is the **Dynamic Firewall Manager**
    * utilizes network/firewall zones which have defined levels of trust for network interfaces or connections
    * supports both IPv4 and IPv6 protocols
    * separates **runtime** and **permanent** (persistent) changes to configuration
    * includes interfaces for services or applications to add firewall rules.
* configuration files are kept in `/etc/firewalld` and `/usr/lib/firewalld`; the files in `/etc/firewalld` override those in the other directory and are the ones a system administrator should work on
* the command line tool is actually **firewall-cmd**
* `$ firewall-cmd --help` has lots of information
* as a service, **firewalld** replaces the older **iptables** - it is an error to run both services, **firewalld** and **iptables**, at the same time
  
### 36.10: firewalld Service Status
----
* **firewalld** is a service which needs to be running to use and configure the firewall, and is enabled/disabled, or started or stopped in the usual way:
    * `$ sudo systemctl [enable/disable] firewalld`
    * `$ sudo systemctl [start/stop] firewalld`
* show the current state in either of the following ways:
    * `$ sudo systemctl status firewalld`
        ```
        firewalld.service - firewalld - dynamic firewall daemon
            Loaded: loaded (/usr/lib/systemd/system/firewalld.service; enabled)
            Active: active (running) since Tue 2015-04-28 12:00:59 CDT; 5min ago
        Main PID: 777 (firewalld)
            CGroup: /system.slice/firewalld.service
                    777 /usr/bin/python -Es /usr/sbin/firewalld --nofork --nopid
        Apr 28 12:00:59 CentOS7 systemd[1]: Started firewalld - dynamic firewall daemon.
        ```
    * `$ sudo firewall-cmd --state`
        > running
* if you have more than one network interface when using IPv4, you have to turn on **ip forwarding**; you can do this at runtime by doing either of:
    * `$ sudo sysctl net.ipv4.ip_forward=1`
    * `$ echo 1 > /proc/sys/net/ipv4/ip_forward`
* this is not persistent; to make persistent, add the following line to `/etc/sysctl.conf`:
    * **net.ipv4.ip_forward=1**
    * and then reboot or type:
    * `$ sudo sysctl -p` to read in the new setting without rebooting
  
### 36.11: Zones
----
* **firewalld** works with **zones**, each of which has a defined level of trust and a certain known behavior for incoming and outgoing packets
* each interface belongs to a particular zone (normally, it is **NetworkManager** which informs **firewalld** which zone is applicable), but this can be changed with **firewall-cmd** or the **firewall-config** GUI
* the zones are:
    * **drop**: all incoming packets are dropped with no reply; only outgoing connections are permitted
    * **block**: all incoming network connections are rejected; the only permitted connections are those from within the system
    * **public**: do not trust any computers on the network; only certain consciously selected incoming connections are permitted
    * **external**: used when masquerading is being used, such as in routers; trust levels are the same as in public
    * **dmz** (**Demilitarized Zone**): used when access to some (but not all) services are to be allowed to the public; only particular incoming connections are allowed
    * **work**: trust (but not completely) connected nodes to be not harmful; only certain incoming connections are allowed
    * **home**: you mostly trust the other network nodes, but still select which incoming connections are allowed
    * **internal**: similar to the work zone
    * **trusted**: all network connections are allowed
* on system installation, most, if not all Linux distributions, will select the **public** zone as default for all interfaces
* do not use a more open zone than necessary
  
### 36.12: Zone Management
----
* `$ sudo firewall-cmd --get-default-zone` get the default zone
* `$ sudo firewall-cmd --get-active-zones` obtain a list of zones currently being used
* `$ sudo firewall-cmd --get-zones` list all available zones
* `$ sudo firewall-cmd --set-default-zone=trusted` change the default zone to **trusted**
* `$ sudo firewall-cmd --set-default-zone=public` change the default zone back to **public**
* `$ sudo firewall-cmd --zone=internal --change-interface=eno1` assign an interface temporarily to a particular zone
* `$ sudo firewall-cmd --permanent --zone=internal --change-interface=eno1` assign an interface to a particular zone permanently
    * creates the file `/etc/firewalld/zones/internal.xml`
* `$ sudo firewall-cmd --get-zone-of-interface=eno1` ascertain the zone associated with a particular interface
* `$ sudo firewall-cmd --zone=public --list-all` get all details about a particular zone
public (default, active)
  
### 36.13: Source Management
----
* any zone can be bound not just to a network interface, but also to particular network addresses; a packet is associated with a zone if:
    * it comes from a source address already bound to the zone; or if not,
    * it comes from an interface bound to the zone
* any packet not fitting the above criteria is assigned to the default zone
* to assign a source to a zone (permanently):
    * `$ sudo firewall-cmd --permanent --zone=trusted --add-source=192.168.1.0/24` assign anyone with an IP address of 192.168.1.x to the **trusted** zone
    * remove a previously assigned source from a zone by using the **--remove-source option**, or change the zone by using **--change-source**
* list the sources bound to a zone with:
    * `$ sudo firewall-cmd --permanent --zone=trusted --list-sources`
* in both of the above commands, leaving out the **--permanent** option will result in changing only the current runtime behavior
  
### 36.14: Service Management
----
* so far, we have assigned particular interfaces and/or addresses to zones, but we haven't delineated what **services** and **ports** should be accessible within a zone
* `$ sudo firewall-cmd --get-services` see all the services available
* `$ sudo firewall-cmd --list-services --zone=public` see services currently accessible in a particular zone
* `$ sudo firewall-cmd --permanent --zone=home --add-service=dhcp` add a service to a zone
* `$ sudo firewall-cmd --reload` reload firewall rules and keep state information. Current permanent configuration will become new runtime configuration
* `/etc/firewalld/services` add new services by editing this
  
### 36.15: Port Management
----
* port management is very similar to service management:
    * `$ sudo firewall-cmd --zone=home --add-port=21/tcp`
    * `$ sudo firewall-cmd --zone=home --list-ports`
* `/etc/services` stores information about services on the machine, including name, port number, protocol, and aliases; a mapping between human-friendly textual names for internet services, and their underlying assigned port numbers and protocol types; every networking program should look into this file to get the port number (and protocol) for its service
* `$ grep " 21/tcp" /etc/services` ascertain that port 21 corresponds to ftp
  
### Lab 36.1: Installing firewalld
----
* `$ which firewalld firewall-cmd`
    ```
    /usr/sbin/firewalld
    /usr/bin/firewall-cmd
    ```
* nothing more to do!
  
### Lab 36.2: Examining firewall-cmd
----
* `$ firewalld-cmd --help`
* nothing more to do
  
### Lab 36.3: Adding Services to a Zone
----
Add the http and https services to the public zone and verify that they are currently listed.  
Solution:  
* `$ sudo firewall-cmd --zone=public --add-service=http`
    > success  
* `$ sudo firewall-cmd --zone=public --add-service=https`
    > success  
* `$ sudo firewall-cmd --list-services --zone=public`
    > dhcpv6-client http https ssh  
* Note if you had run
    * `$ sudo firewall-cmd --reload`
    * `$ sudo firewall-cmd --list-services --zone=public`
        > dhcpv6-client ssh  
    after adding the new services, they would disappear from the list! This curious behavior is because we did not include the **--permanent** flag when adding the services, and the **--reload** option reloads the known persistent services only.
  
### Lab 36.4: Using the firewall GUI
----
Once you launch the firewall configuration GUI, do the previous exercise of adding http and https to the public zone, and
verify that it has taken effect.
* `$ firewall-config` start GUI
* I removed the **http** and **https** services
* verify using `$ sudo firewall-cmd --list-services --zone=public`
  
### Paths and Commands
----
  
#### Paths  

Topics | Path | Notes | Reference
------ | ---- | ----- | ---------
networks, security | `/etc/firewalld` | configuration files for **firewalld**; override those in `/usr/lib/firewalld` | LFS201 36.9
networks, security | `/usr/lib/firewalld` | configuration files for **firewalld** `/etc/firewalld` | LFS201 36.9
networks, security | `/etc/firewalld/zones/internal.xml` | created when assigning an interface to a particular zone permanently | LFS201 36.12
networks, system | `/etc/services` | stores information about services on the machine, including name, port number, protocol, and aliases; a mapping between human-friendly textual names for internet services, and their underlying assigned port numbers and protocol types; every networking program should look into this file to get the port number (and protocol) for its service | LFS201 36.15
networks, security | `/etc/firewalld/services` | add new services by editing this | LFS201 36.14
  
#### Commands  

Topics | Command | Notes | Reference
------ | ------- | ----- | ---------
networks, security | `$ firewall-cmd --help` | has lots of information | LFS201 36.9
networks, security | `$ sudo systemctl [enable/disable] firewalld` | start **firewalld** | LFS201 36.10
networks, security | `$ sudo systemctl [start/stop] firewalld` | stop **firewalld** | LFS201 36.10
networks, security | `$ sudo systemctl status firewalld` | shows the current state of **firewalld** | LFS201 36.10
networks, security | `$ sudo firewall-cmd --state` | shows the current state of **firewalld** | LFS201 36.10
networks, security | `$ sudo sysctl net.ipv4.ip_forward=1` | turn on **ip forwarding** | LFS201 36.10
networks, security | `$ echo 1 > /proc/sys/net/ipv4/ip_forward` | turn on **ip forwarding** | LFS201 36.10
networks, security | 1 `/etc/sysctl.conf` | add **net.ipv4.ip_forward=1** to make **ip forwarding** persistent | LFS201 36.10
networks, security | 2 `$ sudo sysctl -p` | to read in the new setting without rebooting (or just reboot) | LFS201 36.10
networks, security | `$ sudo firewall-cmd --get-default-zone` | get the default zone | LFS201 36.12
networks, security | `$ sudo firewall-cmd --get-active-zones` | obtain a list of zones currently being used | LFS201 36.12
networks, security | `$ sudo firewall-cmd --get-zones` | list all available zones | LFS201 36.12
networks, security | `$ sudo firewall-cmd --set-default-zone=trusted` | change the default zone to **trusted** | LFS201 36.12
networks, security | `$ sudo firewall-cmd --set-default-zone=public` | change the default zone back to **public** | LFS201 36.12
networks, security | `$ sudo firewall-cmd --zone=internal --change-interface=eno1` | assign an interface temporarily to a particular zone | LFS201 36.12
networks, security | `$ sudo firewall-cmd --permanent --zone=internal --change-interface=eno1` | assign an interface to a particular zone permanently, which creates `/etc/firewalld/zones/internal.xml` | LFS201 36.12
networks, security | `$ sudo firewall-cmd --get-zone-of-interface=eno1` | ascertain the zone associated with a particular interface | LFS201 36.12
networks, security | `$ sudo firewall-cmd --zone=public --list-all` | get all details about a particular zone public (default, active) | LFS201 36.12
networks, security | `$ sudo firewall-cmd --permanent --zone=trusted --add-source=192.168.1.0/24` | assign anyone with an IP address of 192.168.1.x to the **trusted** zone | LFS201 36.13
networks, security | `$ sudo firewall-cmd --permanent --zone=trusted --list-sources` | list the sources bound to a zone | LFS`201 36.13
networks, security | `$ sudo firewall-cmd --get-services` | see all the services available | LFS201 36.14
networks, security | `$ sudo firewall-cmd --list-services --zone=public` | see services currently accessible in a particular zon`e | LFS201 36.14
networks, security | `$ sudo firewall-cmd --permanent --zone=home --add-service=dhcp` | add a service to a zone | LFS201 36.14
networks, security | `$ sudo firewall-cmd --reload` | reload firewall rules and keep state information. Current permanent con`figuration will become new runtime configuration | LFS201 36.14
networks, security | `$ sudo firewall-cmd --zone=home --add-port=21/tcp` | add TCP port to home zone services | LFS201 36.15
networks, security | `$ sudo firewall-cmd --zone=home --list-ports` | check ports for home zone | LFS201 36.15
networks, security | `$ grep " 21/tcp" /etc/services` | ascertain that port 21 corresponds to ftp | LFS201 36.15
networks, security | `$ firewall-config` | start firewall GUI | LFS201 Lab 36.4
