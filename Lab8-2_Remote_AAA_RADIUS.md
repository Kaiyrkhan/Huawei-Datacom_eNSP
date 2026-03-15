# Remote AAA configuration using RADIUS
> AAA (Authentication, Authorization, Accounting)  

### 🖧 Network Topology
![Topology](images/Lab8_NetworkTopology_Remote_AAA.png)  

## Scenario:
1) Basic Device Configuration
     - Configure the IP Address
2) Create a RADIUS Server Template
3) Configure the AAA Scheme
4) Configure the AAA Domain
5) Enable the SSH Server
6) Configure the VTY User Interface
7) Configure Local Backup Authentication
8) Verify the Configuration

VMware Workstation Pro ➜ Virtual Machine Settings ➜ Add Hardware Wizard ➜ ...  
![images](images/VMwareWorkstationPro_NetworkAdapter2.png)

VMware Workstation Pro ➜ Etit ➜ Virtual Network Editor ➜ Change Settings  
![images](images/VMwareWorkstationPro_VirtualNetworkEditor_VMnet2.png)

```shell
student@ubuntu:~$ lsb_release -a
Ubuntu 24.04.4 LTS
student@ubuntu:~$ uname -rs
Linux 6.8.0-101-generic x86_64 GNU/Linux
```
![images](images/ubuntu_lsb_release_uname.png)

```shell
student@ubuntu:~$ ip address
```
![images](images/ubuntu_ip_address_before.png)

```shell
student@ubuntu:~$ sudo nano /etc/netplan/50-cloud-init.yaml
network:
  version: 2
  renderer: networkd
  ethernets:
    ens32:
      dhcp4: true
    ens34:
      dhcp4: false
      addresses:
        - 172.16.128.10/24

CTRL+O, ENTER, CTRL+X
```
> **ЕСКЕРТУ:** *YAML файлында бос орындар (indentation) өте маңызды. Әр қатарда 2 бос орын қолдануды ұмытпаңыз! (Tab пернесін қолданбаған дұрыс)*  

![images](images/ubuntu_50-cloud-init_yaml.png)

```shell
student@ubuntu:~$ sudo netplan apply
немесе
student@ubuntu:~$ sudo netplan try
```

```shell
student@ubuntu:~$ ip address
```
![images](images/ubuntu_ip_address_after.png)

```shell
student@ubuntu:~$ networkctl status
```
![images](images/ubuntu_ip_address_networkctl_status.png)

Ping from Ubuntu to Host Machine (Loopback 1)
```shell
student@ubuntu:~$ ping -c4 172.16.128.254
```

FreeRADIUS пакеттін (package) орнату
```shell
student@ubuntu:~$ sudo apt update
student@ubuntu:~$ sudo apt install -y freeradius freeradius-utils
```

FreeRADIUS пакеттінің конфигурациялық файлдары
```shell
student@ubuntu:~$ ls -l /etc/freeradius/3.0/
```

RADIUS клиенттерді қосу
```shell
student@ubuntu:~$ sudo nano /etc/freeradius/3.0/clients.conf
client 172.16.128.11 {
    ipaddr = 172.16.128.11
    secret = Datacom@123
    shortname = R1
    require_message_authenticator = no
    nastype = other
}

CTRL+O, ENTER, CTRL+X
```
Қолданушыларды қосу
```shell
student@ubuntu:~$ sudo nano /etc/freeradius/3.0/users
user1   Cleartext-Password := "Huawei@123"
user2   Cleartext-Password := "Huawei@123"

CTRL+O, ENTER, CTRL+X
```

```shell
```

```shell
student@debian:~$ sudo radtest user1 Huawei@123 127.0.0.1 0 testing123
```

```shell
```
