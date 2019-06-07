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
  