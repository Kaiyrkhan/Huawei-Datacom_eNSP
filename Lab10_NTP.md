# Configure NTP Server & Client on Huawei VRP

**🖧 Желі топологиясы** 
![Topology](images/images.png) 

| Device Name | Role       | IP Address/Prefix |
| ----------- | ---------- | ----------------- |
| EdgeR1      | NTP Server | 10.1.77.1 /24     |
| S1          | NTP Client | 10.1.77.101 /24   |
| R1          | NTP Client | 10.1.77.102 /24   |


## Basic Device Configuration

Configure the IP Address
```shell
<Huawei> system-view
[Huawei] sysname EdgeR1

ip address 192.168.137.254 24
display ip int brief
```

```shell
ping 192.168.137.1
 Request time out
 Request time out
```
Windows+R ➜ Turn off Windows Defender Firewall  
![images](images/windows_firewall_off.png)

```shell
ping 192.168.137.1
 Reply from 192.168.137.1: bytes=56 Sequence=1 ttl=128 time=10 ms
 Reply from 192.168.137.1: bytes=56 Sequence=2 ttl=128 time=10 ms
```

```shell
ping 8.8.8.8
 Request time out
 Request time out
```

```shell
ip route-static 0.0.0.0 0.0.0.0 192.168.137.1
display ip routing-table
```

```shell
ping 8.8.8.8
 Reply from 8.8.8.8: bytes=56 Sequence=1 ttl=108 time=100 ms
 Reply from 8.8.8.8: bytes=56 Sequence=2 ttl=108 time=90 ms
```

```shell
```

```shell
```

## NTP серверді конфигурациялау

**NTP серверін іске қосу**
```shell
ntp-service refclock-master 2                  // LOCAL-ды уақыт

int g0/0/1 (inbound)
ntp-service multicast-server
```
немесе
```shell
undo ntp-service refclock-master
ntp-service unicast-server 80.241.0.72        // сыртқы NTP сервер
```

**NTP аутентификация**
```shell
ntp-service authentication enable
ntp-service authentication-keyid 1 authentication-mode md5 Datacom@123
ntp-service reliable authentication-keyid 1
```

**Access Control List (ACL)**
```shell
acl 2000
 rule 5 permit source 172.16.11.0 0.0.0.255
 rule 10 deny
 quit

ntp-service access peer 2000
```

**Уақыт белдеуін баптау**
```shell
<Huawei> clock timezone Almaty add 05:00:00
<Huawei> clock datetime 13:50:00 2026-03-22
<Huawei> display clock
```

## NTP клиентті конфигурациялау

```shell
ntp-service unicast-server 172.16.11.1

int g0/0/0
ntp-service multicast-client
```

**NTP аутентификация**
```shell
ntp-service authentication enable
ntp-service authentication-keyid 1 authentication-mode md5 Datacom@123
ntp-service reliable authentication-keyid 1
ntp-service unicast-server 172.16.11.1 authentication-keyid 1
```

**Уақыт белдеуін баптау**
```shell
<Huawei> clock timezone Almaty add 05:00:00
<Huawei> display clock
```

## Нәтижені тексеру

```shell
display ntp-service status
display ntp-service sessions
display ntp-service sessions verbose
display clock
```

> offset - сервер мен клиент арасындағы уақыт айырмашылығы  

```shell
```

```shell
```

```shell
```
