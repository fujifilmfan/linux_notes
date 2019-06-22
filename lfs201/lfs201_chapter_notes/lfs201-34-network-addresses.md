Chapter 34: Network Addresses
-----------------------------

[34.3: Learning Objectives](#343-learning-objectives)  
[34.4: IP Addresses](#344-ip-addresses)  
[34.5: IPv4 Address Types](#345-ipv4-address-types)  
[34.6: Reserved Addresses](#346-reserved-addresses)  
[34.7: IPv6 Address Types](#347-ipv6-address-types)  
[34.8: IPv4 Address Classes](#348-ipv4-address-classes)  
[34.9: Netmasks](#349-netmasks)  
[34.10: Hostname](#3410-hostname)  
[34.11: Getting and Setting the Hostname](#3411-getting-and-setting-the-hostname)  
[34.12: Knowledge Check 32.3](#3412-knowledge-check-323)  
[Lab](#lab)  
[Paths and Commands](#paths-and-commands)  
  
### 34.3: Learning Objectives
----
By the end of this chapter, you should be able to:
* Differentiate between different types of IPv4 and IPv6 addresses.
* Understand the role of netmasks.
* Get, set, and change the hostname, based on the system you are using.
  
### 34.4: IP Addresses
----
**IP addresses** uniquely identify nodes on the internet; two varieties:  
* **IPv4**: 32-bit address of 4 **octets** (an octet is 8 bits), like 148.114.252.10
* **IPv6**: 128-bit address of 8 16-bit octet pairs, like 2003:0db5:6123:0000:1f4f:0000:5529:fe23
  
### 34.5: IPv4 Address Types
----
IPv4 address types include:
* **Unicast**: an address associated with a specific host; it might be something like 140.211.169.4 or 64.254.248.193
* **Network**: an address whose **host** portion is set to all binary zeroes, like 192.168.1.0 (the host portion can be the last 1-3 octets; here it is just the last octet)
* **Broadcast**: an address to which each member of a particular network will listen; it will have the host portion set to all 1 bits, such as in 172.16.255.255 or 148.114.255.255 or 192.168.1.255 (the host portion is the last two octets in the first two cases, just the last one in the third case)
* **Multicast**: an address to which appropriately configured nodes will listen; the address 224.0.0.2 is an example of a multicast address; only nodes specifically configured to pay attention to a specific multicast address will interpret packets for that multicast group
  
### 34.6: Reserved Addresses
----
Some reserved addresses:
* **127.x.x.x**: reserved for the loopback (local system) interface, where 0  <=  x  <=  254; generally 127.0.0.1
* **0.0.0.0**: used by systems that do not yet know their own address; protocols like DHCP and BOOTP use this address when attempting to communicate with a server
* **255.255.255.255**: generic broadcast private address, reserved for internal use; these addresses are never assigned or registered to anyone; they are generally not routable
* Other examples of reserved address ranges include (each has a purpose):
    * 10.0.0.0 - 10.255.255.255
    * 172.16.0.0 - 172.31.255.255
    * 192.168.0.0 - 192.168.255.255 (used only for local communications within a private network)
    * More here at Wikipedia: [Reserved IP addresses](https://en.wikipedia.org/wiki/Reserved_IP_addresses)

### 34.7: IPv6 Address Types
----
IPv6 address types include:  
* **Unicast**: a packet is delivered to one interface  
    * **Link-local**:  auto-configured for every interface to have one; non-routable  
    * **Global**:  dynamically or manually assigned; routable  
    * Reserved for documentation (? that's a type?)  
* **Multicast**: a packet is delivered to multiple interfaces  
* **Anycast**: a packet is delivered to the nearest of multiple interfaces (in terms of routing distance)  
* **IPv4-mapped**: an IPv4 address mapped to IPv6; for example, ::FFFF:a.b.c.d/96  
In addition, IPv6 has some special types of addresses such as loopback, which is assigned to the `lo` interface, as `::1/128`.  
  
### 34.8: IPv4 Address Classes
----
* historically, IP addresses are based on defined **classes**
* classes A, B, and C distinguish a network portion of the address from a host portion of the address
* this is used for routing purposes

**Address Classes**

Network Class | Highest order octet range | Notes
------------- | ------------------------- | -----
A             | 1-127                     | 128 networks, 16,772,214 hosts per network, 127.x.x.x reserved for loopback
B             | 128-191                   | 16,384 networks, 65,534 hosts per network
C             | 192-223                   | 2,097,152 networks, 254 hosts per network
D             | 224-239                   | Multicast addresses
E             | 240-254                   | Reserved address range
  
### 34.9: Netmasks
----
* the **netmask** is used to determine how much of the address is used for the network portion and how much for the host portion
* it is also used to determine network and broadcast addresses

**Address Classes and Netmasks**

Network Class | Decimal       | Hex         | Binary                              | Notes
------------- | -------       | ---         | ------                              | -----
A             | 255.0.0.0     | ff:00:00:00 | 11111111 00000000 00000000 00000000 | 8 bits for the network and 24 bits for the host
B             | 255.255.0.0   | ff:ff:00:00 | 11111111 11111111 00000000 00000000 | 16 bits for the network and 16 for the host
C             | 255.255.255.0 | ff:ff:ff:00 | 11111111 11111111 11111111 00000000 | 24 for the network and 8 for the host
D             |               |             |                                     | used for multicasting
E             |               |             |                                     | not currently used

* network address is obtained by **anding** (logical and - &) the IP address with the netmask; example:
    ```
    172.16.2.17   ip address
    &255.255.0.0  netmask
    ------------
    172.16.0.0    network address
    ```  
* the network addresses define a local network which consists of a collection of nodes connected via the same media and sharing the same network address
* all nodes on the same network can directly see each other
  
### 34.10: Hostname
----
* the **hostname** is simply a label used to identify a networked device to distinguish it from other elements on the network
* historically, this has also been called a nodename
* for DNS purposes, hostnames are appended with a period (dot) and a domain name, so that a machine with a hostname of **antje** could have a **fully qualified domain name (FQDN)** of **antje.linuxfoundation.org**
* the hostname is generally specified at installation time, and can be modified at any time later
  
### 34.11: Getting and Setting the Hostname
----
* `$ hostname` gives current hostname
    * the Mac: "Kurts-iMac.local"
    * the VM: "centos"
* `$ sudo hostname lumpy` changes the hostname to "lumpy"
    * will revert to previous value after reboot
    * to make the change persistent, change config files in the `/etc` directory tree, such as by using **hostnameectl** (from **systemd** infrastructure)
* `$ sudo hostnamectl set-hostname MYPC` changes the hostname persistently to "MYPC"; the "pretty" hostname is not used by most distros
* `/etc/hostname` holds current value on most distros; one could edit this as root to change the name
  
### 34.12: Knowledge Check 32.3
----
**192.168.128.255** is a valid **IP** address for a host on a local network if the mask is configured to **255.255.255.0**. True or False?  False  
? Why?  
  
### Lab
----
No lab this chapter  

### Paths and Commands
----
  
#### Paths  

Topics | Path | Notes | Reference
------ | ---- | ----- | ---------
networks | `/etc/hostname` holds current value on most distros | LFS201 34.11
  
#### Commands  

Topics | Command | Notes | Reference
------ | ------- | ----- | ---------
networks | `$ hostname` gives the current hostname | LFS201 34.11
networks | `$ sudo hostname lumpy` changes the hostname to "lumpy" | LFS201 34.11
networks | `$ sudo hostnamectl set-hostname MYPC` changes the hostname persistently to "MYPC" distros | LFS201 34.11
