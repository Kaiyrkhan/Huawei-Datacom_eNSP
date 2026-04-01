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

## D1

```shell
<Huawei> undo terminal monitor
<Huawei> system-view
[Huawei] sysname D1
[D1]

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
```

```shell
int vlanif 43
 ip address 10.1.43.254 24
 description Gateway for APs
display this

int vlanif 200
 ip address 192.168.200.254 24
 description Gateway for STAs
display this
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
