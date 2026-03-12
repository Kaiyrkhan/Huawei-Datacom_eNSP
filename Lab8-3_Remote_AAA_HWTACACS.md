# Remote AAA configuration using HWTACACS
> AAA (Authentication, Authorization, Accounting)  

### 🖧 Network Topology
![Topology](images/Remote_AAA.png)  

## Scenario:
1) Basic Device configuration
     - IP Address configuration
2) RADIUS Server template configuration
3) AAA Scheme configuration
4) AAA Domain configuration
5) SSH Enable configuration
6) VTY configuration
7) Backup Local Admin configuration

```shell
student@ubuntu:~$ sudo nano /etc/netplan/50-cloud-init.yaml
network:
  version: 2
  ethernets:
    ens32:
      dhcp4: true
    ens34:
      dhcp4: false
      addresses: [172.16.128.10/24]
CTRL+O, ENTER, CTRL+X

student@ubuntu:~$ sudo netplan apply

student@ubuntu:~$ ip address
ens32: 192.168.0.104/24
ens34: 172.16.128.10/24
```

```shell
```

```shell
student@ubuntu:~$ sudo radtest user1 Huawei@123 127.0.0.1 0 testing123
```

```shell
```
