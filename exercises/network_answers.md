

LFS101 14
Chapter 14: Network Operations
------------------------------

[14.0: Introduction/ Learning Objectives](#140-introduction-learning-objectives)  
[14.1: Network Addresses and DNS](#141-network-addresses-and-dns)  
[14.2: Network Configuration and Tools](#142-network-configuration-and-tools)  
[14.3: Browsers](#143-browsers)  
[14.4: Transferring Files](#144-transferring-files)  
[14.5: Summary](#145-summary)  
[Paths and Commands](#paths-and-commands)  
  
### 14.0: Introduction/ Learning Objectives
----
By the end of this chapter, you should be able to:
* Explain basic networking concepts, including types of networks and addressing issues.
* Configure network interfaces and use basic networking utilities, such as **ifconfig**, **ip**, **ping**, **route** & **traceroute**.
* Use graphical and non-graphical browsers, such as **Lynx**, **w3m**, **Firefox**, **Chrome** and **Epiphany**.
* Transfer files to and from clients and servers using both graphical and text mode applications, such as **Filezilla**, **ftp**, **sftp**, **curl** and **wget**.

### 14.1: Network Addresses and DNS
----
#### Introduction to Networking

#### IP Addresses
(nothing new here)  

#### IPv4 and IPv6
* IPv4 uses 32 bits - *only* 4.3 billion unique addresses available  
* IPv6 uses 128 bits, allowing for 3.4 x 10^38 unique addresses  

#### Decoding IPv4 Addresses
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

#### IP Address Allocation
* request IP addresses from ISP  
* if assigning manually, use **static** addresses on the network  
* if assigning dynamically (can change on every reboot or more often), use **Dynamic Host Configuration Protocol (DHCP)**  

#### Name Resolution
* **name resolution** used to convert IP to human-readable **hostname**  
* `$ hostname` shows the system's hostname  
* **localhost** == 127.0.0.0  

#### Using Domain Name System (DNS) and Name Resolution Tools
(video)  

#### Try-It-Yourself: Using Domain Name System (DNS) and Name Resolution Tools
(done)
`$ cat /etc/hosts`  
```
   ##
   # Host Database
   #
   # localhost is used to configure the loopback interface
   # when the system is booting.  Do not change this entry.
   ##
   127.0.0.1       localhost
   255.255.255.255 broadcasthost
   ::1             localhost 
```
`$ cat /etc/resolv.conf`  
`$ host linuxfoundation.org`  
```
linuxfoundation.org has address 23.185.0.2
linuxfoundation.org has IPv6 address 2620:12a:8000::2
linuxfoundation.org has IPv6 address 2620:12a:8001::2
linuxfoundation.org mail is handled by 5 alt2.aspmx.l.google.com.
linuxfoundation.org mail is handled by 1 aspmx.l.google.com.
linuxfoundation.org mail is handled by 10 alt3.aspmx.l.google.com.
linuxfoundation.org mail is handled by 10 alt4.aspmx.l.google.com.
linuxfoundation.org mail is handled by 5 alt1.aspmx.l.google.com.
```
`$ nslookup linuxfoundation.org`  
```
Server:         192.168.1.1
Address:        192.168.1.1#53

Non-authoritative answer:
Name:   linuxfoundation.org
Address: 23.185.0.2
```
`$ dig linuxfoundation.org`
```
; <<>> DiG 9.10.6 <<>> linuxfoundation.org
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 5654
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 512
;; QUESTION SECTION:
;linuxfoundation.org.           IN      A

;; ANSWER SECTION:
linuxfoundation.org.    286     IN      A       23.185.0.2

;; Query time: 14 msec
;; SERVER: 192.168.1.1#53(192.168.1.1)
;; WHEN: Wed Dec 26 23:01:34 PST 2018
;; MSG SIZE  rcvd: 64
```

### 14.2: Network Configuration and Tools
----
#### Network Configuration Files
* you can use **Network Manager**, like **nmtui** or **nmcli**  

#### Network Interfaces
* a **network interface** is a connection channel between a device and a network  
* it proceeds through a **network interface card** (**nic**) or implemented as software  
* **ifconfig** (or **ip**) provides information about interfaces  

#### The ip Utility
* view IP address: `$ /sbin/ip addr show`  
* view routing info: `$ /sbin/ip route show`  

#### ping
* **ping** is used to check whether a machine on the network can receive and send data  
* `$ ping <hostname>`  

#### route
* **routing tables** contain the addresses of each node in the network  
* **IP Routing protocols** enable routers to build up a forwarding table that correlates final destinations with the next **hop** addresses  
* can use **route** or **ip route** to view or change IP routing table  
* `$ route -n`  
```
Kernel IP routing table
Destination     Gateway         Genmask         Flags Metric Ref    Use Iface
0.0.0.0         192.168.1.1     0.0.0.0         UG    100    0        0 ens33
192.168.1.0     0.0.0.0         255.255.255.0   U     100    0        0 ens33
192.168.122.0   0.0.0.0         255.255.255.0   U     0      0        0 virbr0
```
* `$ ip route`  
```
default via 192.168.1.1 dev ens33 proto dhcp metric 100 
192.168.1.0/24 dev ens33 proto kernel scope link src 192.168.1.14 metric 100 
192.168.122.0/24 dev virbr0 proto kernel scope link src 192.168.122.1 
```
* commands to manage IP routing:  

Task                       | Command
----                       | -------
Show current routing table | `$ route –n` or `$ ip route`  
Add static route           | `$ route add -net address` or `$ ip route add`   
Delete static route        | `$ route del -net address` or `$ ip route del`  

#### traceroute
* **traceroute** is used to inspect the route which the data packet takes to reach the destination host  

#### Try-It-Yourself: Using ping, route, and traceroute
(done)  

#### More Networking Tools
Networking Tools | Description  
---------------- | -----------  
ethtool          | Queries network interfaces and can also set various parameters such as the speed.  
netstat          | Displays all active connections and routing tables. Useful for monitoring performance and troubleshooting.  
nmap             | Scans open ports on a network. Important for security analysis.  
tcpdump          | Dumps network traffic for analysis.  
iptraf           | Monitors network traffic in text mode.  
mtr              | Combines functionality of ping and traceroute and gives a continuously updated display.  
dig              | Tests DNS workings. A good replacement for host and nslookup.  

#### Using More Networking Tools
* `$ ethtool eth0`  
* `$ netstat -r`  

#### Try-It-Yourself: Using Network Tools
(done)  

#### Knowledge Check
* `$ sudo hostname LFstudent` changes system's hostname

### 14.3: Browsers
----
#### Graphical and Non-Graphical Browsers
* graphical:  
   * Firefox  
   * Google Chrome  
   * Chromium  
   * Epiphany  
   * Opera  
* non-graphical:  

Non-Graphical Browsers | Description  
---------------------- | -----------  
lynx                   | Configurable text-based web browser; the earliest such browser and still in use.  
links or elinks        | Based on lynx. It can display tables and frames.  
w3m                    | Another text-based web browser with many features.  

#### wget
* **wget** is used for web downloads:  
   * Large file downloads  
   * Recursive downloads, where a web page refers to other web pages and all are downloaded at once  
   * Password-required downloads  
   * Multiple file downloads.  
* `$ wget <url>`  
   * `$ sudo wget https://docs.gitlab.com/ee/user/markdown.html`  
   * `$ sudo wget https://training.linuxfoundation.org`  

#### curl
* **curl** is used to save contents of a web page like **wget** and to obtain info about source code  
* `$ curl <URL>`  
* to save to a file called "saved.html": `$ curl -o saved.html <URL>`  

#### Try-It-Yourself: Using wget and curl
(done)  

### 14.4: Transferring Files
----
#### FTP (File Transfer Protocol)
(nothing new)  

#### FTP Clients
* insecure, out of favor  
* can use **rsync**, **https**, or **sftp** (uses **Secure Shell (ssh)** protocol) instead  

#### Connecting to an FTP server
(video)  

#### Try-It-Yourself: Connecting to an FTP server
* `$ ftp ftp.gnu.org`  
   ```
   Connected to ftp.gnu.org.  
   220 GNU FTP server ready.  
   Name (ftp.gnu.org:test1):  
   230-Due to U.S. Export Regulations, all cryptographic software on this
   230-site is subject to the following legal notice:
   230-
   230- This site includes publicly available encryption source code
   230- which, together with object code resulting from the compiling of
   230- publicly available source code, may be exported from the United
   230- States under License Exception "TSU" pursuant to 15 C.F.R. Section
   230- 740.13(e).
   230-
   230-This legal notice applies to cryptographic software only. Please see
   230-the Bureau of Industry and Security (www.bxa.doc.gov) for more
   230-information about current U.S. regulations.
   230 Login successful.
   Remote system type is UNIX.
   Using binary mode to transfer files.
   ```
* `$ get welcome.msg`  

#### SSH: Executing Commands Remotely
* **Secure Shell (SSH)** is a cryptographic network protocol used for secure data communication
* use:  
   * `$ ssh some_system`  
   * `$ ssh -l someone some_system` or  
   * `$ ssh someone@some_system`  
   * `$ ssh some_system my_command`  
   
#### Copying Files Securely with scp
* **Secure Copy (scp)** is used to copy files securely via SSH  
* `$ scp <localfile> <user@remotesystem>:/home/user>`  
    
#### Lab 14.1: Network Troubleshooting
```
Troubleshooting network problems is something that you will often encounter if you haven't already. We are going to practice some of the previously discussed tools, that can help you isolate, troubleshoot and fix problems in your network.

The Solution file contains a step-by-step procedure for exercising many of the tools we have studied. Please repeat the steps, substituting your actual network interface names, alternative network addresses and web sites, etc.
```
* My "solution" (execute lab solution commands):
```
[student@centos log]$ /sbin/ifconfig
ens33: flags=4163<UP,BROADCAST,RUNNING,MULTICAST>  mtu 1500
        inet 192.168.1.103  netmask 255.255.255.0  broadcast 192.168.1.255
        inet6 fe80::fdf5:c05f:e9ee:8180  prefixlen 64  scopeid 0x20<link>
        ether 00:0c:29:0b:9a:b4  txqueuelen 1000  (Ethernet)
        RX packets 68582  bytes 23202907 (22.1 MiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 13480  bytes 1286528 (1.2 MiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

lo: flags=73<UP,LOOPBACK,RUNNING>  mtu 65536
        inet 127.0.0.1  netmask 255.0.0.0
        inet6 ::1  prefixlen 128  scopeid 0x10<host>
        loop  txqueuelen 1000  (Local Loopback)
        RX packets 764  bytes 63436 (61.9 KiB)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 764  bytes 63436 (61.9 KiB)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

virbr0: flags=4099<UP,BROADCAST,MULTICAST>  mtu 1500
        inet 192.168.122.1  netmask 255.255.255.0  broadcast 192.168.122.255
        ether 52:54:00:de:fa:18  txqueuelen 1000  (Ethernet)
        RX packets 0  bytes 0 (0.0 B)
        RX errors 0  dropped 0  overruns 0  frame 0
        TX packets 0  bytes 0 (0.0 B)
        TX errors 0  dropped 0 overruns 0  carrier 0  collisions 0

[student@centos log]$ ip addr show
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host 
       valid_lft forever preferred_lft forever
2: ens33: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc pfifo_fast state UP group default qlen 1000
    link/ether 00:0c:29:0b:9a:b4 brd ff:ff:ff:ff:ff:ff
    inet 192.168.1.103/24 brd 192.168.1.255 scope global noprefixroute dynamic ens33
       valid_lft 86375sec preferred_lft 86375sec
    inet6 fe80::fdf5:c05f:e9ee:8180/64 scope link noprefixroute 
       valid_lft forever preferred_lft forever
3: virbr0: <NO-CARRIER,BROADCAST,MULTICAST,UP> mtu 1500 qdisc noqueue state DOWN group default qlen 1000
    link/ether 52:54:00:de:fa:18 brd ff:ff:ff:ff:ff:ff
    inet 192.168.122.1/24 brd 192.168.122.255 scope global virbr0
       valid_lft forever preferred_lft forever
4: virbr0-nic: <BROADCAST,MULTICAST> mtu 1500 qdisc pfifo_fast master virbr0 state DOWN group default qlen 1000
    link/ether 52:54:00:de:fa:18 brd ff:ff:ff:ff:ff:ff
[student@centos log]$ hostname
centos
[student@centos log]$ sudo ping -c 3 google.com
PING google.com (172.217.11.174) 56(84) bytes of data.
64 bytes from lax28s15-in-f14.1e100.net (172.217.11.174): icmp_seq=1 ttl=54 time=16.2 ms
64 bytes from lax28s15-in-f14.1e100.net (172.217.11.174): icmp_seq=2 ttl=54 time=20.7 ms
64 bytes from lax28s15-in-f14.1e100.net (172.217.11.174): icmp_seq=3 ttl=54 time=20.0 ms

--- google.com ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2005ms
rtt min/avg/max/mdev = 16.287/19.014/20.725/1.952 ms
[student@centos log]$ host 128x983fj.ocm
Host 128x983fj.ocm not found: 3(NXDOMAIN)
[student@centos log]$ host google.com
google.com has address 172.217.11.78
google.com has IPv6 address 2607:f8b0:4007:802::200e
google.com mail is handled by 50 alt4.aspmx.l.google.com.
google.com mail is handled by 20 alt1.aspmx.l.google.com.
google.com mail is handled by 40 alt3.aspmx.l.google.com.
google.com mail is handled by 10 aspmx.l.google.com.
google.com mail is handled by 30 alt2.aspmx.l.google.com.
[student@centos log]$ dig google.com

; <<>> DiG 9.9.4-RedHat-9.9.4-72.el7 <<>> google.com
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 57248
;; flags: qr rd ra; QUERY: 1, ANSWER: 1, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; MBZ: 1c20 , udp: 512
;; QUESTION SECTION:
;google.com.			IN	A

;; ANSWER SECTION:
google.com.		156	IN	A	172.217.4.174

;; Query time: 25 msec
;; SERVER: 192.168.1.1#53(192.168.1.1)
;; WHEN: Thu Dec 27 22:46:03 PST 2018
;; MSG SIZE  rcvd: 55

[student@centos log]$ sudo traceroute 8.8.8.8
traceroute to 8.8.8.8 (8.8.8.8), 30 hops max, 60 byte packets
 1  gateway (192.168.1.1)  2.618 ms  18.665 ms  18.629 ms
 2  * * *
 3  96-34-9-89.static.unas.mo.charter.com (96.34.9.89)  33.177 ms  34.100 ms  34.506 ms
 4  crr02hsprca-tge-0-1-0-8.hspr.ca.charter.com (96.34.99.168)  36.161 ms dtr01lkahca-tge-0-1-0-2.lkah.ca.charter.com (96.34.99.156)  35.294 ms crr02hsprca-tge-0-1-0-8.hspr.ca.charter.com (96.34.99.168)  36.468 ms
 5  bbr01olvemo-tge-0-1-0-11.olve.mo.charter.com (96.34.2.88)  38.319 ms  37.473 ms bbr01stcdmn-tge-0-1-0-3.stcd.mn.charter.com (96.34.2.212)  40.185 ms
 6  * bbr02chcgil-tge-0-2-0-1.chcg.il.charter.com (96.34.3.129)  145.333 ms bbr01ashbva-tge-0-1-0-1.ashb.va.charter.com (96.34.3.139)  244.968 ms
 7  72.14.219.250 (72.14.219.250)  245.103 ms *  244.949 ms
 8  * * *
 9  66.249.94.216 (66.249.94.216)  244.637 ms 108.170.237.142 (108.170.237.142)  244.564 ms 108.170.237.112 (108.170.237.112)  244.480 ms
10  google-public-dns-a.google.com (8.8.8.8)  244.395 ms 216.239.50.37 (216.239.50.37)  244.324 ms google-public-dns-a.google.com (8.8.8.8)  244.254 ms
[student@centos log]$ ip route show
default via 192.168.1.1 dev ens33 proto dhcp metric 100 
192.168.1.0/24 dev ens33 proto kernel scope link src 192.168.1.103 metric 100 
192.168.122.0/24 dev virbr0 proto kernel scope link src 192.168.122.1 
[student@centos log]$ sudo mtr --report-cycles 3 8.8.8.8
[student@centos log]$ ^C
[student@centos log]$ 
```
* Lab solution:    
```

Lab: Network Troubleshooting

Troubleshooting network problems is something that you will often encounter if you haven't already. We are going to practice some of the previously discussed tools, that can help you isolate, troubleshoot and fix problems in your network.

Suppose you need to perform an Internet search, but your web browser can not find google.com, saying the host is unknown. Let's proceed step by step to fix this.

    First make certain your network is properly configured. If your Ethernet device is up and running, running ifconfig should display something like:

    student:/tmp> /sbin/ifconfig

    eno167777 Link encap:Ethernet  HWaddr 00:0C:29:BB:92:C2
              inet addr:192.168.1.14  Bcast:192.168.1.255  Mask:255.255.255.0
              inet6 addr: fe80::20c:29ff:febb:92c2/64 Scope:Link
              UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
              RX packets:3244 errors:0 dropped:0 overruns:0 frame:0
              TX packets:2006 errors:0 dropped:0 overruns:0 carrier:0
              collisions:0 txqueuelen:1000
              RX bytes:4343606 (4.1 Mb)  TX bytes:169082 (165.1 Kb)

    lo        Link encap:Local Loopback
              inet addr:127.0.0.1  Mask:255.0.0.0
              inet6 addr: ::1/128 Scope:Host
              UP LOOPBACK RUNNING  MTU:65536  Metric:1
              RX packets:0 errors:0 dropped:0 overruns:0 frame:0
              TX packets:0 errors:0 dropped:0 overruns:0 carrier:0
              collisions:0 txqueuelen:0
              RX bytes:0 (0.0 b)  TX bytes:0 (0.0 b) 

    On older systems you probably will see a less cryptic name than eno167777, like eth0, or for a wireless connection, you might see something like wlan0 or wlp3s0. You can also show your IP address with:

    student:/tmp> ip addr show

    1: lo:  mtu 65536 qdisc noqueue state UNKNOWN group default
        link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
        inet 127.0.0.1/8 scope host lo
           valid_lft forever preferred_lft forever
        inet6 ::1/128 scope host
           valid_lft forever preferred_lft forever
    2: eno16777736:  mtu 1500 qdisc pfifo_fast state \
        UP group default qlen 1000
        link/ether 00:0c:29:bb:92:c2 brd ff:ff:ff:ff:ff:ff
    p    inet 192.168.1.14/24 brd 192.168.1.255 scope global dynamic eno16777736
           valid_lft 84941sec preferred_lft 84941sec
        inet 192.168.1.15/24 brd 192.168.1.255 scope global secondary dynamic eno16777736
           valid_lft 85909sec preferred_lft 85909sec
        inet6 fe80::20c:29ff:febb:92c2/64 scope link
           valid_lft forever preferred_lft forever
          

    Does the IP address look valid? Depending on where you are using this from, it is most likely a Class C IP address; in the above this is 192.168.1.14

    If it does not show a device with an IP address, you may need to start or restart the network and/or NetworkManager. Exactly how you do this depends on your system. For most distributions one of these commands will accomplish this:

    student:/tmp> sudo systemctl restart NetworkManager
    student:/tmp> sudo systemctl restart network
    student:/tmp> sudo service NetworkManager restart
    student:/tmp> sudo service network restart
          

    If your device was up but had no IP address, the above should have helped fix it, but you can try to get a fresh address with:

    student:/tmp> sudo dhclient eth0
          

    substituting the right name for the Ethernet device.
    If your interface is up and running with an assigned IP address and you still can not reach google.com, we should make sure you have a valid hostname assigned to your machine, with hostname:

    student:/tmp> hostname

    openSUSE
          

    It is rare you would have a problem here, as there is probably always at least a default hostname, such as localhost.
    When you type in a name of a site such as google.com, that name needs to be connected to a known IP address. This is usually done employing the DNS sever (Domain Name System)

    First, see if the site is up and reachable with ping:

    student:/tmp> sudo ping -c 3 google.com

    PING google.com (216.58.216.238) 56(84) bytes of data.
    64 bytes from ord31s22-in-f14.1e100.net (216.58.216.238): icmp_seq=1 ttl=51 time=21.7 ms
    64 bytes from ord31s22-in-f14.1e100.net (216.58.216.238): icmp_seq=2 ttl=51 time=23.8 ms
    64 bytes from ord31s22-in-f14.1e100.net (216.58.216.238): icmp_seq=3 ttl=51 time=21.3 ms

    --- google.com ping statistics ---
    3 packets transmitted, 3 received, 0% packet loss, time 2002ms
    rtt min/avg/max/mdev = 21.388/22.331/23.813/1.074 ms
          

    Note:
        We have used sudo for ping; recent Linux distributions have required this to avoid clueless or malicious users from flooding systems with such queries.
        We have used -c 3 to limit to 3 packets; otherwise ping would run forever until forcibly terminated, say with CTRL-C. 
    If the result was:

    ping: unknown host google.com
          

    It is likely that something is wrong with your DNS set-up. (Note on some systems you will never see the unknown host message, but you will get a suspicious result like:

    student:/tmp> sudo ping l89xl28vkjs.com

    PING l89xl28vkjs.com.site (127.0.53.53) 56(84) bytes of data.
    64 bytes from 127.0.53.53: icmp_seq=1 ttl=64 time=0.016 ms
    ...
          

    where the 127.0.x.x address is a loop feeding back to the host machine you are on. You can eliminate this as being a valid address by doing:

    student:/tmp> host l89xl28vkjs.com
    Host l89xl28vkjs.com not found: 3(NXDOMAIN)
          

    whereas a correct result would look like:

    student:/tmp> host google.com

    google.com has address 216.58.216.206
    google.com has IPv6 address 2607:f8b0:4009:80b::200e
    google.com mail is handled by 20 alt1.aspmx.l.google.com.
    google.com mail is handled by 10 aspmx.l.google.com.
    google.com mail is handled by 30 alt2.aspmx.l.google.com.
    google.com mail is handled by 40 alt3.aspmx.l.google.com.
    google.com mail is handled by 50 alt4.aspmx.l.google.com.
          

    The above command utilizes the DNS server configured in /etc/resolv.conf on your machine. If you wanted to override that you could do:

    host 8.8.8.8

    8.8.8.8.in-addr.arpa domain name pointer google-public-dns-a.google.com.
    student@linux:~> host google.com 8.8.8.8
    Using domain server:
    Name: 8.8.8.8
    Address: 8.8.8.8#53
    Aliases:

    google.com has address 216.58.216.110
    google.com has IPv6 address 2607:f8b0:4009:804::1002
    ...\
          

    where we have used the publicly available DNS server provided by Google itself. (Using this or another public server can be a good trick sometimes if your network is up but DNS is ill; in that case you can also enter it in resolv.conf.)

    Note that there is another file, /etc/hosts, where you can associate names with IP addresses, which is used before the DNS server is consulted. This is most useful for specifying nodes on your local network.

    You could also use the dig utility if you prefer:

    student:/tmp> dig google.com

    ; <<>> DiG 9.9.5-rpz2+rl.14038.05-P1 <<>> google.com
    ;; global options: +cmd
    ;; Got answer:
    ;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 29613
    ;; flags: qr rd ra; QUERY: 1, ANSWER: 11, AUTHORITY: 0, ADDITIONAL: 1

    ;; OPT PSEUDOSECTION:
    ; EDNS: version: 0, flags:; MBZ: 1c20 , udp: 1280
    ;; QUESTION SECTION:
    ;google.com.                    IN      A

    ;; ANSWER SECTION:
    google.com.             244     IN      A       173.194.46.67
    google.com.             244     IN      A       173.194.46.65
    google.com.             244     IN      A       173.194.46.71
    google.com.             244     IN      A       173.194.46.73
    google.com.             244     IN      A       173.194.46.69
    google.com.             244     IN      A       173.194.46.68
    google.com.             244     IN      A       173.194.46.64
    google.com.             244     IN      A       173.194.46.72
    google.com.             244     IN      A       173.194.46.70
    google.com.             244     IN      A       173.194.46.66
    google.com.             244     IN      A       173.194.46.78

    ;; Query time: 22 msec
    ;; SERVER: 192.168.1.1#53(192.168.1.1)
    ;; WHEN: Mon Apr 20 08:58:58 CDT 2015
    ;; MSG SIZE  rcvd: 215
          

    Suppose host or dig fail to connect the name to an IP address. There are many reasons DNS can fail, some of which are:
        The DNS server is down. In this case try pinging it to see if it is alive (you should have the IP address in /etc/resolv.conf.
        The server can be up and running, but DNS may not be currently available on the machine.
        Your route to the DNS server may not be correct. 
    How can we test the route? Tracing the route to one of the public name server we mentioned before:

    student@linux:~> sudo traceroute 8.8.8.8

    traceroute to 8.8.8.8 (8.8.8.8), 30 hops max, 60 byte packets
     1  192.168.1.1 (192.168.1.1)  0.405 ms  0.494 ms  0.556 ms
     2  10.132.4.1 (10.132.4.1)  15.127 ms  15.107 ms  15.185 ms
     3  dtr02ftbgwi-tge-0-6-0-3.ftbg.wi.charter.com (96.34.24.122)
                                                              15.243 ms  15.327 ms  17.878 ms
     4  crr02ftbgwi-bue-3.ftbg.wi.charter.com (96.34.18.116)  17.667 ms  17.734 ms  20.016 ms
     5  crr01ftbgwi-bue-4.ftbg.wi.charter.com (96.34.18.108)  22.017 ms  22.359 ms  22.052 ms
     6  crr01euclwi-bue-1.eucl.wi.charter.com (96.34.16.77)  29.430 ms  22.705 ms  22.076 ms
     7  bbr01euclwi-bue-4.eucl.wi.charter.com (96.34.2.4)  17.795 ms  25.542 ms  25.600 ms
     8  bbr02euclwi-bue-5.eucl.wi.charter.com (96.34.0.7)  28.227 ms  28.270 ms  28.303 ms
     9  bbr01chcgil-bue-1.chcg.il.charter.com (96.34.0.9)  33.114 ms  33.072 ms  33.175 ms
    10  prr01chcgil-bue-2.chcg.il.charter.com (96.34.3.9)  36.882 ms  36.794 ms  36.895 ms
    11  96-34-152-30.static.unas.mo.charter.com (96.34.152.30)  42.585 ms  42.326 ms  42.401 ms
    12  216.239.43.111 (216.239.43.111)  28.737 ms 216.239.43.113 (216.239.43.113)
                                                                24.558 ms  23.941 ms
    13  209.85.243.115 (209.85.243.115)  24.269 ms 209.85.247.17 (209.85.247.17)
       25.758 ms 216.239.50.123 (216.239.50.123)  25.433 ms
    14  google-public-dns-a.google.com (8.8.8.8)  25.239 ms  24.003 ms  23.795 ms
          

    Again, this should likely work for you, but what if you only got the first line in the traceroute output?

    If this happened, most likely your default route is wrong. Try:

    student:/tmp> ip route show

    efault via 192.168.1.1 dev eno16777736  proto static  metric 1024
    192.168.1.0/24 dev eno16777736  proto kernel  scope link  src 192.168.1.14
          

    Most likely this is set to your network interface and the IP address of your router, DSL, or Cable Modem. Let's say that it is blank or simply points to your own machine. Here's your problem! At this point, you would need to add a proper default route and run some of the same tests we just did.

    Note, an enhanced version of traceroute is supplied by mtr, which runs continuously (like top). Running it with the --report-cycles option to limit how long it runs:

    student:/tmp> sudo mtr --report-cycles 3 8.8.8.8

                                 My traceroute  [v0.85] 
    c7 (0.0.0.0)                                           Mon Apr 20 09:30:41 2015
    Unable to allocate IPv6 socket for nameserver communication: Address family not supported
                  by protocol                  Packets               Pings
     Host                                Loss%   Snt   Last   Avg  Best  Wrst StDev
                                          0.0%     3    0.3   0.3   0.2   0.3   0.0
     2. 10.132.4.1                        0.0%     3    6.3   7.1   6.3   8.4   0.7
     3. dtr02ftbgwi-tge-0-6-0-3.ftbg.wi.  0.0%     3    6.2   7.5   6.2  10.0   2.1
     4. dtr01ftbgwi-bue-1.ftbg.wi.charte  0.0%     3    8.9   8.5   6.2  10.4   2.0
     5. crr01ftbgwi-bue-4.ftbg.wi.charte  0.0%     3    8.9   9.7   8.9  10.4   0.0
     6. crr01euclwi-bue-1.eucl.wi.charte  0.0%     3   16.5  17.4  14.2  21.5   3.7
     7. bbr01euclwi-bue-4.eucl.wi.charte  0.0%     3   23.5  22.0  18.2  24.2   3.2
     8. bbr02euclwi-bue-5.eucl.wi.charte  0.0%     3   18.9  22.7  18.1  31.1   7.2
     9. bbr01chcgil-bue-1.chcg.il.charte  0.0%     3   22.9  23.0  22.9  23.1   0.0
    10. prr01chcgil-bue-2.chcg.il.charte  0.0%     3   21.4  24.1  20.8  30.2   5.2
    11. 96-34-152-30.static.unas.mo.char  0.0%     3   22.6  21.9  20.0  23.3   1.6
    12. 216.239.43.111                    0.0%     3   21.2  21.7  21.2  22.0   0.0
    13. 72.14.237.35                      0.0%     3   21.2  21.0  19.8  21.9   1.0
    14. google-public-dns-a.google.com    0.0%     3   26.7  23.0  21.0  26.7   3.2
          

Hopefully, running through some of these commands helped. It actually helps to see what the correct output for your system looks like. Practice using these commands; it is very likely that you will need them someday. 
```

#### Lab 14.2: Non-graphical Browsers
```
There are times when a graphical browser is not available, but you need to look up or download a resource. In this exercise, we are going to experiment with using non-graphical web browsers. 

The Solution file contains a step-by-step procedure for exercising the tools discussed. Please repeat the steps, substituting web sites, etc.
```
* My "solution" (execute lab solution commands):  I tried Lynx, but I think the page described by the solution has changed and lacks a "Latest Releases" section; downloading different files didn't seem to work  
* Lab solution:  
```
We have discussed non-graphical browsers:

    lynx
    links and elinks
    w3m

There are times when you will not have a graphical window interface running on your Linux machine and you need to look something up on the web or download a driver (like a graphics driver in order to bring up a graphical window interface). So, it is a good idea to practice using a non-graphical web browser to do some work.

With links, you can use your mouse to click on the top line of the screen to get a menu. In this case, we want to go to google.com (or your favorite search engine), so you can just type g to go to a typed-in URL.

Pressing the TAB key will move your cursor to the OK button. You can then press the ENTER key.

You should now be at google.com (or your favorite search engine). Use the down-arrow key to move through the choices until you reach the blank line used to enter your search query. Now type Intel Linux graphics drivers in the search box. Use the down-arrow key to move you to the Google Search button. With that highlighted, press the ENTER key.

Use your down-arrow key to move to the entry: Intel(R) Graphics Drivers for Linux - Download Center. It may take several presses of the down-arrow key. You can press the space-bar to move down the page or the â€˜bâ€™ key to move back up the page if needed. Once this line is highlighted, press the ENTER key. You will now go to the Intel Graphics Driver for Linux page. If you want, you can read the page. Remember, the space-bar will page you down the page while the â€˜bâ€™ key will move you back up the page. The Page Down and Page Up keys will do the same thing if you prefer. Find the URL under the line

URL Location:
      

Position your cursor at this line using the up-arrow or down-arrow key. Press the ENTER key to go to this location.

Page down this page until you see the line:

Latest Releases
      

If you move your cursor with the arrow keys, find the latest version (with the most recent release date) under this section. If using your arrow-keys, you should highlight Release Notes. Press the ENTER key.

This has installers for versions of Ubuntu and Fedora, along with the source code. You will need to page down a page or two depending on the size of your screen.

Select one of the installers, perhaps for the version of Linux that you are running, or just a random one, and press the ENTER key.

You should see a text dialog box with choices of what to do. Save the package wherever you want to.

You can now quit your non-graphical browser. If you used links, then click on the top line of the screen, select the File drop-down menu item, and click on Exit. Confirm that you really want to exit Links. You should now see your shell prompt. 
```

### 14.5: Summary
----
You have completed this chapter. Let’s summarize the key concepts covered:
* The **IP** (Internet Protocol) **address** is a unique logical network address that is assigned to a device on a network.
* **IPv4** uses 32-bits for addresses and **IPv6** uses 128-bits for addresses.
* Every IP address contains both a network and a host address field.
* There are five classes of network addresses available: A, B, C, D & E.
* **DNS** (Domain Name System) is used for converting Internet domain and host names to IP addresses.
* The **ifconfig** program is used to display current active network interfaces.
* The commands `$ ip addr show` and `$ ip route` show can be used to view IP address and routing information.
* You can use **ping** to check if the remote host is alive and responding.
* You can use the **route** utility program to manage IP routing.
* You can monitor and debug network problems using networking tools.
* **Firefox**, **Google Chrome**, **Chromium**, and **Epiphany** are the main graphical browsers used in Linux.
* Non-graphical or text browsers used in Linux are **Lynx**, **Links**, and **w3m**.
* You can use **wget** to download webpages.
* You can use **curl** to obtain information about URLs.
* **FTP** (File Transfer Protocol) is used to transfer files over a network.
* **ftp**, **sftp**, **ncftp**, and **yafc** are command line FTP clients used in Linux.
* You can use **ssh** to run commands on remote systems.

### Paths and Commands
----
  
#### Paths  

Topics | Path | Notes | Reference
------ | ---- | ----- | ---------
network | `$ /etc/hosts` | _ | LFS101 14.1
network | `$ /etc/resolv.conf` | local nameserver(?) | LFS101 14.1
network | `$ /sbin/ifconfig` | view network settings | LFS101 Lab 14.1

  
#### Commands  

Topics | Command | Notes | Reference
------ | ------- | ----- | ---------
network | `$ hostname` | shows the system's hostname | LFS101 14.1
network | `$ host linuxfoundation.org` | DNS lookup for linuxfoundation.org | LFS101 14.1
network | `$ nslookup linuxfoundation.org` | query internet name server for linuxfoundation.org | LFS101 14.1
network | `$ dig linuxfoundation.org` | DNS lookup for linuxfoundation.org | LFS101 14.1
network | `$ whois 23.185.0.2` | get information about IP owner | LFS101 14.1
network | `$ ip addr show` | view IP address (do I need to use the one in `/sbin` or is `/usr/sbin` fine?) | LFS101 14.2
network | `$ ip route show` | view routing info (do I need to use the one in `/sbin` or is `/usr/sbin` fine?) | LFS101 14.2
network | `$ ping <hostname>` | check whether a machine on the network can receive and send data | LFS101 14.2
network | `$ route -n` | show current routing table | LFS101 14.2
network | `$ ip route` | show current routing table | LFS101 14.2
network | `$ route add -net address` | add static route | LFS101 14.2
network | `$ ip route add` | add static route | LFS101 14.2
network | `$ route del -net address` | delete statis route | LFS101 14.2
network | `$ ip route del` | delete static route | LFS101 14.2
network | `$ traceroute google.com` | inspect the route which the data packet takes to reach the destination host  | LFS101 14.2
network | `$ ethtool eth0` | _ | LFS101 14.2
network | `$ netstat -r` | _ | LFS101 14.2
network | `$ sudo hostname LFstudent` | changes system's hostname | LFS101 14.2
network | `$ wget <url>` | generic syntax (get a website) | LFS101 14.3
network | `$ sudo wget https://docs.gitlab.com/ee/user/markdown.html` | example **wget** usage | LFS101 14.3
network | `$ sudo wget https://training.linuxfoundation.org` | example **wget** usage | LFS101 14.3
network | `$ curl <URL>` | generic syntax (get a website) | LFS101 14.3
network | `$ curl -o saved.html <URL>` | save to a file called "saved.html" | LFS101 14.3
network | `$ ftp ftp.gnu.org` | get a file from an FTP server | LFS101 14.4
network | `$ get welcome.msg` | ?? | LFS101 14.4
network | `$ ssh some_system` | connect to some_system | LFS101 14.4
network | `$ ssh -l someone some_system` | connect to some_system with login name | LFS101 14.4
network | `$ ssh someone@some_system` | connect to some_system with login name | LFS101 14.4
network | `$ ssh some_system my_command` | connect to some_system and execute my_command | LFS101 14.4
network | `$ scp <localfile> <user@remotesystem>:/home/user>` | generic syntax; transfer a file securely | LFS101 14.4
network | `$ sudo systemctl restart NetworkManager` | restart the network or NetworkManager (depends on distribution) | LFS101 Lab 14.1
network | `$ $ sudo systemctl restart network` | restart the network or NetworkManager (depends on distribution) | LFS101 Lab 14.1
network | `$ $ sudo service NetworkManager restart` | restart the network or NetworkManager (depends on distribution) | LFS101 Lab 14.1
network | `$ $ sudo service network restart` | restart the network or NetworkManager (depends on distribution) | LFS101 Lab 14.1
network | `$ sudo dhclient eth0` | get a fresh IP address if restarting the network / NetworkManager didn't do it | LFS101 Lab 14.1
network | `$ hostname` | check hostname; make sure it's valid | LFS101 Lab 14.1
network | `$ sudo ping -c 3 google.com` | see if site is up and reachable | LFS101 Lab 14.1
network | `$ sudo traceroute 8.8.8.8` | trace the route to the DNS to see if the route is correct | LFS101 Lab 14.1
network | `$ ip route show` | check that route is setup properly (first to network interface or IP of router) | LFS101 Lab 14.1
network | `$ sudo mtr --report-cycles 3 8.8.8.8` | trace route continuously | LFS101 Lab 14.1
  

  
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
* classes A, B, and C distinguis a network portion of the address from a host portion of the address
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
* f*or DNS purposes, hostnames are appended with a period (dot) and a domain name, so that a machine with a hostname of **antje** could have a **fully qualified domain name (FQDN)** of **antje.linuxfoundation.org**
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
    * `$ ip link show | grep w1`
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



### Ch.36
* Lab 36.1
    * **Extra:** installing firewalld
* Lab 36.2
    * **Extra:** examining firewall-cmd
* Lab 36.3
    * **Extra:** adding services to a zone
* Lab 36.4
    * **Extra:** using the firewall GUI
