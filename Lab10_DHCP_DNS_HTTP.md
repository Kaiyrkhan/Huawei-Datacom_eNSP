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
```

D1 Switch
```shell
stp instance 1 priority root primary
stp instance 2 priority root secondary
stp instance 3 priority root primary
```

D2 Switch
```shell
stp instance 2 priority root primary
stp instance 1 priority root secondary
stp instance 3 priority root secondary
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

```shell
```

```shell
```
