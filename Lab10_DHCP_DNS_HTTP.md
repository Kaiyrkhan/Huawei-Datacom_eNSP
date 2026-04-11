## Scenario
1) VLAN құру және Access, Trunk port
2) Link Aggregation. LACP
3) MSTP (Multiple Spanning Tree Protocol)
4) VRRP (Virtual Router Redundancy Protocol)
5) OSPF
6) DHCP
7) NAT (Easy IP)
8) DNS және HTTP

## A1 and A2 Switch
```shell
undo terminal monitor
system-view
sysname A1
```

```shell
vlan batch 11 12 50
display vlan
```

```shell
interface Ethernet0/0/1
 port link-type access
 port default vlan 11
 quit
interface Ethernet0/0/3
 port link-type access
 port default vlan 11
 quit
interface Ethernet0/0/2
 port link-type access
 port default vlan 12
 quit

display vlan
```

```shell
interface g0/0/1
 port link-type trunk
 port trunk allow-pass vlan 11 12 50
 quit

interface g0/0/2
 port link-type trunk
 port trunk allow-pass vlan 11 12 50
 quit

display port vlan
```

## D1 and D2 Switch
```shell
undo terminal monitor
system-view
sysname D1
```

```shell
vlan batch 11 12 50
display vlan
```

```shell
interface Eth-Trunk 1
 port link-type trunk
 port trunk allow-pass vlan 11 12 50
 mode lacp-static

display port vlan
```

```shell
interface g0/0/3
 eth-trunk 1
 quit
interface g0/0/4
 eth-trunk 1
 quit
```

```shell
display int brief
display eth-trunk 1
display int eth-trunk 1
```

```shell
interface g0/0/1
 port link-type trunk
 port trunk allow-pass vlan 11 12 50
 quit

interface g0/0/2
 port link-type trunk
 port trunk allow-pass vlan 11 12 50
 quit

display port vlan
```

```shell
display stp

stp region-configuration
 region-name HQ1
 instance 1 vlan 10
 instance 2 vlan 20
 instance 3 vlan 50
 active region-configuration
 quit

check region-configuration
```

D1 Switch
```shell
stp instance 1 root primary
stp instance 2 root secondary
stp instance 3 root primary
```

D2 Switch
```shell
stp instance 2 root primary
stp instance 1 root secondary
stp instance 3 root secondary
```

## A1 and A2 Switch
```shell
stp region-configuration
 region-name HQ1
 instance 1 vlan 11
 instance 2 vlan 12
 instance 3 vlan 50
 active region-configuration
 quit

check region-configuration
```

```shell
display stp instance 1 brief
display stp instance 2 brief
display stp instance 3 brief
немесе
display stp vlan 11
display stp vlan 12
display stp vlan 50
```

## Configure VRRP (Virtual Router Redundancy Protocol)

D1 Switch
```shell
interface vlanif 11
 ip address 172.16.11.1 24
 vrrp vrid 11 virtual-ip 172.16.11.254
 vrrp vrid 11 priority 105
 quit

interface vlanif 12
 ip address 172.16.12.1 24
 vrrp vrid 12 virtual-ip 172.16.12.254
 quit

interface vlanif 50
 ip address 10.1.50.1 24
 vrrp vrid 50 virtual-ip 10.1.50.254
 vrrp vrid 50 priority 105
 quit

display ip int brief
display vrrp brief
```

D2 Switch
```shell
interface vlanif 11
 ip address 172.16.11.2 24
 vrrp vrid 11 virtual-ip 172.16.11.254
 quit

interface vlanif 12
 ip address 172.16.12.2 24
 vrrp vrid 12 virtual-ip 172.16.12.254
 vrrp vrid 12 priority 105
 quit

interface vlanif 50
 ip address 10.1.50.2 24
 vrrp vrid 50 virtual-ip 10.1.50.254
 quit

display ip int brief
display vrrp brief
```

## Configure Single-Area OSPF

D1 Switch

```shell
interface vlanif 1
 ip address 10.1.1.106 30
 quit
interface Loopback 50
 ip address 50.3.3.3 32
 quit

display ip int brief
```

```shell
ospf 1 router-id 50.3.3.3
 area 0
 network 10.1.1.104 0.0.0.3
 network 172.16.11.0 0.0.0.255
 network 172.16.12.0 0.0.0.255
 network 10.1.50.0 0.0.0.255
 network 50.3.3.3 0.0.0.0

display cu | begin ospf
```

D2 Switch
```shell
interface vlanif 1
 ip address 10.1.1.110 30
 quit
interface Loopback 50
 ip address 50.4.4.4 32
 quit

display ip int brief
```

```shell
ospf 1 router-id 50.4.4.4
 area 0
 network 10.1.1.108 0.0.0.3
 network 172.16.11.0 0.0.0.255
 network 172.16.12.0 0.0.0.255
 network 10.1.50.0 0.0.0.255
 network 50.4.4.4 0.0.0.0

display ospf peer
```

C1 Switch
```shell
undo terminal monitor
system-view
sysname C1

interface g0/0/0
 ip address 10.1.1.102 30
 quit
interface g0/0/1
 ip address 10.1.1.105 30
 quit
interface g0/0/2
 ip address 10.1.1.109 30
 quit
interface g0/0/3
 ip address 10.10.10.1 24
 quit
interface Loopback 50
 ip address 50.2.2.2 32
 quit

display ip int brief

ospf 1 router-id 50.2.2.2
 area 0
 network 10.1.1.100 0.0.0.3
 network 10.1.1.104 0.0.0.3
 network 10.1.1.108 0.0.0.3
 network 10.10.10.0 0.0.0.255
 network 50.2.2.2 0.0.0.0

display ospf peer
```

EdgeRT1
```shell
undo terminal monitor
system-view
sysname EdgeRT1

interface g0/0/0
 ip address 10.1.1.101 30
 quit
interface g0/0/2
 ip address 172.16.128.1 24
 quit
interface g0/0/1
 ip address 192.168.137.254 24
 quit
interface Loopback 50
 ip address 50.1.1.1 32
 quit

display ip int brief

ospf 1 router-id 50.1.1.1
 area 0
 network 10.1.1.100 0.0.0.3
 network 172.16.128.0 0.0.0.255
 network 50.1.1.1 0.0.0.0

display ospf peer
```

## Configure DHCP Server
```shell
undo terminal monitor
system-view
sysname DHCP

interface g0/0/0
 ip address 10.10.10.67 24
 quit
interface Loopback 50
 ip address 50.5.5.5 32
 quit

display ip int brief

ospf 1 router-id 50.5.5.5
 area 0
 network 10.10.10.0 0.0.0.255
 network 50.5.5.5 0.0.0.0
 quit

display ospf peer
```

```shell
dhcp enable

ip pool VLAN11
 network 172.16.11.0 mask 24
 gateway-list 172.16.11.254
 dns-list 172.16.128.53
 excluded-ip-address 172.16.11.1 172.16.11.10
 excluded-ip-address 172.16.11.251 172.16.11.253
 lease day 5

ip pool VLAN12
 network 172.16.12.0 mask 24
 gateway-list 172.16.12.254
 dns-list 172.16.128.53
 excluded-ip-address 172.16.12.1 172.16.12.10
 excluded-ip-address 172.16.12.251 172.16.12.253
 lease day 5

interface g0/0/0
 dhcp select global

display ip pool
display ip pool name VLAN11
display dhcp server statistics
```

DHCP Relay Agent (D1 and D2 Switch)
```shell
dhcp enable

interface vlanif 11
 dhcp select relay
 dhcp relay server-ip 50.5.5.5

interface vlanif 12
 dhcp select relay
 dhcp relay server-ip 50.5.5.5
```

```shell
```

```shell
```

```shell
```

```shell
```
