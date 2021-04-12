## Introduction

We will create an IPv6 tunnel (provided by  [Hurricane Electric](http://he.net/)  suitable for subnetting (/48).

## Configuration

In /etc/hostname.gif0:

    tunnel 207.246.122.61 209.51.161.14
    !ifconfig gif0 inet6 alias 2001:470:1f06:95f::2 2001:470:1f06:95f::1 prefixlen 128
    !route -n add -inet6 default 2001:470:1f06:95f::1

Enable it (as root):

    sh /etc/netstart gif0

## Test

Check that it works:

    freedom# route show -inet6

          
Routing tables

    Internet6:
    Destination        Gateway            Flags   Refs      Use   Mtu  Prio Iface
    default            tunnel521973.tunne UGS        0       44     -     8 gif0 
    default            fe80::fc00:1ff:fed UGS        0        0     -    56 vio0 
    ::/96              localhost          UGRS       0        0 32768     8 lo0  
    localhost          localhost          UHhl      10       20 32768     1 lo0  
    ::ffff:0.0.0.0/96  localhost          UGRS       0        0 32768     8 lo0  
    tunnel521973.tunne tunnel521973-pt.tu UHh        1        1     -     8 gif0 
    tunnel521973-pt.tu tunnel521973-pt.tu UHl        0       59     -     1 gif0 
    2001:19f0:5:11d4:: freedom.mgk.ro     UCn        0        0     -     4 vio0 
    freedom.mgk.ro     56:00:01:d3:aa:bd  UHLl       0        0     -     1 vio0 
    2002::/24          localhost          UGRS       0        0 32768     8 lo0  
    2002:7f00::/24     localhost          UGRS       0        0 32768     8 lo0  
    2002:e000::/20     localhost          UGRS       0        0 32768     8 lo0  
    2002:ff00::/24     localhost          UGRS       0        0 32768     8 lo0  
    fe80::/10          localhost          UGRS       0        3 32768     8 lo0  
    fec0::/10          localhost          UGRS       0        0 32768     8 lo0  
    fe80::%vio0/64     fe80::5400:1ff:fed UCn        1        1     -     4 vio0 
    fe80::5400:1ff:fed 56:00:01:d3:aa:bd  UHLl       0        0     -     1 vio0 
    fe80::fc00:1ff:fed fe:00:01:d3:aa:bd  UHLch      1        5     -     3 vio0 
    fe80::1%lo0        fe80::1%lo0        UHl        0        0 32768     1 lo0  
    fe80::%gif0/64     fe80::42bc:4cfd:63 Un         0        0     -     4 gif0 
    fe80::42bc:4cfd:63 fe80::42bc:4cfd:63 UHl        0        0     -     1 gif0 
    ff01::/16          localhost          UGRS       0        3 32768     8 lo0  
    ff01::%vio0/32     fe80::5400:1ff:fed Um         0        1     -     4 vio0 
    ff01::%lo0/32      fe80::1%lo0        Um         0        1 32768     4 lo0  
    ff01::%gif0/32     fe80::42bc:4cfd:63 Um         0        1     -     4 gif0 
    ff02::/16          localhost          UGRS       0        3 32768     8 lo0  
    ff02::%vio0/32     fe80::5400:1ff:fed Um         0        1     -     4 vio0 
    ff02::%lo0/32      fe80::1%lo0        Um         0        1 32768     4 lo0  
    ff02::%gif0/32     fe80::42bc:4cfd:63 Um         0        1     -     4 gif0 
    freedom# 
    freedom# 
    freedom# ping6 google.com
    PING google.com (2607:f8b0:4006:810::200e): 56 data bytes
    64 bytes from 2607:f8b0:4006:810::200e: icmp_seq=0 hlim=57 time=2.307 ms
    64 bytes from 2607:f8b0:4006:810::200e: icmp_seq=1 hlim=57 time=2.892 ms
    ^C
    --- google.com ping statistics ---
    2 packets transmitted, 2 packets received, 0.0% packet loss
    round-trip min/avg/max/std-dev = 2.307/2.599/2.892/0.292 ms
    freedom# traceroute6 google.com                                                
    traceroute6 to google.com (2607:f8b0:4006:810::200e), 64 hops max, 60 byte packets
     1  tunnel521973.tunnel.tserv4.nyc4.ipv6.he.net (2001:470:1f06:95f::1)  9.294 ms  6.861 ms  6.119 ms
     2  ve422.core1.nyc4.he.net (2001:470:0:5d::1)  2.155 ms  2.158 ms  2.876 ms
     3  core1-0-0-8.lga.net.google.com (2001:504:f::27)  1.798 ms  1.815 ms  2.393 ms
     4  2001:4860:0:1126::1 (2001:4860:0:1126::1)  3.032 ms  3.619 ms 2001:4860:0:1128::1 (2001:4860:0:1128::1)  2.576 ms
     5  2001:4860:0:1::2105 (2001:4860:0:1::2105)  2.89 ms  2.362 ms 2001:4860:0:1::2107 (2001:4860:0:1::2107)  2.052 ms
     6  lga34s14-in-x0e.1e100.net (2607:f8b0:4006:810::200e)  2.167 ms  2.114 ms  2.193 ms
    freedom#

## Routing

Assign IPs to the interface you wish to route to:

    freedom# cat /etc/hostname.enc0 

 
inet 172.24.24.1 255.255.255.0 172.24.24.255
**inet6 2001:470:8c78:_a0_::1 64**
up

Reconfigure it:

    sh /etc/netstart enc0

Enable IPv6 forwarding:

    freedom# cat /etc/sysctl.conf   
    hw.smt=1
    net.inet.ip.forwarding=1
    **net.inet6.ip6.forwarding=1**

Same thing with sysctl (as root):

    sysctl net.inet6.ip6.forwarding=1
