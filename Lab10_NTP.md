# Configure NTP Server & Client on Huawei VRP

**🖧 Желі топологиясы** 
![Topology](images/images.png) 

| Device Name | Role       | IP Address/Prefix |
| ----------- | ---------- | ----------------- |
| EdgeR1      | NTP Server | 172.16.11.1 /24   |
| R1          | NTP Client | 172.16.11.10 /24  |

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
```shell

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
