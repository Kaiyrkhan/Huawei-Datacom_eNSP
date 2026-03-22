# Configure NTP Server & Client on Huawei VRP

## NTP серверді конфигурациялау

**NTP серверін іске қосу**
```shell
ntp-service refclock-master 2                  // LOCAL-ды уақыт
```
немесе
```shell
undo ntp-service refclock-master
ntp-service unicast-server 80.241.0.72        // сыртқы NTP сервер
```
```shell
int g0/0/1 (LAN)
ntp-service multicast-server
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
немесе
ntp-service unicast-server 172.16.11.1 version 4
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
