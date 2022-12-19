# My networking notes

## IP
- 32-bit number

=> I . II . III . IV octets

- main network (I + II octets)
- subnet  (I + II + III octets)
- host (PC) - IV octet

## Types

- Private

* [24-bit-block] 10.0.0.0       - 10.255.255.255
* [20-bit-block] 172.16.0.0     - 172.31.255.255
* [16-bit-block] 192.168.0.0    - 192.168.255.255

- Public - all other

## NAT

NAT = network address translation. This is a system that alters
IP packet headers and changes source/destination. SNAT/DNAT.

## Tools

- netstat
- nmap

## 1.1

1) ❯ ipcalc 129.167.38.54/13
Address:   129.167.38.54        10000001.10100 111.00100110.00110110
Netmask:   255.248.0.0 = 13     11111111.11111 000.00000000.00000000
Wildcard:  0.7.255.255          00000000.00000 111.11111111.11111111
=>
Network:   129.160.0.0/13       10000001.10100 000.00000000.00000000
HostMin:   129.160.0.1          10000001.10100 000.00000000.00000001
HostMax:   129.167.255.254      10000001.10100 111.11111111.11111110
Broadcast: 129.167.255.255      10000001.10100 111.11111111.11111111
Hosts/Net: 524286                Class B

2) Conversion of the mask
1. 255.255.255.0:
    prefix - 24
    binary: 1111 1111 . 1111 1111 . 1111 1111 . 0000 0000
2. Conversions: 11111111.11111111.11111111.11110000
    prefix: 28
    normal: 255.255.255.240
3. Min/max hosts in 12.167.38.4:
    Network:   12.0.0.0/8           00001100. 00000000.00000000.00000000
    * /8:
    HostMin:   12.0.0.1             00001100. 00000000.00000000.00000001
    HostMax:   12.255.255.254       00001100. 11111111.11111111.11111110
    * 11111111.11111111.00000000.00000000
    HostMin:   12.167.0.1           00001100.10100111. 00000000.00000001
    HostMax:   12.167.255.254       00001100.10100111. 11111111.11111110
    * 255.255.254.0
    HostMin:   12.167.38.1          00001100.10100111.0010011 0.00000001
    HostMax:   12.167.39.254        00001100.10100111.0010011 1.11111110
    * /4:
    HostMin:   0.0.0.1              0000 0000.00000000.00000000.00000001
    HostMax:   15.255.255.254       0000 1111.11111111.11111111.11111110

## 1.2 Localhost

* [24-bit-block] 10.0.0.0       - 10.255.255.255
* [20-bit-block] 172.16.0.0     - 172.31.255.255
* [16-bit-block] 192.168.0.0    - 192.168.255.255
* [loopback]     127.0.0.0      - 127.255.255.255

- 194.34.23.100 (-)
- 127.0.0.2 (+)
- 127.1.0.1 (+)
- 128.0.0.1 (-)

## 1.3 Network ranges and segments

##### 1) which of the listed IPs can be used as public and which only as private: 
- *10.0.0.45* (priv)
- *134.43.0.2* (pub)
- *192.168.4.2* (priv)
- *172.20.250.4* (priv)
- *172.0.2.1* (pub)
- *192.172.0.1* (pub)
- *172.68.0.2* (pub)
- *172.16.255.255* (pub)
- *10.10.10.10* (pub)
- *192.169.168.1* (pub)


##### 2) which of the listed gateway IP addresses are possible for *10.10.0.0/18* network:


Address:   10.10.0.0            00001010.00001010.00 000000.00000000
Netmask:   255.255.192.0 = 18   11111111.11111111.11 000000.00000000
Wildcard:  0.0.63.255           00000000.00000000.00 111111.11111111
=>
Network:   10.10.0.0/18         00001010.00001010.00 000000.00000000
HostMin:   10.10.0.1            00001010.00001010.00 000000.00000001
HostMax:   10.10.63.254         00001010.00001010.00 111111.11111110
Broadcast: 10.10.63.255         00001010.00001010.00 111111.11111111
Hosts/Net: 16382                 Class A, Private Internet

*10.0.0.1* (+)
*10.10.0.2* (+)
*10.10.10.10* (+)
*10.10.100.1* (-)
*10.10.1.255* (+)

## 2 - Static routing

1) Before

* lo - loopback interface
* enp0s3 - NAT VM interface to communicate with host (if we delete / misconfigure it, networking with host breaks)
* enp0s8 - internal network of VMs

ws1
```
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:08:8c:75 brd ff:ff:ff:ff:ff:ff
    inet 10.0.2.15/24 metric 100 brd 10.0.2.255 scope global dynamic enp0s3
       valid_lft 86385sec preferred_lft 86385sec
    inet6 fe80::a00:27ff:fe08:8c75/64 scope link
       valid_lft forever preferred_lft forever
3: enp0s8: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:60:ee:c0 brd ff:ff:ff:ff:ff:ff
    inet 192.168.100.10/16 brd 192.168.255.255 scope global enp0s8
       valid_lft forever preferred_lft forever
    inet6 fe80::a00:27ff:fe60:eec0/64 scope link
       valid_lft forever preferred_lft forever`
```

ws2
```
1: lo: <LOOPBACK,UP,LOWER_UP> mtu 65536 qdisc noqueue state UNKNOWN group default qlen 1000
    link/loopback 00:00:00:00:00:00 brd 00:00:00:00:00:00
    inet 127.0.0.1/8 scope host lo
       valid_lft forever preferred_lft forever
    inet6 ::1/128 scope host
       valid_lft forever preferred_lft forever
2: enp0s3: <BROADCAST,MULTICAST,UP,LOWER_UP> mtu 1500 qdisc fq_codel state UP group default qlen 1000
    link/ether 08:00:27:08:8c:75 brd ff:ff:ff:ff:ff:ff
    inet 10.0.2.15/24 metric 100 brd 10.0.2.255 scope global dynamic enp0s3
       valid_lft 86291sec preferred_lft 86291sec
    inet6 fe80::a00:27ff:fe08:8c75/64 scope link
       valid_lft forever preferred_lft forever
```

2) Setting up static ips

ws1
```yaml
network:
  version: 2
  ethernets:
    enp0s3:
      dhcp4: true
    enp0s8:
      dhcp4: false # turn off DHCP to set static IP in specified network
      addresses:
        - 192.168.100.10/16
```
`$ netplan apply` (no output)

ws2
```
network:
  version: 2
  ethernets:
    enp0s3:
      dhcp4: true
    enp0s8:
      dhcp4: false # turn off DHCP to set static IP in specified network
      addresses:
        - 172.24.116.8/12
```
`$ netplan apply` (no output)

3) Setup static routing with `ip r`

```
WS21
posidoni@ws21:~$ sudo ip route add 192.168.100.10 dev enp0s8
[sudo] password for posidoni:
posidoni@ws21:~$ ping 192.168.100.10
PING 192.168.100.10 (192.168.100.10) 56(84) bytes of data.
64 bytes from 192.168.100.10: icmp_seq=1 ttl=64 time=3.01 ms
64 bytes from 192.168.100.10: icmp_seq=2 ttl=64 time=0.990 ms
--- 192.168.100.10 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1003ms
rtt min/avg/max/mdev = 0.990/1.999/3.009/1.009 ms
---------
posidoni@ws11:~$ sudo ip route add 172.24.116.8 dev enp0s8
posidoni@ws11:~$ ping 172.24.116.8
PING 172.24.116.8 (172.24.116.8) 56(84) bytes of data.
64 bytes from 172.24.116.8: icmp_seq=1 ttl=64 time=1.48 ms
64 bytes from 172.24.116.8: icmp_seq=2 ttl=64 time=1.05 ms
64 bytes from 172.24.116.8: icmp_seq=3 ttl=64 time=1.06 ms
--- 172.24.116.8 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2005ms
rtt min/avg/max/mdev = 1.051/1.196/1.479/0.200 ms
```

4) Setting up persistent static routing via netplan utility

```yalm
network: # WS1
  version: 2
  ethernets:
    enp0s3:
      dhcp4: true
    enp0s8:
      dhcp4: false
      addresses:
        - 192.168.100.10/16
      routes:
        - to: 172.24.116.8
          via: 192.168.100.10
network: # WS21
  version: 2
  ethernets:
    enp0s3:
      dhcp4: true
    enp0s8:
      dhcp4: false
      addresses:
        - 172.24.116.8/12
      routes:
        - to: 192.168.100.10
          via: 172.24.116.8
```

Ping

```txt
== WS1
posidoni@ws11:~$ sudo netplan apply
posidoni@ws11:~$ ping 172.24.116.8
PING 172.24.116.8 (172.24.116.8) 56(84) bytes of data.
64 bytes from 172.24.116.8: icmp_seq=1 ttl=64 time=1.21 ms
64 bytes from 172.24.116.8: icmp_seq=2 ttl=64 time=1.07 ms
^C
--- 172.24.116.8 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1001ms
rtt min/avg/max/mdev = 1.070/1.139/1.208/0.069 ms

== WS2
posidoni@ws21:~$ sudo vim /etc/netplan/00-installer-config.yaml ^C
posidoni@ws21:~$ ping 192.168.100.10
PING 192.168.100.10 (192.168.100.10) 56(84) bytes of data.
64 bytes from 192.168.100.10: icmp_seq=1 ttl=64 time=1.78 ms
64 bytes from 192.168.100.10: icmp_seq=2 ttl=64 time=0.879 ms
^C
--- 192.168.100.10 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1002ms
rtt min/avg/max/mdev = 0.879/1.331/1.784/0.452 ms
```

## 3. `ipperf3` utility

3.1) Conversion:
- 8 Mbps -> 1 MB (because 1 bit = 8 bytes)
- 100 MB -> 102,400 Kbps
- 1 Gbps -> 1000 Mbps

3.2) Measure connection speed between WS1 and WS2:

```
posidoni@ws11:~$ iperf3 -s -f K # start listener on WS1
posidoni@ws21:~$ iperf3 -c 192.168.100.10 # connect to WS1 from WS2
-----------------------------------------------------------
Server listening on 5201
-----------------------------------------------------------
Accepted connection from 172.24.116.8, port 50510
[  5] local 192.168.100.10 port 5201 connected to 172.24.116.8 port 50518
[ ID] Interval           Transfer     Bitrate
[  5]   0.00-1.00   sec   132 MBytes  135017 KBytes/sec
[  5]   1.00-2.00   sec   155 MBytes  158279 KBytes/sec
[  5]   2.00-3.00   sec   152 MBytes  156118 KBytes/sec
[  5]   3.00-4.00   sec   151 MBytes  154414 KBytes/sec
[  5]   4.00-5.00   sec   143 MBytes  146272 KBytes/sec
[  5]   5.00-6.00   sec   159 MBytes  163097 KBytes/sec
[  5]   6.00-7.00   sec   155 MBytes  159156 KBytes/sec
[  5]   7.00-8.00   sec   160 MBytes  163730 KBytes/sec
[  5]   8.00-9.00   sec   132 MBytes  135199 KBytes/sec
[  5]   9.00-10.00  sec   149 MBytes  152366 KBytes/sec
[  5]  10.00-10.04  sec  3.13 MBytes  85486 KBytes/sec
- - - - - - - - - - - - - - - - - - - - - - - - -
[ ID] Interval           Transfer     Bitrate
[  5]   0.00-10.04  sec  1.46 GBytes  152113 KBytes/sec                  receiver
```

## 4. Firewalls

### 4.1 Iptables

- [Tutorial](https://web.mit.edu/rhel-doc/4/RH-DOCS/rhel-rg-en-4/s1-iptables-options.html)
- [Tutorial 2](https://www.digitalocean.com/community/tutorials/how-the-iptables-firewall-works)

The iptables firewall operates by _comparing network traffic against a set of rules. The rules define the characteristics that a network packet needs to have to match, and the action that should be taken for matching packets_.

What can match
- source/dest addr/port
- protocol type
- network interface

When packet mathes -> ACTION (target):
- final policy action: ACCEPT, DROP
- routing to another rule chain

Rules are organized in groups called chains. Just like CoR pattern.

Default chains:

INPUT: This chain handles all packets that are addressed to your server.
OUTPUT: This chain contains rules for traffic created by your server.
FORWARD: This chain is used to deal with traffic destined for other servers that are not created on your server. This chain is a way to configure your server to route requests to other machines.
Each chain can contain zero or more rules, and has a default policy. The policy determines what happens when a packet drops through all of the rules in the chain and does not match any rule. You can either drop the packet or accept the packet if no rules match.

Iptables can also track connections. This means you can create rules that define what happens to a packet based on its relationship to previous packets. The capability is “state tracking”, “connection tracking”, or configuring the “state machine”.


```sh
iptables -S # list all chains
```

```sh
iptables -t filter -A INPUT -p tcp -m tcp --dport ssh -j ACCEPT
iptables -t filter -A INPUT -p tcp -m tcp --dport http -j ACCEPT
```

```sh
# WS1
iptables -t filter -A OUTPUT -p icmp --icmp-type echo-reply -j REJECT
iptables -t filter -A OUTPUT -p icmp --icmp-type echo-reply -j ACCEPT

# WS2
iptables -t filter -A OUTPUT -p icmp --icmp-type echo-reply -j ACCEPT
iptables -t filter -A OUTPUT -p icmp --icmp-type echo-reply -j REJECT
```
 
Why does it work like that?
Because in case of WS1 chain looks like this: `REJECT PING -> ACCEPT PING`
For WS2 it is: `ACCEPT PING (jumps to ACCEPt) -> REJECT PING (unreachable code)`

```sh
# I deleted iptables -X cmd, because it didn't work
posidoni@ws11:~$ sudo /etc/firewall.sh
posidoni@ws21:~$ sudo /etc/firewall.sh
```

### 4.2. Nmap scan

```txt
posidoni@ws21:~$ nmap 192.168.100.10
Starting Nmap 7.80 ( https://nmap.org ) at 2022-12-19 06:12 UTC
Nmap scan report for 192.168.100.10
Host is up (0.0045s latency).
Not shown: 999 closed ports
PORT   STATE SERVICE
22/tcp open  ssh

Nmap done: 1 IP address (1 host up) scanned in 0.72 seconds
posidoni@ws21:~$ ping 192.168.100.10
PING 192.168.100.10 (192.168.100.10) 56(84) bytes of data.

--- 192.168.100.10 ping statistics ---
7 packets transmitted, 0 received, 100% packet loss, time 6137ms
```

## 5. Network configuration

VB config:
```
networks:
r1p (r1 private): WS1
r2p (r2 private): WS21, WS22
rsh (shared): r1, r2
```

Step one:
ws1 -> r1
ws21 <-> r2 <-> ws22

r1 !<->!r2 (r1 does not communicate with r2 yet)

```yaml
network: #ws1
  ethernets:
    enp0s3:
      dhcp4: true
    enp0s8: # ws1 private network
      dhcp4: false
      addresses:
        - 10.10.0.2/18
      routes:
        - to: 10.10.0.0/18 # to communicate with my network
          via: 10.10.0.2 # I can sent packets via myself
        - to: default # in order to speak with anyone
          via: 10.10.0.1/18 # route via default gateway of my network
network: #r1
  version: 2
  ethernets:
    enp0s3:
      dhcp4: true
    enp0s8: # r1p network
      dhcp4: false
      addresses:
        - 10.10.0.1/18
      routes:
        - to: 10.10.0.0/18 # to communicate with my network
          via: 10.10.0.1 # I can sent packets via myself
        - to: default # in order to speak with anyone
          via: 10.100.0.11/16 # route through my shared interface
    enp0s9: # shared network
      dhcp4: false
      addresses:
        - 10.100.0.11/16
      # routes: TODO: setup inter-network communication here
```

### Enable IP forwarding

With CLI:

```
posidoni@r1:~$ sudo sysctl -w net.ipv4.ip_forward=1
net.ipv4.ip_forward = 1
posidoni@r2:~$ sudo sysctl -w net.ipv4.ip_forward=1
net.ipv4.ip_forward = 1
```

With file configs:

```
# File: /etc/sysctl.conf
# Uncomment the next line to enable packet forwarding for IPv4
net.ipv4.ip_forward=1
```

### Default route configuration


### WS1 <-> R2

### Adding static routes

```bash
posidoni@ws11:~$ ip r list 0.0.0.0/0
default via 10.10.0.1 dev enp0s8 proto static

posidoni@ws11:~$ ip r list 10.10.0.0/18
10.10.0.0/18 dev enp0s8 proto kernel scope link src 10.10.0.2
10.10.0.0/18 via 10.10.0.2 dev enp0s8 proto static
```

Network 0.0.0.0/0 is not specified in our routing table. WS1 doesn't
know how to reach it. Thus, it will send packets to default gateway,
which is 10.10.0.1.

However, network 10.10.0.0/18 is explicitly mentioned in routing list.
We have a rule to send packets to ourself (10.10.0.2 - enp0s8) in
this list.


### Final (5 machines)

```
network: # ws1
  version: 2
  ethernets:
    enp0s3:
      dhcp4: true
    enp0s8:
      dhcp4: false
      addresses:
        - 10.10.0.2/18
      routes:
        - to: default # in order to speak with anyone
          via: 10.10.0.1 # route via default gateway of my network
        - to: 10.10.0.0/18 # to communicate with my network
          via: 10.10.0.2 # I can sent packets via myself
network: # WS21
  version: 2
  ethernets:
    enp0s3:
      dhcp4: true
    enp0s8: # r2p network
      dhcp4: false
        # gateway4: 10.20.0.1
      addresses:
        - 10.20.0.10/26
      routes:
        - to: default
          via: 10.20.0.1
        - to: 10.20.0.0/26 # in order to speak with my network
          via: 10.20.0.10/26 # just send packets via myself
network: # WS22
  version: 2
  ethernets:
    enp0s3:
      dhcp4: true
    enp0s8: # r2p network
      dhcp4: false
        # gateway4: 10.20.0.1
      addresses:
        - 10.20.0.20/26
      routes:
        - to: default
          via: 10.20.0.1
        - to: 10.20.0.0/26 # in order to speak with my network
          via: 10.20.0.20/26 # just send packets via myself
network: #r1
  version: 2
  ethernets:
    enp0s3:
      dhcp4: true
    enp0s8: # r1p network
      dhcp4: false
      addresses:
        - 10.10.0.1/16
      routes:
        - to: 10.10.0.0/16 # to communicate with my network
          via: 10.10.0.1   # I can sent packets via myself
        - to: 10.100.0.0/16
          via: 10.100.0.11
    enp0s9: # shared network
      dhcp4: false
      addresses:
        - 10.100.0.11/18
      routes:
        - to: 10.100.0.0/18 # to communicate with my network
          via: 10.100.0.11  # use me
        - to: 10.20.0.0/26
          via: 10.100.0.12
network: # r2
  version: 2
  ethernets:
    enp0s3:
      dhcp4: true
    enp0s8: # r2p network
      dhcp4: false
      addresses:
        - 10.20.0.1/26
      routes:
        - to: 10.20.0.0/26 # to communicate with own network
          via: 10.20.0.1 # use self
        - to: 10.100.0.0/16 #
          via: 10.100.0.12
    enp0s9: # shared network
      dhcp4: false
      addresses:
        - 10.100.0.12/16
      routes:
        - to: 10.100.0.0/16 # to communicate with own network
          via: 10.100.0.12 # use self
        - to: 10.10.0.0/18
          via: 10.100.0.11

posidoni@ws11:~$ ping r1
PING r1 (10.10.0.1) 56(84) bytes of data.
64 bytes from r1 (10.10.0.1): icmp_seq=1 ttl=64 time=1.54 ms
64 bytes from r1 (10.10.0.1): icmp_seq=2 ttl=64 time=1.02 ms
--- r1 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1001ms
rtt min/avg/max/mdev = 1.018/1.277/1.537/0.259 ms
posidoni@ws11:~$ ping r2
PING r2 (10.100.0.12) 56(84) bytes of data.
64 bytes from r2 (10.100.0.12): icmp_seq=1 ttl=63 time=1.73 ms
64 bytes from r2 (10.100.0.12): icmp_seq=2 ttl=63 time=12.0 ms
--- r2 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1001ms
rtt min/avg/max/mdev = 1.729/6.854/11.979/5.125 ms
posidoni@ws11:~$ ping ws21
PING ws21 (10.20.0.10) 56(84) bytes of data.
--- ws21 ping statistics ---
5 packets transmitted, 0 received, 100% packet loss, time 4075ms

posidoni@ws11:~$ ping ws22
PING ws22 (10.20.0.20) 56(84) bytes of data.
64 bytes from ws22 (10.20.0.20): icmp_seq=1 ttl=62 time=3.85 ms
64 bytes from ws22 (10.20.0.20): icmp_seq=2 ttl=62 time=3.13 ms
64 bytes from ws22 (10.20.0.20): icmp_seq=3 ttl=62 time=4.23 ms

--- ws22 ping statistics ---
3 packets transmitted, 3 received, 0% packet loss, time 2003ms
rtt min/avg/max/mdev = 3.133/3.735/4.226/0.453 ms

posidoni@ws11:~$ ping ws22
PING ws22 (10.20.0.20) 56(84) bytes of data.
64 bytes from ws22 (10.20.0.20): icmp_seq=1 ttl=62 time=8.93 ms
64 bytes from ws22 (10.20.0.20): icmp_seq=2 ttl=62 time=2.98 ms

--- ws22 ping statistics ---
2 packets transmitted, 2 received, 0% packet loss, time 1002ms
rtt min/avg/max/mdev = 2.982/5.958/8.934/2.976 ms
```


## Hosts file

- Hosts make networking easier and allow us to make name aliases for VMs
```
───────┬──────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
       │ File: /etc/hosts
───────┼──────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
   4   │ 127.0.0.1   localhost
   7   │ 10.8.60.78  host.docker.internal
   8   │ 10.8.60.78  gateway.docker.internal
   9   │ 127.0.0.1   kubernetes.docker.internal
───────┴──────────────────────────────────────────────────────────────────────────────────────────────────────────────────────────
```

## Default gateway

A default gateway is the node in a computer network using the Internet protocol suite that serves as the forwarding host (router) to other networks when no other route specification matches the destination IP address of a packet.

## Questions:

1. When we use netplan to assign statis ip, we must set mask. Why do we need mask? Networks are like streets, ip addresses (host addressess) are like houses numbers.
In order to find someone we need both the street and house number.

## Takeaways

1. When the subnetwork is not specified, it is implicitly set to `/0`,
so when we take any private address it is taken from all available
addresses.

2. Each subnetting divides network by 2, because this is how bitmasks
work.

3. Host id - host number in local network or network interface name
