# Configure NTP Server & Client on Huawei VRP

### 🖧 Network Topology (желі топологиясы)
![Topology](images/Lab10_NetworkTopology_NTP.png) 

| Device | Role       | interface | IP Address /Prefix |
| ------ | ---------- | --------- | ------------------ |
| EdgeR1 | NTP Server | g0/0/1    | 10.1.77.1 /24      |
| R1     | NTP Client | g0/0/0    | 10.1.77.101 /24    |
| S1     | NTP Client | Vlanif1   | 10.1.77.102 /24    |

### Scenario
1) Configure the IP Address;
2) Configure Single-Area OSPFv2;
3) Configure NAT (Easy IP);
4) Configure NTP Server;
5) Configure NTP Client.

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

```shell
[EdgeR1] ping 192.168.137.1
 Request time out
```
Windows+R ➜ Turn off Windows Defender Firewall  
![images](images/windows_firewall_on_to_off.png)

```shell
[EdgeR1] ping 192.168.137.1
 Reply from 192.168.137.1: bytes=56 Sequence=1 ttl=128 time=10 ms
```

```shell
<Huawei> system-view
[Huawei] sysname R1
[R1]

int g0/0/0
 ip address 10.1.77.101 24
 quit
display ip int brief
```
```shell
ping 10.1.77.1
 Reply from 10.1.77.1: bytes=56 Sequence=1 ttl=255 time=40 ms
```

```shell
<Huawei> system-view
[Huawei] sysname S1
[S1]

int Vlanif 1
 ip address 10.1.77.102 24
 quit
display ip int brief
```
```shell
ping 10.1.77.1
 Reply from 10.1.77.1: bytes=56 Sequence=1 ttl=255 time=60 ms
```

## Step2: Configure Single-Area OSPFv2

```shell
[EdgeR1] display ip int brief
[EdgeR1] ospf 1 router-id 1.1.1.1
          area 0
           network 10.1.77.0 0.0.0.255
```

Configure the Default Static Route
```shell
[EdgeR1] ip route-static 0.0.0.0 0.0.0.0 192.168.137.1
```

Advertise the Default Route
```shell
[EdgeR1] ospf 1
          default-route-advertise
```

```shell
[R1] display ip int brief
[R1] ospf 1 router-id 2.2.2.2
      area 0
       network 10.1.77.0 0.0.0.255
```

```shell
[S1] display ip int brief
[S1] ospf 1 router-id 3.3.3.3
      area 0
       network 10.1.77.0 0.0.0.255
```

```shell
display ospf peer
display ospf peer brief

display ip routing-table

display cu section ospf
display cu | begin ospf
```

## Step3: Configure NAT (Easy IP)

```shell
[EdgeR1] ping 8.8.8.8
 Request time out
 Request time out
```

Configure the Default Static Route (EdgeR1)
```shell
[EdgeR1] ip route-static 0.0.0.0 0.0.0.0 192.168.137.1
[EdgeR1] display ip routing-table
```

```shell
[EdgeR1] ping 8.8.8.8
 Reply from 8.8.8.8: bytes=56 Sequence=1 ttl=108 time=100 ms
 Reply from 8.8.8.8: bytes=56 Sequence=2 ttl=108 time=90 ms
```

```shell
[R1] ping 8.8.8.8
 Request time out
[S1] ping 8.8.8.8
 Request time out
```

Configure NAT (Easy IP)
```shell
[EdgeR1] acl 2000
          rule permit source 10.1.77.0 0.0.0.255
[EdgeR1] int g0/0/0
          nat outbound 2000
```

Configure the Default Static Route (R1, S1)
```shell
[S1] ip route-static 0.0.0.0 0.0.0.0 10.1.77.1
[R1] ip route-static 0.0.0.0 0.0.0.0 10.1.77.1
```

Verify the Configuration
```shell
[R1] ping 8.8.8.8
 Reply from 8.8.8.8: bytes=56 Sequence=1 ttl=107 time=150 ms
 Reply from 8.8.8.8: bytes=56 Sequence=2 ttl=107 time=130 ms

[S1] ping 8.8.8.8
 Reply from 8.8.8.8: bytes=56 Sequence=1 ttl=107 time=140 ms
 Reply from 8.8.8.8: bytes=56 Sequence=2 ttl=107 time=120 ms
```

## Step3: NTP серверді конфигурациялау

**NTP серверін іске қосу**

1-әдіс: LOCAL-ды құрылғының уақытын NTP сервер ретінде қолдану
```shell
ntp-service refclock-master 2
```

2-әдіс: Сыртқы NTP сервер уақытын қолдану
```shell
undo ntp-service refclock-master               // LOCAL-ды уақытты өшіру
ntp-service unicast-server 80.241.0.72
```

3-әдіс: Сыртқы NTP сервер уақытымен бірге LOCAL-ды құрылғының уақытын (резервті NTP сервер ретінде) қолдану
```shell
ntp-service unicast-server 80.241.0.72
ntp-service refclock-master 5                 // тек қосымша (резерв) NTP сервер ретінде қолданылады
```
> Best practice бойынша Production ортада 2 немесе 3-әдісті қолдану ұсынылады!  

**NTP аутентификация**
```shell
ntp-service authentication enable
ntp-service authentication-keyid 1 authentication-mode md5 Datacom@123
ntp-service reliable authentication-keyid 1
```
> *Нақты физикалық құрылғыда **"hmac-sha256"** аутентификация режимін қолдану ұсынылады!*  
> **Мысалы:** ntp-service authentication-keyid 1 authentication-mode hmac-sha256 cipher Datacom@123  

**Access Control List (ACL)**
```shell
acl 2001
 rule 5 permit source 10.1.77.0 0.0.0.255
 rule 10 deny
 quit

ntp-service access peer 2001
```

**Уақыт белдеуін орнату**
```shell
[EdgeR1] quit
<EdgeR1> clock timezone Almaty add 05:00:00
<EdgeR1> clock datetime 14:26:00 2026-03-23
<EdgeR1> display clock
```

**Нәтижені тексеру**
```shell
display ntp-service status
display ntp-service sessions
display ntp-service sessions verbose
display clock
```
 > offset - сервер мен клиент арасындағы уақыт айырмашылығы  

## Step4: NTP клиентті конфигурациялау

**Уақыт белдеуін орнату (міндетті емес, ұсынылады)**
```shell
<Huawei> clock timezone Almaty add 05:00:00
<Huawei> display clock
```

**NTP аутентификация**
```shell
ntp-service authentication enable
ntp-service authentication-keyid 1 authentication-mode md5 Datacom@123
ntp-service reliable authentication-keyid 1
```
> *Нақты физикалық құрылғыда **"hmac-sha256"** аутентификация режимін қолдану ұсынылады!*  
> **Мысалы:** ntp-service authentication-keyid 1 authentication-mode hmac-sha256 cipher Datacom@123  

**NTP сервермен байланыс орнату**
```shell
ntp-service unicast-server 10.1.77.1 authentication-keyid 1
```
> *NTP аутентификация қолданбаған жағдайда NTP сервермен байланыс орнату*  
> ntp-service unicast-server 10.1.77.1  

**Нәтижені тексеру**
```shell
display ntp-service status
display ntp-service sessions
display clock
```

```shell
display cu | include ntp-service
```
