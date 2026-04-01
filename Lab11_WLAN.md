# Configure WLAN on Huawei VRP

### 🖧 Network Topology (желі топологиясы)
![Topology](images/Lab11_NetworkTopology_WLAN.png)  
[Download Link for eNSP Topology File](Topology/Lab11_NetworkTopology_WLAN.topo)

| Item                    | Value      |
| ----------------------- | ---------- |
| Management VLAN for APs | VLAN 43    |
| Service VLAN for STAs   | VLAN 200   |

## Step1: Configure the IP Address

```shell
<Huawei> system-view
[Huawei] sysname EdgeR1
[EdgeR1]

int g0/0/0
 ip address 192.168.137.254 24
 quit
int g0/0/1
 ip address 10.1.77.1 24
 quit
display ip int brief
```
