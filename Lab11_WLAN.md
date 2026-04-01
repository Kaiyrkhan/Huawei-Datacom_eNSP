# Configure WLAN on Huawei VRP

### 🖧 Network Topology (желі топологиясы)
![Topology](images/Lab11_NetworkTopology_WLAN.png)  
[Download Link for eNSP Topology File](Topology/Lab11_NetworkTopology_WLAN.topo)

Table - WLAN Data Plan
| Item                            | Value                                                                                     |
| ------------------------------- | ----------------------------------------------------------------------------------------- |
| Management VLAN for APs         | VLAN 43                                                                                   |
| Service VLAN for STAs           | VLAN 200                                                                                  |
| Default Gateway address of APs  | 10.1.43.254                                                                               |
| IP address Pool APs             | 10.1.43.100-10.1.43.200/24                                                                |
| Default Gateway address of STAs | 192.168.200.254                                                                           |
| IP address Pool STAs            | 192.168.200.10-192.168.200.250/24                                                         |
| AP group                        | Name: ap-group1                                                                           |
|                                 | Referenced profiles: VAP profile **WLAN-Guest** and Regulatory domain profile **default** |
| Regulatory domain profile       | Name: default                                                                             |
|                                 | Country code: KZ                                                                          |
| SSID profile                    | Name: WLAN-Guest                                                                          |
|                                 | SSID name: Guest-WiFi                                                                     |
| Security profile                | Name: WLAN-Guest                                                                          |
|                                 | Security policy: WPA-WPA2+PSK+AES                                                         |
|                                 | Password: Huawei@123                                                                      |
| VAP profile                     | Name: WLAN-Guest                                                                          |
|                                 | Forwarding mode: direct forwarding                                                        |
|                                 | Service VLAN: 200                                                                         |
|                                 | Referenced profiles: SSID profile **WLAN-Guest** and Security profile **WLAN-Guest**      |

## D1 Switch

```shell
<Huawei> undo terminal monitor

<Huawei> system-view
[Huawei] sysname D1
[D1]
```

```shell
vlan batch 43 200
display vlan

interface g0/0/10
 port link-type trunk
 port trunk allow-pass vlan 43 200

interface g0/0/13
 port link-type trunk
 port trunk allow-pass vlan 43 200

interface g0/0/14
 port link-type trunk
 port trunk allow-pass vlan 43 200

display port vlan
```

```shell
interface Loopback 50
 ip address 50.1.1.1 32

interface vlanif 200
 ip address 192.168.200.254 24
 description Gateway for STAs

display ip int brief
```
> Switched Virtual Interface (SVI)  

```shell
dhcp enable
ip pool STAs
 network 192.168.200.0 mask 24
 gateway-list 192.168.200.254
 dns-list 8.8.8.8
 excluded-ip-address 192.168.200.1 192.168.200.10
 excluded-ip-address 192.168.200.251 192.168.200.253
 lease day 5

interface vlanif 200
 dhcp select global

display ip pool
```

## Access Controller

```shell
<Huawei> undo terminal monitor

<Huawei> system-view
[Huawei] sysname AC1
[AC1]
```

```shell
vlan batch 43 200
display vlan brief

interface g0/0/10
 port link-type trunk
 port trunk allow-pass vlan 43 200

display vlan brief
display port vlan
```

```shell
interface vlanif 43
 ip address 10.1.43.254 24
 description Gateway for APs

display ip int brief
```

```shell
dhcp enable
 ip pool APs
 network 10.1.43.0 mask 24
 gateway-list 10.1.43.254
 dns-list 8.8.8.8
 excluded-ip-address 10.1.43.1 10.1.43.100
 excluded-ip-address 10.1.43.201 10.1.43.253
 lease day 5

interface vlanif 15
 dhcp select global

display ip pool
```

```shell
```
