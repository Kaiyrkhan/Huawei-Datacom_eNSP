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
vlan batch 11 12
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
 port trunk allow-pass vlan 11 12
 quit

interface g0/0/2
 port link-type trunk
 port trunk allow-pass vlan 11 12
 quit

display port vlan
```

```shell
stp region-configuration
 region-name HQ1
 instance 1 vlan 11
 instance 2 vlan 12
 active region-configuration
 quit
```

```shell
```

```shell
```

```shell
```

```shell
```

```shell
```

```shell
```

```shell
```

```shell
```

```shell
```
