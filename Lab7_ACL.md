# Access Control List (ACL) Configuration on Huawei Router

### 🖧 Network Topology
![Topology](images/Lab7_NetworkTopology_ACL.png)  
[Download Link for eNSP Topology File](Topology/Lab7_NetworkTopology_ACL.topo)

## Scenario:
1) Configure IP address;
2) Configure OSPF;
3) Configure Remote Access (Telnet);
4) Create ACL;
5) Verification.

## Step1: Configure IP address

R1
```shell
undo terminal monitor
system-view
sysname R1

int g0/0/0
 ip address 10.1.1.101 30
int loopback0
 ip address 50.1.1.1 32
int loopback1
 ip address 50.2.2.2 32
quit
```
R2
```shell
undo terminal monitor
system-view
sysname R3

int g0/0/1
 ip address 10.1.1.102 30
quit
```

## Step2: Configure OSPF

R1
```shell
ospf
area 0
network 50.1.1.1 0.0.0.0
network 50.2.2.2 0.0.0.0
network 10.1.1.100 0.0.0.3
return
```
R2
```shell
ospf
area 0
network 10.1.1.100 0.0.0.3
return
```
```shell
ping 50.1.1.1
ping 50.2.2.2
ping 10.1.1.101
```

## Step3: Configure Remote Access (Telnet)

R2
```shell
telnet server enable
user-interface vty 0 4
 user privilege level 3
 set authentication password cipher Huawei@123
```

## Step4: Create ACL for Telnet

R2
```shell
acl 3001
 rule 5 permit tcp source 50.2.2.2 0 destination 10.1.1.102 0 destination-port eq 23
 rule 10 deny tcp source any
 display this

user-interface vty 0 4
acl 3001 inbound
немесе
int g0/0/1
traffic-filter inbound acl 3001

display acl 3001
display cu section acl
```

Verification:
```shell
<R1> telnet -a 10.1.1.101 10.1.1.102
<R1> telnet -a 50.1.1.1 10.1.1.102
<R1> telnet -a 50.2.2.2 10.1.1.102
```

## Step5: Create ACL for ICMP

R1
```shell
ping -a 10.1.1.101 10.1.1.102
ping -a 50.1.1.1 10.1.1.102
ping -a 50.2.2.2 10.1.1.102
```

R2
```shell
acl 3002
 rule 5 deny icmp source 50.2.2.2 0 destination 10.1.1.102 0
 rule 10 permit icmp source any

int g0/0/1
traffic-filter inbound acl 3002

display acl 3002
display cu section acl
```

Verification:
```shell
<R1> ping -a 10.1.1.101 10.1.1.102
Reply from 10.1.1.102: bytes=56 Sequence=1 ttl=255 time=20 ms

<R1> ping -a 50.1.1.1 10.1.1.102
Reply from 10.1.1.102: bytes=56 Sequence=1 ttl=255 time=20 ms

<R1> ping -a 50.2.2.2 10.1.1.102
Request time out
```

## inter-VLAN Routing

SW1
```shell
undo terminal monitor
system-view
sysname SW1
```
```shell
vlan batch 10 20
display vlan

int g0/0/2
 port link-type access
 port default vlan 10
int g0/0/3
 port link-type access
 port default vlan 10

int g0/0/4
 port link-type access
 port default vlan 20
int g0/0/5
 port link-type access
 port default vlan 20

display vlan
```
```shell
int g0/0/1
 port link-type trunk
 port trunk allow-pass vlan 10 20
 display this
```

RT1
```shell
undo terminal monitor
system-view
sysname RT1

int g0/0/0.10
 ip address 172.16.10.1 24
 dot1q termination vid 10
 arp broadcast enable
quit
int g0/0/0.20
 ip address 172.16.20.1 24
 dot1q termination vid 20
 arp broadcast enable
quit

display ip int brief
```

Verification
```shell
<PC1> ping 172.16.10.102
<PC1> ping 172.16.20.101
<PC1> ping 172.16.20.102
```

RT2
```shell
acl 3000
 rule 5 deny ip source 172.16.10.0 0.0.0.255 destination 172.16.20.0 0.0.0.255
 rule 10 permit ip source any destination any
 display this

int g0/0/0
 traffic-filter inbound acl 3000
 display this
```
```shell
display acl 3000
display cu section acl
```
Verification
```shell
<PC1> ping 172.16.10.102
<PC1> ping 172.16.20.101

<PC3> ping 172.16.20.102
<PC3> ping 172.16.10.101
```
