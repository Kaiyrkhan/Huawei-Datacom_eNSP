# Configure NTP Server & Client on Huawei VRP

### 🖧 Network Topology (желі топологиясы)
![Topology](images/Lab10_NetworkTopology_NTP.png) 

## Scenario
1) Configure the IP Address;
2) Configure NTP Server;
3) Configure NTP Client.

| Device Name | Role       | interface | IP Address/Prefix |
| ----------- | ---------- | --------- | ----------------- |
| EdgeR1      | NTP Server | g0/0/1    | 10.1.77.1 /24     |
| R1          | NTP Client | g0/0/0    | 10.1.77.101 /24   |
| S1          | NTP Client | Vlanif1   | 10.1.77.102 /24   |

## Step1: Configure the IP Address

```shell
<Huawei> system-view
[Huawei] sysname EdgeR1
[EdgeR1]

int g0/0/1
 ip addr 10.1.77.1 24
 quit
display ip int brief
```

```shell
<Huawei> system-view
[Huawei] sysname R1
[R1]

int g0/0/0
 ip addr 10.1.77.101 24
 quit
display ip int brief
ping 10.1.77.1
```

```shell
<Huawei> system-view
[Huawei] sysname S1
[S1]

int Vlanif 1
 ip addr 10.1.77.102 24
 quit
display ip int brief
ping 10.1.77.1
```

## Step2: NTP серверді конфигурациялау

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

> display ntp-service sessions  
> reach – мәні 255 болуы керек!  
> offset - сервер мен клиент арасындағы уақыт айырмашылығы  

## Step3: NTP клиентті конфигурациялау

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
