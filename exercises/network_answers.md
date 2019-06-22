Networks
-----

### Chapters: LFS101: 14, LFS201: 34, 35, 36

 

### Name resolution tools
* show the system's hostname - `$ hostname`
* change the system's hostname - `$ sudo hostname LFstudent`
* change the system's hostname persistently - `$ sudo hostnamectl set-hostname LFstudent`
* view hosts file - `$ cat /etc/hosts`  
* show local nameserver - `$ cat /etc/resolv.conf`  
* resolve linuxfoundation.org - `$ host linuxfoundation.org` or `$ nslookup linuxfoundation.org` or `$ dig linuxfoundation.org`

### ip and route
* show the kernel IP routing table (address of each node in the network) with route - `$ route -n` or `$ route`
* show the kernel IP routing table with netstat - `$ netstat -r`
* view routing info with ip - `$ /sbin/ip route show` or `$ ip route`
* add a static route - `$ route add -net address` or `$ ip route add`
* delete a static route - `$ route del -net address` or `$ ip route del`
* show information about a network interface - `$ ethtool ens33`
#### ping
* ping localhost - `$ ping <hostname>`  


#### More Networking Tools
Networking Tools | Description  
---------------- | -----------  
netstat          | Displays all active connections and routing tables. Useful for monitoring performance and troubleshooting.  
nmap             | Scans open ports on a network. Important for security analysis.  
tcpdump          | Dumps network traffic for analysis.  
iptraf           | Monitors network traffic in text mode.  


* copy a file from your account to another user's account using scp - `$ scp <localfile> <user@remotesystem>:/home/user>`  
* use ssh to delete the file - `$ ssh some_system my_command`  
* login to another user's account using ssh - `$ ssh -l someone some_system` or `$ ssh someone@some_system`


#### Network troubleshooting

Troubleshooting network problems is something that you will often encounter if you haven't already. We are going to practice some of the previously discussed tools, that can help you isolate, troubleshoot and fix problems in your network.

Suppose you need to perform an Internet search, but your web browser can not find google.com, saying the host is unknown. Let's proceed step by step to fix this.
* is the network properly configured? - `$ ifconfig` (should see "Ethernet")
* what if the device was not up? - restart the network and/or NetworkManager: `$ sudo systemctl restart NetworkManager` or `$ sudo systemctl restart network`
* does your IP address look valid? - `$ /sbin/ip addr show` (look at "inet", most likely Class C)
* what if the device was up but had no IP? - restarting the network / NetworkManager should do it; if not, get a fresh IP address with `$ sudo dhclient ens33`
* if your interface is up and running with an assigned IP address and you still can not reach google.com, make sure you have a valid hostname assigned to your machine - `$ hostname` (rare to have a problem here)
* check whether google.com is up and reachable - `sudo ping -c 3 google.com` (-c for count) 
    * "unknown host google.com" would indicate a possible problem with the DNS set-up
    * or you might see a ping result with a loop to the host machine like "64 bytes from 127.0.53.53 ..."
    * can do `$ host random3inval6idsi2te.com`
* associate "myfavoritemachine" with 127.0.0.1 - edit `/etc/hosts`; `/etc/hosts` is used before the DNS is consulted (most useful for specifying nodes on your local network)
* resolve google.com using the default DNS server - `$ host google.com` (using `/etc/resolv.conf`) or `$ dig google.com`
* resolve google.com using a publicly available DNS - `$ host google.com 8.8.8.8` or `$ dig google.com 8.8.8.8`
* if the host or dig still cannot connect the name to an IP address, maybe the DNS service is down; check - `$ ping 172.16.79.2` or `$ ping 8.8.8.8`
* if the server is up but unavailable on the machine, the route may be incorrect; check - `$ sudo traceroute 8.8.8.8` or `$ sudo mtr --report-cycles 3 8.8.8.8`
* if you only get one line of output, the default route is probably wrong; check - `$ ip route show` or `$ /sbin/ip route show` or `$ ip route`; should be set to your network interface and IP address of router; add a proper default route


* show the current hostname by path - `$ cat /etc/hostname`
* show the current static DNS by path - `$ cat /etc/hosts`
* show persistent NIC and device configs - `$ cat /etc/sysconfig/network` and `$ ls -al /etc/sysconfig/network-scripts` (use Network Manager to edit)

### Network interfaces
* show information for all network interfaces - *`$ ip link show` 
* show information for the `ens33` network interface, including statistics - `$ ip -s link show ens33` 
* set the IP address for `eth0` - `$ sudo ip addr add 192.168.1.7 dev eth0` 
* bring `eth0` down - `$ sudo ip link set eth0 down`  
* set the **MTU** to 1480 bytes for `eth0` - `$ sudo ip link set eth0 mtu 1480` 
* set the networking route - `$ sudo ip route add 172.16.1.0/24 via 192.168.1.5` 

### Network manager
* open the network manager text user interface - `$ nmtui`
* show general device and connection information - `$ nmcli`
* show detailed information about devices - `$ nmcli device show`
* list in-memory and on-disk connection profiles - `$ nmcli connection show`
* print status of devices - `$ nmcli device status`
* show nmcli examples - `$ man nmcli-examples`
  
### Default route
* manually configure the route using nmcli - `$ sudo nmcli con mod virbr0 ipv4.routes 192.168.10.0/24 +ipv4.gateway 192.168.122.0` then `$ sudo nmcli con up virbr0`
* manually configure the route directly - put **GATEWAY=x.x.x.x** in `/etc/sysconfig/network` or alternatively in `/etc/sysconfig/network-scripts/ifcfg-ethX` on a device-specific basis
* set the new route at runtime - `$ sudo route add default gw 192.168.1.10 enp2s0`
* when the system can access more than one router, or perhaps there are multiple interfaces, selectively control which packets go to which router - `$ sudo ip route add 10.5.0.0/16 via 192.168.1.100` (not persistent)
* make the route persistent - edit `$ /etc/sysconfig/network-scripts/route-eth0`
* restore the default route - `$ sudo route add default gw 192.168.1.1 enp2s0`
  
### /etc/hosts
* change name resolution priority - `$ vim /etc/nsswitch.conf`
* explicitly allow a host - `$ vim /etc/hosts.allow`
* explicitly deny a host - `$ vim /etc/hosts.deny`
* both allow and deny a host; which one takes effect? - allow
  
### Static configuration of a network interface
* show your current IP address - `$ ip addr show ens33`
* show your current default route - `$ ip route` or `$ route -n`
* show your dns settings for ens33 or eth0, keep a copy - `$ cat /etc/resolve.conf` or `$ nmcli dev show | grep DNS`, `$ cp /etc/resolv.conf resolv.conf.keep`
* bring down ens33 or eth0 - `$ sudo ip link set ens33 down`
* reconfigure ens33 or eth0 to use a static address instead of **DCHP** - edit `/etc/sysconfig/network-scripts/ifcfg-eth0` and add following lines at the bottom: IPADDR=noted from step 1  NETMASK=noted from step 1 GATEWAY=noted from step 1
* bring the interface back up - `$ sudo ip link set ens33 up`
* configure the nameserver resolver with the information that you noted before - `$ sudo cp resolv.conf.keep /etc/resolv.conf`
* verify your hostname and then **ping** it - `$ cat /etc/sysconfig/network`, `$ cat /etc/hosts`, `$ ping yourhostname`
  
### Add a static hostname
* open `/etc/hosts` and add an entry for `mysystem.mydomain` that will point to the IP address associated with your network card - `$ sudo sh -c "echo 192.168.1.180 mysystem.mydomain >> /etc/hosts"`, `$ ping mysystem.mydomain`
* add a second entry that will make all references to ad.doubleclick.net point to 127.0.0.1 - `$ sudo sh -c "echo 127.0.0.1 ad.doubleclick.net >> /etc/hosts"`, `$ ping ad.doubleclick.net`
* as an optional exercise, download the host file from: http://winhelp2002.mvps.org/hosts2.htm or more directly from http://winhelp2002.mvps.org/hosts.txt , and install it on your system. Do you notice any difference using your browser with and without the new host file in place? - `$ wget http://winhelp2002.mvps.org/hosts.txt`, `$ sudo sh -c "cat hosts.txt >> /etc/hosts"`

### Adding a network interface alias/address using nmcli
* obtain your current interface name - `$ sudo nmcli con`
* obtain your current internet address - `$ sudo nmcli con show "Auto Ethernet" | grep IP4.ADDRESS` (shows 172.16.2.135/24)
* add a new address that your machine can be seen by - `$ sudo nmcli con modify "Auto Ethernet" +ipv4.addresses 172.16.2.140/24`
* activate it and test to see if it is there - `$ sudo nmcli con up "Auto Ethernet"`, `$ ping -c 3 172.16.2.140`
* clean up by removing the alias - `$ sudo nmcli con modify "Auto Ethernet" -ipv4.addresses 172.16.2.140/24` , `$ sudo nmcli con up "Auto Ethernet"`

### Add a static route using nmcli
* examine your current routing tables, using both route and ip - `$ route`, `$ ip route`
* add a new route using nmcli - `$ sudo nmcli con mod "Auto Ethernet" +ipv4.routes "192.168.100.0/24 172.16.2.1"`
* note it has not yet taken effect - `$ route`
* reload the interface to have it take effect and show it has - `$ sudo nmcli con up "Auto Ethernet"`, `$ route`
* remove the new route - `$ sudo nmcli con mod "Auto Ethernet" -ipv4.routes "192.168.100.0/24 172.16.2.1"`, `$ sudo nmcli con up "Auto Ethernet"`
* do this using route (not persistent) - `$ sudo ip route add 192.168.100.0/24 via 172.16.2.1`
 
### firewalld
----
* get firewalld help - `$ firewall-cmd --help` 
* check the status of firewalld - `$ sudo systemctl status firewalld` or `$ sudo firewall-cmd --state`
* turn on ip forwarding - `$ sudo sysctl net.ipv4.ip_forward=1` or `$ echo 1 > /proc/sys/net/ipv4/ip_forward`
* make ip forwarding persistent - add `net.ipv4.ip_forward=1` to `/etc/sysctl.conf` then reboot or type `$ sudo sysctl -p`
 
### Zone management
----
* get the default zone - `$ sudo firewall-cmd --get-default-zone`
* obtain a list of zones currently being used - `$ sudo firewall-cmd --get-active-zones`
* list all available zones - `$ sudo firewall-cmd --get-zones`
* change the default zone to **trusted** - `$ sudo firewall-cmd --set-default-zone=trusted`
* change the default zone back to **public** - `$ sudo firewall-cmd --set-default-zone=public`
* assign an interface temporarily to a particular zone - `$ sudo firewall-cmd --zone=internal --change-interface=eno1`
* assign an interface to a particular zone permanently - `$ sudo firewall-cmd --permanent --zone=internal --change-interface=eno1`(creates the file `/etc/firewalld/zones/internal.xml`)
* ascertain the zone associated with a particular interface - `$ sudo firewall-cmd --get-zone-of-interface=eno1`
* get all details about a particular zone - `$ sudo firewall-cmd --zone=public --list-all`
public (default, active)
  
### Source management
* any zone can be bound not just to a network interface, but also to particular network addresses; a packet is associated with a zone if:
    * it comes from a source address already bound to the zone; or if not,
    * it comes from an interface bound to the zone
* any packet not fitting the above criteria is assigned to the default zone
* assign anyone with an IP address of 192.168.1.x to the trusted zone (permanently) `$ sudo firewall-cmd --permanent --zone=trusted --add-source=192.168.1.0/24`
* remove 192.168.1.x from the assigned source - use the **--remove-source option** or change the zone by using **--change-source**
* list the sources bound to a zone with - `$ sudo firewall-cmd --permanent --zone=trusted --list-sources`
  
### Service management
* see all the services available - `$ sudo firewall-cmd --get-services`
* see services currently accessible in a particular zone - `$ sudo firewall-cmd --list-services --zone=public`
* add a service to a zone - `$ sudo firewall-cmd --permanent --zone=home --add-service=dhcp`
* reload firewall rules and keep state information. Current permanent configuration will become new runtime configuration - `$ sudo firewall-cmd --reload`
* add new services by editing this - `/etc/firewalld/services`
  
### Port management
* add port 21/tcp to the home zone - `$ sudo firewall-cmd --zone=home --add-port=21/tcp`
* list all ports in the home zone - `$ sudo firewall-cmd --zone=home --list-ports`
* list all services on the machine, including name, port number, protocol, and aliases - `/etc/services`
* ascertain that port 21 corresponds to ftp - `$ grep " 21/tcp" /etc/services`
  
### Adding services to a zone
----
* add the http and https services to the public zone and verify that they are currently listed - `$ sudo firewall-cmd --zone=public --add-service=http`, `$ sudo firewall-cmd --zone=public --add-service=https`, and `$ sudo firewall-cmd --list-services --zone=public`
* reload the firewall settings and verify - `$ sudo firewall-cmd --reload`, `$ sudo firewall-cmd --list-services --zone=public`

### Network quiz
* Your workstation is connected to the local network of Acme Company. On the local network, there are other workstations, a DNS server, and a web server for the domain http://intranet.acme.com. Internet connection is also provided for the local network.
    * Removing the gateway configuration from your workstation **has no** impact on accessing resources on the local network.
    * Removing the gateway configuration from your workstation **has** an impact on accessing resources on the Internet.
    * Removing the DNS configuration from your workstation **has** an impact on accessing resources on the local network.
    * Removing the DNS configuration from your workstation **has** an impact on accessing resources on the Internet.
    * Adding an entry to `/etc/hosts` **can** enable your workstation to access http://intranet.acme.com when the DNS server is not working.

### Extras
* ifconfig (superceded by ip)
    * `$ ifconfig` display information about all interfaces
    * `$ ifconfig eth0` display information about only `eth0`
    * `$ sudo ifconfig eth0 192.168.1.50` set the IP address to 192.168.1.50 on interface `eth0`
    * `$ sudo ifconfig eth0 netmask 255.255.255.0` set the **netmask** to 24-bit
    * `$ sudo ifconfig eth0 up` bring interface `eth0` up
    * `$ sudo ifconfig eth0 down` bring interface `eth0` down
    * `$ sudo ifconfig eth0 mtu 1480` set the **MTU** (**M**aximum **T**ransfer **U**nit) to 1480 bytes for interface `eth0`
* Lab 36.1: installing firewalld
* Lab 36.2: examining firewall-cmd
* Lab 36.4: using the firewall GUI

#### Decoding IPv4 addresses
* divided into four, 8-bit sections called octets (bytes)  
* example:  
   * IP address →   172.        16.         31.         46  
   * Bit format →   10101100.   00010000.   00011111.   00101110  
* network addresses divided into classes: A, B, C, D, E  
* A, B, C classified into two parts: **Network addresses (Net ID)** and **Host address (HostID)**  
   * Net ID identifies network  
   * Host ID identifies a host in the network  
* D for multicast applications  
* E reserved for the future  

#### Class A Network Addresses
* use first octet as Net ID and other three as Host ID  
* first bit of 1st octet always set to zero, so only 7 bits for unique network numbers  
* 16.7M unique hosts available on a network, from 1.0.0.0, to 127.255.255.255  

#### Class B Network Addresses
* use first two octets as Net ID and last two as Host ID  
* first two bits of 1st octet always set to 10  
* can support max of 65,536 unique hosts on network, from 128.0.0.0 to 191.255.255.255  

#### Class C Network Addresses
* use first three octets as Net ID and last one as Host ID  
* first three bits set to 110, so 2.1M networks available  
* common for smaller networks which don't have many unique hosts  
* can support 256 unique hosts, from 192.0.0.0 to 223.255.255.255 

### IPv4 address types
----
* **Unicast**: an address associated with a specific host; it might be something like 140.211.169.4 or 64.254.248.193
* **Network**: an address whose **host** portion is set to all binary zeroes, like 192.168.1.0 (the host portion can be the last 1-3 octets; here it is just the last octet)
* **Broadcast**: an address to which each member of a particular network will listen; it will have the host portion set to all 1 bits, such as in 172.16.255.255 or 148.114.255.255 or 192.168.1.255 (the host portion is the last two octets in the first two cases, just the last one in the third case)
* **Multicast**: an address to which appropriately configured nodes will listen; the address 224.0.0.2 is an example of a multicast address; only nodes specifically configured to pay attention to a specific multicast address will interpret packets for that multicast group

**Address Classes**

Network Class | Highest order octet range | Notes
------------- | ------------------------- | -----
A             | 1-127                     | 128 networks, 16,772,214 hosts per network, 127.x.x.x reserved for loopback
B             | 128-191                   | 16,384 networks, 65,534 hosts per network
C             | 192-223                   | 2,097,152 networks, 254 hosts per network
D             | 224-239                   | Multicast addresses
E             | 240-254                   | Reserved address range

### Netmasks
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

