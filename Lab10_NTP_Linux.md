# Configure NTP Server on Linux

### 🖧 Network Topology (желі топологиясы)
![Topology](images/Lab10_NetworkTopology_NTP_Linux.png)  
[Download Link for eNSP Topology File](Topology/Lab10_NetworkTopology_NTP_Linux.topo)

| Device | Role       | interface | IP Address /Prefix | default Gateway |
| ------ | ---------- | --------- | ------------------ | --------------- |
| Ubuntu | NTP Server | ens34     | 172.16.128.10 /24  | 172.16.128.254  |
|        |            | ens32     | DHCP Assigned      |                 |
| R1     | NTP Client | g0/0/0    | 172.16.128.11 /24  | 172.16.128.254  |
| S1     | NTP Client | Vlanif1   | 172.16.128.12 /24  | 172.16.128.254  |

### Scenario
1) Configure NTP Server on Linux;
2) Configure NTP Client on Huawei VRP.

## Configure NTP Server on Linux

VMware Workstation Pro ➜ Virtual Machine Settings ➜ Add Hardware Wizard ➜ ...  
![images](images/VMwareWorkstationPro_NetworkAdapter2.png)

VMware Workstation Pro ➜ Edit ➜ Virtual Network Editor ➜ Change Settings  
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
 4 packets transmitted, 0 received, 100% packet loss, time 3058ms
```
Windows+R ➜ Turn off Windows Defender Firewall  
![images](images/windows_firewall_on_to_off.png)

```shell
student@ubuntu:~$ ping -c4 172.16.128.254
 64 bytes from 172.16.128.254: icmp_seq=1 ttl=128 time=0.230 ms
```

FreeRADIUS пакеттін (package) орнату
