# TCP/IP

|时间|内容|
|:------|:----|
|20191223|add introductional notes of <br>ARP, RARP<br>IP, DNS, ICMP, RIP, OSPF, BGP, CIDR, IGMP<br>UDP, TCP<br>SNMP, NFS.|


标准化过程:

```
ISOC: Internet Society
|-- IAB: Internet Architecture Board
|--|-- IETF: Internet Enginerring Task Force
|--|-- IRTF: Internet Research Task Force
```

API:

- Berkeley sockets
- AT&T TLI(Transport Layer Interface); XTI(X/Open Transport Interface)

## 资源

- TCP/IP® Illustrated, Volume 1: The Protocols. W. Richard Stevens. 2016.
- [RFC Sourcebook](http://www.networksorcery.com/enp/default.htm)
> The RFC Sourcebook is a comprehensive guide to the Request for Comments (RFCs) series of documents published by the IETF.



## 图例说明

- 使用gliffy: [tcpip.gliffy](./images/tcpip.gliffy)
- 使用RFC协议规范中文本表示法

```
 0               1               2               3
 0 1 2 3 4 5 6 7 0 1 2 3 4 5 6 7 0 1 2 3 4 5 6 7 0 1 2 3 4 5 6 7
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|     xxxx      |     xxxx      |          xxxxxxxx             |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                             xxxxxx                            |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
```


## RFC: Request for Comment

- RFC index: https://tools.ietf.org/rfc/index

- [RFC 1340 Assigned Numbers](https://tools.ietf.org/html/rfc1340): 描述Internet协议中使用的魔法数和常量. 相关RFC: 1700, 3232. [IANA Service Name and Transport Protocol Port Number Registry](https://www.iana.org/assignments/service-names-port-numbers/service-names-port-numbers.xhtml), [IANA IPv4 Multicast Address Space Registry](https://www.iana.org/assignments/multicast-addresses/multicast-addresses.xhtml)
- [RFC 1600 Internet Official Protocol Standards](https://tools.ietf.org/html/rfc1600): 描述Internet协议标准的状态. 见[Official Internet Protocol Standards](https://www.rfc-editor.org/standards)
- [RFC 1122 Requirements for Internet Hosts -- Communication Layers](https://tools.ietf.org/html/rfc1122), [RFC 1123 Requirements for Internet Hosts -- Application and Support](https://tools.ietf.org/html/rfc1123): 定义和讨论Internet主机软件的需求.
- [RFC 1009 Requirements for Internet Gateways](https://tools.ietf.org/html/rfc1812): 定义和讨论网关的需求. 相关RFC: 1812.

## Link Layer

### Ethernet

- [IEEE 802 at wikipedia](https://en.wikipedia.org/wiki/IEEE_802)
- [RFC 894 A Standard for the Transmission of IP Datagrams over Ethernet Networks](https://tools.ietf.org/html/rfc894): 描述在Ethernet中封装IP数据报的标准方法.
- [RFC 1042 A Standard for the Transmission of IP Datagrams over IEEE 802 Networks](https://tools.ietf.org/html/rfc1042): 描述IEEE 802网络中封装IP数据报和ARP请求和响应的标准方法.

- [RFC 893 Trailer Encapsulations](https://tools.ietf.org/html/rfc893): LAN帧的尾部封装, 作为文档记录.
- SLIP: Serial Line IP, [RFC 1055 A Nonstandard for Transmission of IP Datagrams over Serial Lines: SLIP](https://tools.ietf.org/html/rfc1055)(不是Internet标准)
- CSLIP: Compressed SLIP, [RFC 1144 Compressing TCP/IP Headers for Low-Speed Serial Links](https://tools.ietf.org/html/rfc1144)

- PPP: Point-to-Point Protocol, RFC 1548, RFC 1332.

- MTU: Maximum Transmission Unit, RFC 1191.
- path MTU: RFC1191.

### ARP: Adress Resolution Protocol

- [RFC 826 An Ethernet Address Resolution Protocol: Or Converting Network Protocol Addresses to 48.bit Ethernet Address for Transmission on Ethernet Hardware](https://tools.ietf.org/html/rfc826)
- [arp](http://man7.org/linux/man-pages/man8/arp.8.html)

```
$ arp -a
gda.linux (192.168.56.110) at 8:0:27:4b:50:c on vboxnet0 ifscope [ethernet]
$ sudo arp -d gda.linux
Password:
gda.linux (192.168.56.110) deleted
```

```
$ ssh zhoujiagen@192.168.56.110
```

```
Ethernet II, Src: 0a:00:27:00:00:00 (0a:00:27:00:00:00), Dst: Broadcast (ff:ff:ff:ff:ff:ff)
    Destination: Broadcast (ff:ff:ff:ff:ff:ff)
    Source: 0a:00:27:00:00:00 (0a:00:27:00:00:00)
    Type: ARP (0x0806)
Address Resolution Protocol (request)
    Hardware type: Ethernet (1)
    Protocol type: IPv4 (0x0800)
    Hardware size: 6
    Protocol size: 4
    Opcode: request (1)
    Sender MAC address: 0a:00:27:00:00:00 (0a:00:27:00:00:00)
    Sender IP address: 192.168.56.1
    Target MAC address: 00:00:00_00:00:00 (00:00:00:00:00:00)
    Target IP address: 192.168.56.110
```

```
Ethernet II, Src: PcsCompu_4b:50:0c (08:00:27:4b:50:0c), Dst: 0a:00:27:00:00:00 (0a:00:27:00:00:00)
    Destination: 0a:00:27:00:00:00 (0a:00:27:00:00:00)
    Source: PcsCompu_4b:50:0c (08:00:27:4b:50:0c)
    Type: ARP (0x0806)
    Padding: 000000000000000000000000000000000000
Address Resolution Protocol (reply)
    Hardware type: Ethernet (1)
    Protocol type: IPv4 (0x0800)
    Hardware size: 6
    Protocol size: 4
    Opcode: reply (2)
    Sender MAC address: PcsCompu_4b:50:0c (08:00:27:4b:50:0c)
    Sender IP address: 192.168.56.110
    Target MAC address: 0a:00:27:00:00:00 (0a:00:27:00:00:00)
    Target IP address: 192.168.56.1
```


### RARP: Reverse Address Resolution Protocol

- [RFC 903 A Reverse Address Resolution Protocol](https://tools.ietf.org/html/rfc903): RARP协议.

## Network Layer

### IP: Internet Protocol

- [InterNIC](https://www.internic.net/): Internet Network Information Center
- type of IP addresses: unicast, broadcast, multicast
- [RFC 791 Internet Protocol: DARPA Internet Program Protocol Specification](https://tools.ietf.org/html/rfc791): IP规范

```
 0               1               2               3
 0 1 2 3 4 5 6 7 0 1 2 3 4 5 6 7 0 1 2 3 4 5 6 7 0 1 2 3 4 5 6 7
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|Version|  IHL  |Type of Service|          Total Length         |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|         Identification        |Flags|      Fragment Offset    |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|  Time to Live |    Protocol   |         Header Checksum       |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                       Source Address                          |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                    Destination Address                        |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                    Options                    |    Padding    |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
```


- TOS: Type of Service, RFC 1340, RFC 1349.

4个TOS位: minimize delay, maximize throughput, maximize reliability, minimize monetary cost.

```
Bits 0-2:  Precedence.
Bit    3:  0 = Normal Delay,      1 = Low Delay.
Bits   4:  0 = Normal Throughput, 1 = High Throughput.
Bits   5:  0 = Normal Relibility, 1 = High Relibility.
Bit  6-7:  Reserved for Future Use.

   0     1     2     3     4     5     6     7
+-----+-----+-----+-----+-----+-----+-----+-----+
|                 |     |     |     |     |     |
|   PRECEDENCE    |  D  |  T  |  R  |  0  |  0  |
|                 |     |     |     |     |     |
+-----+-----+-----+-----+-----+-----+-----+-----+

Precedence

    111 - Network Control
    110 - Internetwork Control
    101 - CRITIC/ECP
    100 - Flash Override
    011 - Flash
    010 - Immediate
    001 - Priority
    000 - Routine
```

- flags

```
Bit 0: reserved, must be zero
Bit 1: (DF) 0 = May Fragment,  1 = Don't Fragment.
Bit 2: (MF) 0 = Last Fragment, 1 = More Fragments.

    0   1   2
  +---+---+---+
  |   | D | M |
  | 0 | F | F |
  +---+---+---+
```


- header checksum: [Compute 16-bit One's Complement Sum](http://mathforum.org/library/drmath/view/54379.html), RFC 1071, RFC 1141.

(1) checksum字段全置为0;<br>
(2) 计算头部的16位1的补码和, 放入checksum字段;<br>
(3) 接收端计算头部的16位1的补码和, 如果全为1则无错, 否则有错.

```
1000 0110 0101 1110
1010 1100 0110 0000
0111 0001 0010 1010
1000 0001 1011 0101

First, we add the 16-bit values 2 at a time:

    1000 0110 0101 1110   First 16-bit value
+   1010 1100 0110 0000   Second 16-bit value
  ---------------------
  1 0011 0010 1011 1110   Produced a carry-out, which gets added
+  \----------------> 1   back into LBb
  ---------------------
    0011 0010 1011 1111
+   0111 0001 0010 1010   Third 16-bit value
  ---------------------
  0 1010 0011 1110 1001   No carry to swing around (**)
+   1000 0001 1011 0101   Fourth 16-bit value
  ---------------------
  1 0010 0101 1001 1110   Produced a carry-out, which gets added
+  \----------------> 1   back into LBb
  ---------------------
    0010 0101 1001 1111   Our "one's complement sum"

(**) Note that we could "swing around" the carry-out of 0, but adding
0 back into the LSb has no affect on the sum. (But technically, that's
what the checksum generator does.)
```

- subnet addressing: 子网寻址, 主机ID拆分为子网ID与主机ID. [RFC 950 Internet Standard Subnetting Procedure](https://tools.ietf.org/html/rfc950)
- subnet mask
- special case IP address

- [ifconfig](http://man7.org/linux/man-pages/man8/ifconfig.8.html)
- [netstat](http://man7.org/linux/man-pages/man8/netstat.8.html)

- [RFC 8200 Internet Protocol, Version 6 (IPv6) Specification](https://tools.ietf.org/html/rfc8200)

### DNS: Domain Name System

- [RFC 1034 Domain Names - Concepts and Facilities](https://tools.ietf.org/html/rfc1034): see also updates
- [RFC 1035 Domain Names - Implementation and Specification](https://tools.ietf.org/html/rfc1035): see also updates
- [The Berkeley Internet Name Domain (BIND)](https://www.bind9.net/manuals): <br>
host— DNS lookup utility<br>
named — Internet domain name server<br>
nslookup— query Internet name servers interactively<br>
...
- [gethostbyname](http://man7.org/linux/man-pages/man3/gethostbyname.3.html), [gethostbyaddr](http://man7.org/linux/man-pages/man3/gethostbyaddr.3.html)
- UDP 53, TCP 53: 响应的`TC`位被设置时, 改为使用TCP; zone迁移使用TCP.


### ICMP: Internet Control Message Protocol

- [ICMP on wikipedia](https://en.wikipedia.org/wiki/Internet_Control_Message_Protocol)
- [RFC792 Internet Control Message Protocol](https://tools.ietf.org/html/rfc792): ICMPv4.
- [RFC 4443 Internet Control Message Protocol (ICMPv6)for the Internet Protocol Version 6 (IPv6) Specification](https://tools.ietf.org/html/rfc4443): ICMPv6.

相关RFC:

- [RFC 950 Internet Standard Subnetting Procedure](https://tools.ietf.org/html/rfc950)
- [RFC 4884 Extended ICMP to Support Multi-Part Messages](https://tools.ietf.org/html/rfc4884)
- [RFC 6633 Deprecation of ICMP Source Quench Messages](https://tools.ietf.org/html/rfc6633)
- [RFC 6918 Formally Deprecating Some ICMPv4 Message Types](https://tools.ietf.org/html/rfc6918)


[`traceroute`程序](http://man7.org/linux/man-pages/man8/traceroute.8.html): 查看IP数据报的路由<br>
路由器收到的IP数据报中TTL字段为0或1时, 不转发数据报, 而是给源主机发送ICMP "time exceed"消息.<br>
发送UDP报文到目标主机时选择不大可能被使用的端口, 预期收到ICMP "port unreachable"错误.<br>
使用`-g`使用LSRR(Loose Source and Record Route); 使用`-G`使用SSRR(Strict Source and Record Route).



消息类型:


- Echo, Echo Reply

```
 0               1               2               3
 0 1 2 3 4 5 6 7 0 1 2 3 4 5 6 7 0 1 2 3 4 5 6 7 0 1 2 3 4 5 6 7
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|     0/8       |     Code      |          Checksum             |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|           Identifier          |        Sequence Number        |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|     Data ...
+-+-+-+-+-
```

```
Type
   8 for echo message;
   0 for echo reply message.

Code
   0
```

[`ping`程序](http://man7.org/linux/man-pages/man8/ping.8.html): <br>
发送ICMP echo请求到主机, 期望返回ICMP echo响应.<br>
使用`-R`选项开启记录路由特性.<br>
使用`-T`开启IP实现戳选项.

- Destination Unreachable

```
 0               1               2               3
 0 1 2 3 4 5 6 7 0 1 2 3 4 5 6 7 0 1 2 3 4 5 6 7 0 1 2 3 4 5 6 7
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|     3         |     Code      |          Checksum             |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                             unused                            |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|      Internet Header + 64 bits of Original Data Datagram      |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
```

```
Code
   0 = net unreachable;
   1 = host unreachable;
   2 = protocol unreachable;
   3 = port unreachable;
   4 = fragmentation needed and DF set;
   5 = source route failed.

   // http://www.networksorcery.com/enp/protocol/icmp/msg3.htm
   6 = Destination network unknown error.	- RFC 1122
   7 = Destination host unknown error.	- RFC 1122
   8 = Source host isolated error. - RFC 1122
   9 = The destination network is administratively prohibited.	- RFC 1122
   10 = The destination host is administratively prohibited.	- RFC 1122
   11 = The network is unreachable for Type Of Service.	- RFC 1122
   12 = The host is unreachable for Type Of Service.	- RFC 1122
   13 = Communication Administratively Prohibited.	- RFC 1812
   14 = Host precedence violation. - RFC 1812
   15 = Precedence cutoff in effect. - RFC 1812
```

- Source Quench - deprecated by RFC 6633

```
 0               1               2               3
 0 1 2 3 4 5 6 7 0 1 2 3 4 5 6 7 0 1 2 3 4 5 6 7 0 1 2 3 4 5 6 7
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|     4         |     Code      |          Checksum             |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                             unused                            |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|      Internet Header + 64 bits of Original Data Datagram      |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
```

```
Code
   0
```

- Redirect

```
 0               1               2               3
 0 1 2 3 4 5 6 7 0 1 2 3 4 5 6 7 0 1 2 3 4 5 6 7 0 1 2 3 4 5 6 7
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|     5         |     Code      |          Checksum             |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                 Gateway Internet Address                      |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|      Internet Header + 64 bits of Original Data Datagram      |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
```

```
Code
   0 = Redirect datagrams for the Network.
   1 = Redirect datagrams for the Host.
   2 = Redirect datagrams for the Type of Service and Network.
   3 = Redirect datagrams for the Type of Service and Host.
```

当路由器接收到发送者的报文, 检测到转发的网卡与接收网卡是同一个时, 发送该ICMP消息给发送者.

- Router advertisement, Router solicitation - [RFC 1256 ICMP Router Discovery Messages](https://tools.ietf.org/html/rfc1256)

```
ICMP Router Advertisement Message

 0               1               2               3
 0 1 2 3 4 5 6 7 0 1 2 3 4 5 6 7 0 1 2 3 4 5 6 7 0 1 2 3 4 5 6 7
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|     9         |     0         |           Checksum            |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|   Num Addrs   |Addr Entry Size|           Lifetime            |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                       Router Address[1]                       |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                      Preference Level[1]                      |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                       Router Address[2]                       |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                      Preference Level[2]                      |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                               .                               |
|                               .                               |
|                               .                               |
```

```
ICMP Router Solicitation Message

 0               1               2               3
 0 1 2 3 4 5 6 7 0 1 2 3 4 5 6 7 0 1 2 3 4 5 6 7 0 1 2 3 4 5 6 7
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|     10        |     0         |           Checksum            |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                           Reserved                            |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
```


- Time Exceeded

```
 0               1               2               3
 0 1 2 3 4 5 6 7 0 1 2 3 4 5 6 7 0 1 2 3 4 5 6 7 0 1 2 3 4 5 6 7
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|     11        |     Code      |          Checksum             |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                             unused                            |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|      Internet Header + 64 bits of Original Data Datagram      |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
```

```
Code
   0 = time to live exceeded in transit;
   1 = fragment reassembly time exceeded.
```

- Parameter Problem

```
 0               1               2               3
 0 1 2 3 4 5 6 7 0 1 2 3 4 5 6 7 0 1 2 3 4 5 6 7 0 1 2 3 4 5 6 7
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|     12        |     Code      |          Checksum             |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|    Pointer    |                   unused                      |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|      Internet Header + 64 bits of Original Data Datagram      |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
```

```
Code
   0 = pointer indicates the error.
```

- Timestamp, Timestamp Reply

```
 0               1               2               3
 0 1 2 3 4 5 6 7 0 1 2 3 4 5 6 7 0 1 2 3 4 5 6 7 0 1 2 3 4 5 6 7
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|     13/14     |      Code     |          Checksum             |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|           Identifier          |        Sequence Number        |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|     Originate Timestamp                                       |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|     Receive Timestamp                                         |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|     Transmit Timestamp                                        |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
```

```
Type
   13 for timestamp message;
   14 for timestamp reply message.

Code
   0
```

- Information Request, Information Reply - Obsolete

```
 0               1               2               3
 0 1 2 3 4 5 6 7 0 1 2 3 4 5 6 7 0 1 2 3 4 5 6 7 0 1 2 3 4 5 6 7
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|     15/16     |      Code     |          Checksum             |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|           Identifier          |        Sequence Number        |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
```

```
Type
   15 for information request message;
   16 for information reply message.

Code
   0
```

- Address Mask - RFC 950

```
 0               1               2               3
 0 1 2 3 4 5 6 7 0 1 2 3 4 5 6 7 0 1 2 3 4 5 6 7 0 1 2 3 4 5 6 7
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|     17/18     |      0        |          Checksum             |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|           Identifier          |       Sequence Number         |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                        Address Mask                           |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
```

```
Type
   AM1 for address mask request message - 17
   AM2 for address mask reply message - 18

Code
   0 for address mask request message
   0 for address mask reply message
```

### IP路由

- 主机路由表: [`netstat`程序](http://man7.org/linux/man-pages/man8/netstat.8.html), `netstat -r`
- `routed`: UNIX系统运行的路由守护进程
- `gated`: 与`routed`类似, 同时支持IGP和EGP.
- 路由机制: 搜索路由表, 决定使用哪个网卡发出报文
- 路由策略: 决定将路由放入路由表的一组规则

- AS: Autonomous Systems, 自治系统
- IGP: Interior Gateway Protocol, 域内网关协议
- RIP: Routing Information Protocol, 路由信息协议, 最常见的IGP
- OSPF: Open Shortest Path First, 开放最短路径优先协议, 意图替代RIP
- EGP: Exterior Gateway Protocol, 域间网关协议
- BGP: Border Gateway Protocol, 边界网关协议, 意图替代EGP

#### RIP: Routing Information Protocol

- [RFC 1058 Routing Information Protocol](https://tools.ietf.org/html/rfc1058)
- [RFC 1388 RIP Version 2 Carrying Additional Information](https://tools.ietf.org/html/rfc1388)


#### OSPF: Open Shortest Path First

- [RFC 1247 OSPF Version 2](https://tools.ietf.org/html/rfc1247)

#### BGP: Border Gateway Protocol

- [RFC 1267 A Border Gateway Protocol 3 (BGP-3)](https://tools.ietf.org/html/rfc1267)
- [RFC 1268 Application of the Border Gateway Protocol in the Internet](https://tools.ietf.org/html/rfc1268): RFC 1655, RFC 1772

#### CIDR: Classless Interdomain Routing

- [RFC 1518 An Architecture for IP Address Allocation with CIDR](https://tools.ietf.org/html/rfc1518)
- [RFC 1519 Classless Inter-Domain Routing (CIDR): an Address Assignment and Aggregation Strategy](https://tools.ietf.org/html/rfc1519): Obsoleted by [RFC 4632 Classless Inter-domain Routing (CIDR): The Internet Address Assignment and Aggregation Plan](https://tools.ietf.org/html/rfc4632)
- [RFC 1467 Status of CIDR Deployment in the Internet](https://tools.ietf.org/html/rfc1467)

### IGMP: Internet Group Management Protocol

- 广播(broadcast)

受限的广播(255.255.255.255)、 网络定向的广播(主机ID全为1)、子网定向的广播(主机ID全为1, 有特定的子网ID)、所有子网定向的广播(子网ID与主机ID全为1)

- 多播(multicast)

D类IP地址(224.0.0.0 - 239.255.255.255): 多播组地址<br>
主机组: 监听特定IP多播地址的主机集合; IANA定义了一些永久主机组<br>
多播组地址与Ethernet地址的装换: <br>
与IP多播对应的Ethernet地址范围: 01:00:5e:00:00:00 - 01:00:5e:7f:ff:ff.<br>
D类IP地址的低23位映射到48位Ethernet地址的低23位.<br>
多播路由器需要一个协议, 来确定属于给定物理网络的任意主机是否属于一个多播组: IGMP.


- [RFC 3376 Internet Group Management Protocol, Version 3](https://tools.ietf.org/html/rfc3376): IGMPv3
- 相关RFC: [RFC 1112 Host Extensions for IP Multicasting](https://tools.ietf.org/html/rfc1112)、[RFC 2236 Internet Group Management Protocol, Version 2](https://tools.ietf.org/html/rfc2236), [RFC 4604 Using Internet Group Management Protocol Version 3 (IGMPv3) and Multicast Listener Discovery Protocol Version 2 (MLDv2) for Source-Specific Multicast](https://tools.ietf.org/html/rfc4604)

## Transport Layer

### UDP: User Datagram Protocol

- [RFC 768 User Datagram Protocol](https://tools.ietf.org/html/rfc768)

### TCP: Transmission Control Protocol

> TCP provides a connection-oriented, reliable, byte stream service.

- [RFC 793 Transmission Control Protocol - DARPA Internet Program Protocol Specification](https://tools.ietf.org/html/rfc793)<br>
[RFC 1122 Requirements for Internet Hosts -- Communication Layers](https://tools.ietf.org/html/rfc1122)<br>
[RFC 3168 The Addition of Explicit Congestion Notification (ECN) to IP](https://tools.ietf.org/html/rfc3168)<br>
[RFC 6093 On the Implementation of the TCP Urgent Mechanism](https://tools.ietf.org/html/rfc6093)<br>
[RFC 6528 Defending against Sequence Number Attacks](https://tools.ietf.org/html/rfc6528)

- [RFC 7323 TCP Extensions for High Performance](https://tools.ietf.org/html/rfc7323): Obsoletes RFC 1323.<br>
Window Scale(WS) option<br>
Timestamps(TS) option

- [RFC 1379 Extending TCP for Transactions -- Concepts](https://tools.ietf.org/html/rfc1379): T/TCP, avoid three-way handshake, shorten the TIME_WAIT state. 另一个事务协议: [RFC 1045 VMTP: Versatile Message Transaction Protocol](https://tools.ietf.org/html/rfc1045).

- [sys/socket.h - main sockets header](https://pubs.opengroup.org/onlinepubs/9699919799.2018edition/): Base Definitions - 13. Headers.

TCP头:

```
 0               1               2               3
 0 1 2 3 4 5 6 7 0 1 2 3 4 5 6 7 0 1 2 3 4 5 6 7 0 1 2 3 4 5 6 7
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|          Source Port          |       Destination Port        |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                        Sequence Number                        |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                    Acknowledgment Number                      |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|  Data |           |U|A|P|R|S|F|                               |
| Offset| Reserved  |R|C|S|S|Y|I|            Window             |
|       |           |G|K|H|T|N|N|                               |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|           Checksum            |         Urgent Pointer        |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                    Options                    |    Padding    |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
|                             data                              |
+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+-+
```

```
Control Bits:  6 bits (from left to right):

  URG:  Urgent Pointer field significant
  ACK:  Acknowledgment field significant
  PSH:  Push Function
  RST:  Reset the connection
  SYN:  Synchronize sequence numbers
  FIN:  No more data from sender
```

```
Currently defined options include (kind indicated in octal): - in RFC 793

  Kind     Length    Meaning
  ----     ------    -------
   0         -       End of option list.
   1         -       No-Operation.
   2         4       Maximum Segment Size.

RFC 7323:
Window Scale option (WSopt)
Timestamps option (TSopt)

```


#### 连接建立与终止

```
    TCP A                                                TCP B

1.  CLOSED                                               LISTEN
2.  SYN-SENT    --> <SEQ=100><CTL=SYN>               --> SYN-RECEIVED
3.  ESTABLISHED <-- <SEQ=300><ACK=101><CTL=SYN,ACK>  <-- SYN-RECEIVED
4.  ESTABLISHED --> <SEQ=101><ACK=301><CTL=ACK>       --> ESTABLISHED
5.  ESTABLISHED --> <SEQ=101><ACK=301><CTL=ACK><DATA> --> ESTABLISHED

    Basic 3-Way Handshake for Connection Synchronization
```

```
    TCP A                                            TCP B

1.  CLOSED                                           CLOSED
2.  SYN-SENT     --> <SEQ=100><CTL=SYN>              ...
3.  SYN-RECEIVED <-- <SEQ=300><CTL=SYN>              <-- SYN-SENT
4.               ... <SEQ=100><CTL=SYN>              --> SYN-RECEIVED
5.  SYN-RECEIVED --> <SEQ=100><ACK=301><CTL=SYN,ACK> ...
6.  ESTABLISHED  <-- <SEQ=300><ACK=101><CTL=SYN,ACK> <-- SYN-RECEIVED
7.               ... <SEQ=101><ACK=301><CTL=ACK>     --> ESTABLISHED

          Simultaneous Connection Synchronization
```

```
    TCP A                                                TCP B

1.  ESTABLISHED                                          ESTABLISHED
2.  (Close)
FIN-WAIT-1  --> <SEQ=100><ACK=300><CTL=FIN,ACK>  --> CLOSE-WAIT
3.  FIN-WAIT-2  <-- <SEQ=300><ACK=101><CTL=ACK>      <-- CLOSE-WAIT
4.                                                       (Close)
TIME-WAIT   <-- <SEQ=300><ACK=101><CTL=FIN,ACK>  <-- LAST-ACK
5.  TIME-WAIT   --> <SEQ=101><ACK=301><CTL=ACK>      --> CLOSED
6.  (2 MSL)
CLOSED

                   Normal Close Sequence
```


```
    TCP A                                                TCP B

 1.  ESTABLISHED                                          ESTABLISHED
 2.  (Close)                                              (Close)
     FIN-WAIT-1  --> <SEQ=100><ACK=300><CTL=FIN,ACK>  ... FIN-WAIT-1
                 <-- <SEQ=300><ACK=100><CTL=FIN,ACK>  <--
                 ... <SEQ=100><ACK=300><CTL=FIN,ACK>  -->
 3.  CLOSING     --> <SEQ=101><ACK=301><CTL=ACK>      ... CLOSING
                 <-- <SEQ=301><ACK=101><CTL=ACK>      <--
                 ... <SEQ=101><ACK=301><CTL=ACK>      -->
 4.  TIME-WAIT                                            TIME-WAIT
     (2 MSL)                                              (2 MSL)
     CLOSED                                               CLOSED

                     Simultaneous Close Sequence

```


#### 交互数据流(interactive data flow)

- delayed ACKs: 通常TCP不是在接收到数据时立即发送ACK, 而是延迟该ACK, 希望有与该ACK同方向的数据需要发送.<br>
TCP如有周期为200ms的定时器, 会在自内核启动的每200ms触发.
- [RFC 896 Congestion Control in IP/TCP Internetworks](https://tools.ietf.org/html/rfc896): the Nagle algorithm<br>
当TCP连接中有未被确认的数据时, 小片段在这些数据被确认之前不能发送. <br>
TCP收集这些小数据, 当确认到达时, 在一个片段中发送这些数据.


#### 块数据流(bulk data flow)

- sliding window: RFC 793 - 3.7.  Data Communication - Managing the Window
- [RFC 5681 TCP Congestion Control](https://tools.ietf.org/html/rfc5681): congestion control algorithms:<br>
slow start: congestion window<br>
congestion avoidance<br>
fast retansmit<br>
fast recovery<br>
- bandwidth-delay product

```
capacity(bit) of pipe = bandwitdh(bits/sec) * round-trip time(sec)
```

#### 定时器(timer)

TCP为每个连接维护了4个定时器:

- 重传定时器(retransmission timer): 期望另一端的ACK时使用.
- 坚持定时器(persist timer): 甚至在另一端关闭接收窗口时, 保持窗口大小信息流动.
- 保活定时器(keepalive timer): 检测空闲连接的另一端何时奔溃或重启.
- 2MSL定时器: 度量连接已在TIME_WAIT状态的时间.

#### 超时与重传(timeout and retransmission)

RFC 793中的估算方法:

- RTT: round-trip time
- $M$: the measured RTT
- $R$: smoothed RTT estimator, $R \leftarrow \alpha R + (1- \alpha)M$, 平滑因子建议值$\alpha = 0.9$
- RTO: retransmission timeout value, $RTO = R \beta$, 延迟方差因子建议值$\beta = 2$

Jacobson提出的估算方法:

- $A$: the smoothed RTT
- $D$: the smoothed mean deviation
- $Err$: the difference between the measured value just obtained and the current RTT estimator
- $g$: gain for the average, $g = 0.125$
- $h$: gain for the deviation, $h = 0.25$

$$
Err = M - A \\
A \leftarrow A + g Err \\
D \leftarrow D + h(|Err| - D) \\
RTO = A + 4D
$$

##### 拥塞避免与慢启动

> Jacobson V. Congestion avoidance and control[C]//ACM SIGCOMM computer communication review. ACM, 1988, 18(4): 314-329.

- 假设: 因损坏而产生的包丢失的几率很小(远小于1%), 包的丢失表明源与目的地之间的网络有拥塞.
- 包丢失的迹象: 出现超时, 收到重复的ACK.

连接上的变量:

- cwnd: 拥塞窗口
- ssthresh: 慢启动阈值

算法操作:

1. 给连接初始化变量: cwnd=1个片段大小, ssthresh=65535 B.
2. TCP输出例程从不发送超过 min(cwnd, 接收者的广告窗口大小)大小的数据.
3. 出现拥塞时, ssthresh=当前窗口大小 / 2; 当前窗口大小是min(cwnd, 接收者的广告窗口), 但至少是2个片段大小. <br>此外, 如果是超时迹象的拥塞, cwnd=1个片段大小(即慢启动).
4. 当新的数据被另一端确认时, 增加cwnd.<br>当cwnd <= ssthresh时, 在做慢启动; 否则在做拥塞避免.<br>慢启动以cwnd=1一个片段开始, 每次收到ACK时增加1个片段.<br>拥塞避免在每次收到ACK时, 给cwnd增加1 / cwnd, 即 $cwnd \leftarrow cwnd + \frac{segsize \times segsize}{cwnd} + \frac{segsize}{8}$.

##### 快速重传与快速恢复

- TCP在接收到乱序片段时, 被要求立即生成确认(重复的ACK).
- 如果一段收到三个或多个重复的ACK, 这是包丢失的一个强暗示.
- 快速重传: 不等待重传定时器超时, 而是立即重传变现为丢失的片段.
- 快速恢复: 执行拥塞避免而不是慢启动.

算法操作:

1. 当收到第三个重复的ACK时, 设置ssthresh=min(cwnd, 接收者的广告窗口大小) / 2.<br>重传丢失的片段.<br>设置cwnd=ssthresh + 3 * 该片段大小.
2. 每次收到其他重复的ACK时, 给cwnd增加1个片段, 新的cwnd允许时发送包.
3. 当收到确认新数据的ACK时, 设置cwnd=ssthresh.(拥塞避免)


## Application Layer

- IANA: Internet Assigned Numbers Authority, [Service Name and Transport Protocol Port Number Registry](https://www.iana.org/assignments/service-names-port-numbers/service-names-port-numbers.xhtml)

### SNMP: Simple Network Management Protocol

- [RFC 1157 A Simple Network Management Protocol (SNMP)](https://tools.ietf.org/html/rfc1157): SNMPv1.
- [RFC 1441 Introduction to version 2 of the Internet-standard Network Management Framework](https://tools.ietf.org/html/rfc1441): SNMPv2.
- [RFC 1213 Management Information Base for Network Management of TCP/IP-based internets: MIB-II](https://tools.ietf.org/html/rfc1213): MIB-II.
- [RFC 1155 Structure and Identification of Management Information for TCP/IP-based Internets](https://tools.ietf.org/html/rfc1155): SMI.

- ASN.1: Abstract Syntax Notation 1
- BER: Basic Encoding Rules

```
RFC1157-SNMP DEFINITIONS ::= BEGIN

 IMPORTS
      ObjectName, ObjectSyntax, NetworkAddress, IpAddress, TimeTicks
              FROM RFC1155-SMI;


 -- top-level message

         Message ::=
                 SEQUENCE {
                      version        -- version-1 for this RFC
                         INTEGER {
                             version-1(0)
                         },

                     community      -- community name
                         OCTET STRING,

                     data           -- e.g., PDUs if trivial
                         ANY        -- authentication is being used
                 }

  -- protocol data units

          PDUs ::=
                  CHOICE {
                      get-request
                          GetRequest-PDU,

                      get-next-request
                          GetNextRequest-PDU,

                      get-response
                          GetResponse-PDU,

                      set-request
                          SetRequest-PDU,

                      trap
                          Trap-PDU
                       }

  -- the individual PDUs and commonly used
  -- data types will be defined later

  END
```


```
RFC1213-MIB DEFINITIONS ::= BEGIN

...

-- groups in MIB-II

system       OBJECT IDENTIFIER ::= { mib-2 1 }

interfaces   OBJECT IDENTIFIER ::= { mib-2 2 }

at           OBJECT IDENTIFIER ::= { mib-2 3 }

ip           OBJECT IDENTIFIER ::= { mib-2 4 }

icmp         OBJECT IDENTIFIER ::= { mib-2 5 }

tcp          OBJECT IDENTIFIER ::= { mib-2 6 }

udp          OBJECT IDENTIFIER ::= { mib-2 7 }

egp          OBJECT IDENTIFIER ::= { mib-2 8 }

-- historical (some say hysterical)
-- cmot      OBJECT IDENTIFIER ::= { mib-2 9 }

transmission OBJECT IDENTIFIER ::= { mib-2 10 }

snmp         OBJECT IDENTIFIER ::= { mib-2 11 }
```

### NFS: Network File System

- [Network File System at wikipedia](https://en.wikipedia.org/wiki/Network_File_System)

- [RFC 1057 RPC: Remote Procedure Call Version 2](https://tools.ietf.org/html/rfc1057)
- [RFC 1014 XDR: External Data Representation Standard](https://tools.ietf.org/html/rfc1014)
- [RFC 1094 NFS: Network File System Protocol Specification](https://tools.ietf.org/html/rfc1094): NFS v2
- [RFC 1813 NFS Version 3 Protocol Specification](https://tools.ietf.org/html/rfc1813): NFS v3
- [RFC 7530 Network File System (NFS) Version 4 Protocol](https://tools.ietf.org/html/rfc7530): NFS v4<br>
[RFC 5661 Network File System (NFS) Version 4 Minor Version 1 Protocol](https://tools.ietf.org/html/rfc5661): NFS v4.1<br>
[RFC 7862 Network File System (NFS) Version 4 Minor Version 2 Protocol](https://tools.ietf.org/html/rfc7862): NFS v4.2
