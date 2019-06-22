Chapter 35: Network Devices and Configuration
---------------------------------------------

[35.3: Learning Objectives](#353-learning-objectives)  
[35.4: Network Devices](#354-network-devices)  
[35.5: ip](#355-ip)  
[35.6: Examples of Using ip](#356a-examples-of-using-ip)  
[35.7: ifconfig](#357-ifconfig)  
[35.8: Problems with Network Device Names](#358-problems-with-network-device-names)  
[35.9: Predictable Network Interface Device Names](#359-predictable-network-interface-device-names)  
[35.10: Examples of the New Naming Scheme](#3510-examples-of-the-new-naming-scheme)  
[35.11: NIC Configuration Files](#3511-nic-configuration-files)  
[35.12: Network Manager](#3512-network-manager)  
[35.13: Network Manager Interfaces](#3513-network-manager-interfaces)  
[35.14: nmtui](#3514-nmtui)  
[35.15: nmtui Wireless Configuration](#3515-nmtui-wireless-configuration)  
[35.16: nmcli](#3516-nmcli)  
[35.17: Routing](#3517-routing)  
[35.18: Default Route](#3518-default-route)  
[35.19: Static Routes](#3519-static-routes)  
[35.20: Name Resolution](#3520-name-resolution)  
[35.21: /etc/hosts](#3521-etchosts)  
[35.22: DNS](#3522-dns)  
[35.23: Network Diagnostics](#3523-network-diagnostics)  
[35.24: ping Example](#3524-ping-example)  
[35.25: traceroute Example](#3525-traceroute-example)  
[35.26: mtr Example](#3526-mtr-example)  
[35.27: Knowledge Check](#3527-knowledge-check)  
[Lab 35.1: Static Configuration of a Network Interface](#lab-351-static-configuration-of-a-network-interface)  
[Lab 35.2: Adding a Static Hostname](#lab-352-adding-a-static-hostname)  
[Lab 35.3: Adding a Network Interface Alias/Address Using nmcli](#lab-353-adding-a-network-interface-aliasaddress-using-nmcli)  
[Lab 35.4: Adding a Static Route Using nmcli](#lab-354-adding-a-static-route-using-nmcli) 
[Paths and Commands](#paths-and-commands)  
  
### 35.3: Learning Objectives
----
By the end of this chapter, you should be able to:
* Identify network devices and understand how the operating system names them and binds them to specific duties.
* Use the **ip** utility to display and control devices, routing, policy-based routing, and tunnelling. 
* Use the older **ifconfig** to configure, control, and query network interface parameters from either the command line or from system configuration scripts.
* Understand the Predictable Network Interface Device Names scheme.
* Know the main network configuration files in `/etc`.
* Use **Network Manager** (**nmtui** and **nmcli**) to configure network interfaces in a distribution-independent manner.  
* Know how to set default routes and static routes.
* Configure name resolution as well as run diagnostic utilities.
  
### 35.4: Network Devices
----
* unlike block and character devices, network devices are not associated with **special device files**, also known as **device nodes**
* rather than having associated entries in the `/dev` directory, they are known by their names
* these names consist of a type identifier followed by a number as in:
    * `eth0`, `eth1`, `eno1`, `eno2`, etc. for ethernet devices
    * `wlan0`, `wlan1`, `wlan2`, `wlp3s0`, `wlp3s2`, etc. for wireless devices
    * `br0`, `br1`, `br2`, etc. for bridge interfaces
    * `vmnet0`, `vmnet1`, `vmnet2`, etc. for virtual devices for communicating with virtual clients; historically, multiple virtual devices could be associated with single physical devices
  
### 35.5: ip
----
* **ip** is used to configure, control and query interface parameters and control devices, routing, etc.
* it is preferred to **ifconfig** as it is more versatile as well as more efficient because it uses **netlink** sockets rather than **ioctl** system calls
* `$ ip [ OPTIONS ] OBJECT { COMMAND | help }` general syntax
* `$ ip [ -force ] -batch filename` read and execute commands from FILENAME (**-batch**); don't terminate ip on errors in batch mode (**-force**)
* **ip** is a multiplex utility; the `OBJECT` argument describes what kind of action is going to be performed; the possible `COMMANDS` depend on which `OBJECT` is selected

**Main ip `OBJECT`s**

`OBJECT` | Function
-------- | --------
address  | **IPv4** or **IPv6** protocol device address
link     | network devices
maddress | multicast address
monitor  | watch for netlink messages
route    | routing table entry
rule     | rule in the routing policy database
tunnel   | tunnel over IP
  
### 35.6: Examples of Using ip
----
* example usages of **ip**:
    * `$ ip link show` show information for all network interfaces
    * `$ ip -s link show eth0` show information for the `eth0` network interface, including statistics
    * `$ sudo ip addr add 192.168.1.7 dev eth0` set the IP address for `eth0`
    * `$ sudo ip link set eth0 down` bring `eth0` down 
    * `$ sudo ip link set eth0 mtu 1480` set the **MTU** to 1480 bytes for `eth0`
    * `$ sudo ip route add 172.16.1.0/24 via 192.168.1.5` set the networking route
  
### 35.7: ifconfig
----
* **ifconfig** is a system administration utility long found in UNIX-like operating systems used to configure, control, and query network interface parameters from either the command line or from system configuration scripts
* it has been superseded by **ip**
* example usages of **ifconfig**:
    * `$ ifconfig` display information about all interfaces
    * `$ ifconfig eth0` display information about only `eth0`
    * `$ sudo ifconfig eth0 192.168.1.50` set the IP address to 192.168.1.50 on interface `eth0`
    * `$ sudo ifconfig eth0 netmask 255.255.255.0` set the **netmask** to 24-bit
    * `$ sudo ifconfig eth0 up` bring interface `eth0` up
    * `$ sudo ifconfig eth0 down` bring interface `eth0` down
    * `$ sudo ifconfig eth0 mtu 1480` set the **MTU** (**M**aximum **T**ransfer **U**nit) to 1480 bytes for interface `eth0`
  
### 35.8: Problems with Network Device Names
----
* classic device naming conventions described earlier encountered difficulties, particularly when multiple interfaces of the same type were present
* probing for devices is not deterministic for modern systems, and devices may be located or plugged in an unpredictable order
* many system administrators have solved this problem in a simple manner, by hardcoding associations between hardware (MAC) addresses and device names in system configuration files and startup scripts, but that requires manual tuning and has other problems, such as when MAC addresses are not fixed
  
### 35.9: Predictable Network Interface Device Names
----
* the **Predictable Network Interface Device Names** (**PNIDN**) is strongly correlated with the use of **udev** and integration with **systemd**
* there are now 5 types of names that devices can be given:
    1. incorporating **Firmware** or **BIOS** provided index numbers for on-board devices, such as `eno1`
    2. incorporating **Firmware** or **BIOS** provided **PCI Express** hotplug slot index numbers, such as `ens1`
    3. incorporating physical and/or geographical location of the hardware connection, such as `enp2s0`
    4. incorporating the MAC address, such as `enx7837d1ea46da`
    5. using the old classic method, such as `eth0`
  
### 35.10: Examples of the New Naming Scheme
----
* on a machine with two onboard PCI network interfaces that would have been `eth0` and `eth1`:
    * `$ ip link show | grep enp`
    ```
    2: enp4s2: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc pfifo_fast state DOWN mode DEFAULT qlen 1000
    3: enp2s0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP mode DEFAULT qlen 1000
    ```
* for a wireless device that previously would have been simply named `wlan0`:
    * `$ ip link show | grep wl`
    ```
    3: wlp3s0: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc mq state UP mode DORMANT qlen 1000
    ```
  
### 35.11: NIC Configuration Files
----
* **NIC** stands for **N**etwork **I**nterface **C**ard
* **ip** or **ifconfig** settings made on the fly are not persistent
* thus, Linux distribution-dependent files store persistent network interface and device configuration information
* Red Hat
    * `/etc/sysconfig/network`
    * `/etc/sysconfig/network-scripts/ifcfg-ethX`
    * `/etc/sysconfig/network-scripts/ifcfg-ethX:Y`
    * `/etc/sysconfig/network-scripts/route-ethX`
* Debian
    * `/etc/network/interfaces`
* SUSE
    * `/etc/sysconfig/network`
* on **systemd** systems, it is preferable to use Network Manager rather than directly configuring these files; in fact, in new Linux distributions, many of these files are non-existent, empty or much smaller and are there only for backward compatibility reasons
  
### 35.12: Network Manager
----
* network connections used to be mostly wired (and static), so files in `/etc` were read at system boot; that doesn't work as much anymore as networks and devices change, and there can be a large choice of networks to connect to
* **Network Manager** still uses config files, but the administrator can avoid directly manipulating them, and its use is hopefully almost the same on different systems
  
### 35.13: Network Manager Interfaces
----
* **GUI**: you can use this to select between different networks, configure security and passwords, turn devices off and on, etc
* **nmtui**: if you are making a configuration change on your system that is likely to last for a while, you are likely to use **nmtui** as it has almost no learning curve and will edit the underlying configuration files for you
* **nmcli**: if you need to run scripts that change the network configuration, you will want to use **nmcli**; or, if you are a command line junkie, you may want to use this instead of **nmtui**
* any of these will work, but the latter two are essentially distribution independent and hide any differences in underlying configuration files
  
### 35.14: nmtui
----
**nmtui** is the **N**etwork **M**anager **T**ext **U**ser **I**nterface
* `$ nmtui` activates **nmtui** interface; 
* see "35.14 - nmtui-edit.png" and "35.14 - nmtui-main.png"
  
### 35.15: nmtui Wireless Configuration
----
* see "35.15 - nmtui-config.png"
  
### 35.16: nmcli 
----
* **nmcli** is the **N**etwork **M**anager **C**ommand **L**ine **I**nterface
* examples can be found at [Networking/CLI](https://fedoraproject.org/wiki/Networking/CLI) or by typing `$ man nmcli-examples`
* `$ nmcli` gives some device and connection information
* `$ nmcli device show` show detailed information about devices
* `$ nmcli connection show` list in-memory and on-disk connection profiles
* `$ nmcli device status` print status of devices
  
### 35.17: Routing
----
* **routing** is the process of selecting paths in a network along which to send network traffic
* the **routing table** is a list of routes to other networks managed by the system; it defines paths to all networks and hosts, sending remote traffic to routers
* `$ route -n` show current routing table; **-n** is used to show numerical addresses instead of trying to determine symbolic host names, useful if you are trying to determine why the route to your nameserver has vanished; mine:
    ```
    Kernel IP routing table
    Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
    default         gateway         0.0.0.0         UG    100    0        0 ens33
    192.168.1.0     0.0.0.0         255.255.255.0   U     100    0        0 ens33
    192.168.122.0   0.0.0.0         255.255.255.0   U     0      0        0 virbr0
    ```
* `$ ip route` show current routing table
  
### 35.18: Default Route
----
* the **default route** is the way packets are sent when there is no other match in the routing table for reaching the specified network
    * it can be obtained dynamically using DHCP
    * it can also be manually configured (static)
* to manually configure the route using **nmcli**:
    * `$ sudo nmcli con mod virbr0 ipv4.routes 192.168.10.0/24 +ipv4.gateway 192.168.122.0`
    * `$ sudo nmcli con up virbr0`
* to manually configure the route directly on Red Hat-based systems, one can modify `/etc/sysconfig/network` putting in the line **GATEWAY=x.x.x.x** or alternatively in `/etc/sysconfig/network-scripts/ifcfg-ethX` on a device-specific basis
* on Debian-based systems, it would be **gateway=x.x.x.x** in `/etc/network/interfaces`
* it can be set at runtime with:
    * `$ sudo route add default gw 192.168.1.10 enp2s0` but will not be persistent
    * `$ route`
        ```
        Kernel IP routing table
        Destination   Gateway      Genmask       Flags Metric Ref Use Iface
        default       192.168.1.10 0.0.0.0       UG    0      0     0 enp2s0
        default       192.168.1.1  0.0.0.0       UG    1024   0     0 enp2s0
        172.16.132.0  0.0.0.0      255.255.255.0 U     0      0     0 vmnet1
        192.168.1.0   0.0.0.0      255.255.255.0 U     0      0     0 enp2s0
        192.168.113.0 0.0.0.0      255.255.255.0 U     0      0     0 vmnet8
        ```
    * `$ sudo route add default gw 192.168.1.1 enp2s0` to restore the default in the above example
  
### 35.19: Static Routes
----
* **static routes** are used to control packet flow when there is more than one router or route; they are defined for each interface and can be either persistent or non-persistent
* when the system can access more than one router, or perhaps there are multiple interfaces, it is useful to selectively control which packets go to which router
* either the **route** or **ip** command can be used to set a non-persistent route as in: 
    * `$ sudo ip route add 10.5.0.0/16 via 192.168.1.100`
    * `$ route`
        ```
        Destination  Gateway      Genmask        Flags Metric Ref Use Iface
        default      192.168.1.1  0.0.0.0        UG    0      0     0 eth0
        10.5.0.0     quad         255.255.0.0    UG    0      0     0 eth0
        192.168.1.0  *            255.255.255.0  U     1      0     0 eth0
        ```
* on a Red Hat-based system, a persistent route can be set by editing `/etc/sysconfig/network-scripts/route-ethX` as shown by: 
    * `$ cat /etc/sysconfig/network-scripts/route-eth0`
        > 10.5.0.0/16 via 172.17.9.1
* on a Debian-based system you need to add lines to `/etc/network/interfaces` such as:
    ```
    iface eth1 inet dhcp
        post-up route add -host 10.1.2.51 eth1
        post-up route add -host 10.1.2.52 eth1
    ```
* on a SUSE-based system you need to add to or create a file such as `/etc/sysconfig/network/ifroute-eth0` with lines like:
    ```
    # Destination Gateway Netmask Interface [Type] [Options]

    192.168.1.150 192.168.1.1 255.255.255.255 eth0
    10.1.1.150 192.168.233.1.1 eth0
    10.1.1.0/24 192.168.1.1 - eth0
    ```
    where each field is separated by tabs
  
### 35.20: Name Resolution
----
* **name resolution** is the act of translating hostnames to the IP addresses of their hosts
    * for example, a browser or email client will take "training.linuxfoundation.org" and resolve the name to the IP address of the server (or servers) that serve it in order to transmit to and from that location
* there are two facilities for doing this translation:
    * **static name resolution** (using `/etc/hosts`)
    * **dynamic name resolution** (using **DNS** servers)
* there are several command line tools that can be used to resolve the IP address of a hostname:
    * `$ dig linuxfoundation.org` resolves IP address; generates the most information and has many options
    * `$ host linuxfoundation.org` resolves IP address; more compact
    * `$ nslookup linuxfoundation.org` resolves IP address; older
* **host** and **nslookup** are sometimes considered deprecated, but the output for host is the easiest to read and contains the basic information
* **reverse resolution** converts an IP address to a host name
    * didn't work on my VM for 23.185.0.2
  
### 35.21: /etc/hosts
----
* `/etc/hosts` holds a local database of hostnames and IP addresses; it contains a set of records (each taking one line) which map IP addresses with corresponding hostnames and aliases
* a typical /`etc/hosts` file looks like:
    * `$ cat /etc/host`
        ```
        127.0.0.1     localhost localhost.localdomain localhost4 localhost4.localdomain4
        ::1           localhost localhost.localdomain localhost6 localhost6.localdomain6

        192.168.1.100 hans hans7 hans64
        192.168.1.150 bethe bethe7 bethe64
        192.168.1.2 hp-printer
        192.168.1.10 test32 test64 oldpc
        ```
* such static name resolution is primarily used for local, small, isolated networks
    * it is generally checked before DNS is attempted to resolve an address
    * this priority can be controlled by `/etc/nsswitch.conf`
* the other host-related files in `/etc` are `/etc/hosts.deny` and `/etc/hosts.allow`
    * `allow` file searched first and the `deny` file only if query not found in `allow`
* `/etc/host.conf` contains general configuration information; it is rarely used
  
### 35.22: DNS
----
* if name resolution cannot be done locally using `/etc/hosts`, then the system will query a **DNS** (**D**omain **N**ame **S**erver) server
* DNS is dynamic and consists of a network of servers which a client uses to look up names
* the machine's usage of DNS is configured in `/etc/resolv.conf`, which historically has looked like:
    ```
    search example.com aps.org
    nameserver 192.168.1.1
    nameserver 8.8.8.8
    ```
* the `/etc/resolv.conf` file:
    * can specify particular domains to search
    * defines a strict order of nameservers to query
    * may be manually configured or updated from a service such as **DHCP** (**D**ynamic **H**ost **C**onfiguration **P**rotocol)
    * is automatically generated on most modern systems, in the following example, by NetworkManager invoking DHCP (Dynamic Host Configuration Protocol) on the primary network interface
        ```
        # Generated by NetworkManager
        nameserver 192.168.1.1
        ```
  
### 35.23: Network Diagnostics
----
* **ping**: sends 64-byte test packets to designated network hosts and (if it finds them) tries to report back on the time required to reach it (in milliseconds), any lost packets, and some other parameters
    * `$ ping -c 10 linuxfoundation.org`
* **traceroute**: displays a network path to a destination; it shows the routers packets flow through to get to a host, as well as the time it takes for each hop
    * `$ traceroute linuxfoundation.org`
* **mtr** (**M**y **Tr**aceroute): combines the functionality of ping and traceroute and creates a continuously updated display, like top
    * `$ mtr linuxfoundation.org`
* **dig**: useful for testing DNS functionality
  
### 35.24: ping Example
----
Image  
  
### 35.25: traceroute Example
----
Image  
  
### 35.26: mtr Example
----
Image  
  
### 35.27: Knowledge Check
----
* Your workstation is connected to the local network of Acme Company. On the local network, there are other workstations, a DNS server, and a web server for the domain http://intranet.acme.com. Internet connection is also provided for the local network.
    * Removing the gateway configuration from your workstation **has no** impact on accessing resources on the local network.
    * Removing the gateway configuration from your workstation **has** an impact on accessing resources on the Internet.
    * Removing the DNS configuration from your workstation **has** an impact on accessing resources on the local network.
    * Removing the DNS configuration from your workstation **has** an impact on accessing resources on the Internet.
    * Adding an entry to `/etc/hosts` **can** enable your workstation to access http://intranet.acme.com when the DNS server is not working.
  
### Lab 35.1: Static Configuration of a Network Interface
----
1. Show your current IP address, default route and **DNS** settings for eth0 . Keep a copy of them for resetting later.
    * `$ ip addr show ens33`
        ```
        2: ens33: <BROADCAST,MULTICAST> mtu 1500 qdisc pfifo_fast state DOWN group default qlen 1000
        link/ether 00:0c:29:0b:9a:b4 brd ff:ff:ff:ff:ff:ff
        ```
    * `$ ip route`
        ```
        172.17.0.0/16 dev docker0 proto kernel scope link src 172.17.0.1 
        192.168.122.0/24 dev virbr0 proto kernel scope link src 192.168.122.1
        ```
    * `$ cp /etc/resolv.conf resolv.conf.keep` - the file has only one, commented line
2. Bring down `ens33` and reconfigure to use a static address instead of **DCHP**, using the information you just recorded.
    * `$ sudo ip link set ens33 down`
    * might need to edit `/etc/sysconfig/network-scripts/ifcfg-eth0`:
        ```
        DEVICE=eth0
        BOOTPROTO=static
        ONBOOT=yes
        IPADDR=noted from step 1
        NETMASK=noted from step 1
        GATEWAY=noted from step 1
        ```
3. Bring the interface back up, and configure the nameserver resolver with the information that you noted before. Verify your hostname and then **ping** it.
    * `$ sudo ip link set ens33 up`
    * `$ sudo cp resolv.conf.keep /etc/resolv.conf`
    * `$ cat /etc/sysconfig/network` - the file has only one, commented line
    * `$ cat /etc/hosts`
    * `$ ping yourhostname`
4. Make sure your configuration works after a reboot.
    * why? this didn't seem to change anything
    * `$ sudo reboot`
    * `$ ping hostname`
  
### Lab 35.2: Adding a Static Hostname
----
1. Open `/etc/hosts` and add an entry for `mysystem.mydomain` that will point to the IP address associated with your network card.
2. Add a second entry that will make all references to ad.doubleclick.net point to 127.0.0.1 .
3. As an optional exercise, download the host file from: http://winhelp2002.mvps.org/hosts2.htm or more directly from http://winhelp2002.mvps.org/hosts.txt , and install it on your system. Do you notice any difference using your browser with and without the new host file in place?

Solution:
```
1. $ sudo sh -c "echo 192.168.1.180
mysystem.mydomain >> /etc/hosts"
$ ping mysystem.mydomain
2. $ sudo sh -c "echo 127.0.0.1
ad.doubleclick.net >> /etc/hosts"
$ ping ad.doubleclick.net
3. $ wget http://winhelp2002.mvps.org/hosts.txt
--2014-11-01 08:57:12-- http://winhelp2002.mvps.org/hosts.txt
Resolving winhelp2002.mvps.org (winhelp2002.mvps.org)... 216.155.126.40
Connecting to winhelp2002.mvps.org (winhelp2002.mvps.org)|216.155.126.40|:80... connected.
HTTP request sent, awaiting response... 200 OK
Length: 514744 (503K) [text/plain]
Saving to: hosts.txt
100%[======================================>] 514,744
977KB/s
in 0.5s
2014-11-01 08:57:13 (977 KB/s) - hosts.txt saved [514744/514744]
$ sudo sh -c "cat hosts.txt >> /etc/hosts"
```
  
### Lab 35.3: Adding a Network Interface Alias/Address Using nmcli
----

### Lab 35.4: Adding a Static Route Using nmcli
----

### Paths and Commands
----
  
#### Paths  

Topics | Path | Notes | Reference
------ | ---- | ----- | ---------
networks | `/etc/sysconfig/network` | stores persistent NIC configs on Red Hat | LFS201 35.11
networks | `/etc/sysconfig/network-scripts/ifcfg-ethX` | stores persistent NIC configs on Red Hat | LFS201 35.11
networks | `/etc/sysconfig/network-scripts/ifcfg-ethX:Y` | stores persistent NIC configs on Red Hat | LFS201 35.11
networks | `/etc/sysconfig/network-scripts/route-ethX` | stores persistent NIC configs on Red Hat | LFS201 35.11
networks | `/etc/network/interfaces` | stores persistent NIC configs on Debian | LFS201 35.11
networks | `/etc/sysconfig/network` | stores persistent NIC configs on SUSE | LFS201 35.11
networks | `/etc/sysconfig/network`, add **GATEWAY=x.x.x.x** | manually configure the default route directly on Red Hat-based systems | LFS201 35.18
networks | `/etc/sysconfig/network-scripts/ifcfg-ethX`, add **GATEWAY=x.x.x.x** | manually configure the default route directly on Red Hat-based systems on a device-specific basis | LFS201 35.18
networks | `/etc/network/interfaces`, add **gateway=x.x.x.x** | manually configure the default route directly on Debian-based systems | LFS201 35.18
networks | `/etc/sysconfig/network-scripts/route-ethX` | edit this to set a persistent route on a Red Hat-based system | LFS201 35.19
networks | `/etc/network/interfaces` | edit this to set a persistent route on a Debian-based system | LFS201 35.19
networks | `/etc/sysconfig/network/ifroute-eth0` | edit this to set a persistent route on a SUSE-based system |  LFS201 35.19
networks | `/etc/hosts` | used for static name resolution | LFS201 35.20
networks | `/etc/hosts` | holds a local database of hostnames and IP addresses; maps IP addresses with corresponding hostnames and aliases | LFS201 35.21
networks | `/etc/nsswitch.conf` | used to set DNS priority | LFS201 35.21
networks | `/etc/hosts.deny` | searched after `allow` | LFS201 35.21
networks | `/etc/hosts.allow` | searched first | LFS201 35.21
networks | `/etc/host.conf` | contains general configuration information; rarely used | LFS201 35.21
networks | `/etc/resolv.conf` | configures machine's usage of DNS | LFS201 35.22
  
#### Commands  

Topics | Command | Notes | Reference
------ | ------- | ----- | ---------
networks | `$ ip ...` | used to configure, control and query interface parameters and control devices, routing, etc. | LFS201 35.5
networks | `$ ip [ OPTIONS ] OBJECT { COMMAND | help }` | general syntax | LFS201 35.5
networks | `$ ip [ -force ] -batch filename` | read and execute commands from FILENAME (**-batch**); don't terminate ip on errors in batch mode (**-force**) | LFS201 35.5
networks | `$ ip link show` | show information for all network interfaces | LFS201 35.6
networks | `$ ip -s link show eth0` | show information for the `eth0` network interface, including statistics | LFS201 35.6
networks | `$ sudo ip addr add 192.168.1.7 dev eth0` | set the IP address for `eth0` | LFS201 35.6
networks | `$ sudo ip link set eth0 down` | bring `eth0` down  | LFS201 35.6
networks | `$ sudo ip link set eth0 mtu 1480` | set the **MTU** to 1480 bytes for `eth0` | LFS201 35.6
networks | `$ sudo ip route add 172.16.1.0/24 via 192.168.1.5` | set the networking route | LFS201 35.6
networks | `$ ifconfig` | display information about all interfaces | LFS201 35.7
networks | `$ ifconfig eth0` | display information about only `eth0` | LFS201 35.7
networks | `$ sudo ifconfig eth0 192.168.1.50` | set the IP address to 192.168.1.50 on interface `eth0` | LFS201 35.7
networks | `$ sudo ifconfig eth0 netmask 255.255.255.0` | set the **netmask** to 24-bit | LFS201 35.7
networks | `$ sudo ifconfig eth0 up` | bring interface `eth0` up | LFS201 35.7
networks | `$ sudo ifconfig eth0 down` | bring interface `eth0` down | LFS201 35.7
networks | `$ sudo ifconfig eth0 mtu 1480` | set the **MTU** to 1480 bytes for interface `eth0` | LFS201 35.7
networks | `$ nmtui` | activates **nmtui** (NetworkManager Text User Interface) | LFS201 35.13
networks | `$ nmcli ...` | NetworkManager Command Line Interface | LFS201 35.13
networks | `$ man nmcli-examples` | get examples of **nmcli** use | LFS201 35.16
networks | `$ nmcli` | gives some device and connection information | LFS201 35.16
networks | `$ nmcli device show` | show detailed information about devices | LFS201 35.16
networks | `$ nmcli connection show` | list in-memory and on-disk connection profiles | LFS201 35.16
networks | `$ nmcli device status` | print status of devices | LFS201 35.16
networks | `$ route -n` | shows current routing table; **-n** is used to show numerical addresses instead of  symbolic host names | LFS201 35.17
networks | `$ ip route` | shows current routing table | LFS201 35.17
networks | `$ sudo nmcli con mod virbr0 ipv4.routes 192.168.10.0/24 +ipv4.gateway 192.168.122.0` | manually configure the default route | LFS201 35.18
networks | `$ sudo nmcli con up virbr0` | manually configure the default route | LFS201 35.18
networks | `$ sudo route add default gw 192.168.1.10 enp2s0` | set default route at runtime | LFS201 35.18
networks | `$ sudo route add default gw 192.168.1.1 enp2s0` | restore the default in the above example | LFS201 35.18
networks | `$ sudo ip route add 10.5.0.0/16 via 192.168.1.100` | set a non-persistent route | LFS201 35.19
networks | `$ dig linuxfoundation.org` | resolves IP address; generates the most information and has many options | LFS201 35.20
networks | `$ host linuxfoundation.org` | resolves IP address; more compact | LFS201 35.20
networks | `$ nslookup linuxfoundation.org` | resolves IP address; older | LFS201 35.20
networks | `$ ping -c 10 linuxfoundation.org` | sends test packets to network hosts | LFS201 35.23
networks | `$ traceroute linuxfoundation.org` | displays a network path to a destination | LFS201 35.23
networks | `$ mtr linuxfoundation.org` | combines the functionality of ping and traceroute and creates a continuously updated display | LFS201 35.23
  