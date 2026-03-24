# Configure NTP Server on Ubuntu

### 🖧 Network Topology (желі топологиясы)
![Topology](images/Lab10_NetworkTopology_NTP_Linux.png)  
[Download Link for eNSP Topology File](Topology/Lab10_NetworkTopology_NTP_Linux.topo)

| Device       | Role       | interface | IP Address / Prefix | Operating System |
| ------------ | ---------- | --------- | ------------------- | ---------------- |
| Ubuntu       | NTP Server | ens34     | 172.16.128.10 /24   | Linux            |
|              |            | ens32     | DHCP Assigned       |                  |
| R1           | NTP Client | g0/0/0    | 172.16.128.11 /24   | Huawei VRP       |
| S1           | NTP Client | Vlanif1   | 172.16.128.12 /24   | Huawei VRP       |
| Host Machine | Bridge     | Loopback1 | 172.16.128.254 /24  | Windows          |

### Scenario
1) Configure NTP Server on Ubuntu;
2) Configure NTP Client on Huawei VRP.

## Step1: Configure NTP Server on Ubuntu

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

**Chrony пакетін (package) орнату**

> Package атауы: **chrony**  
> Daemon/Service атауы: **chrony** немесе **chronyd**  

> **chronyd** – the actual daemon to sync and serve via the Network Time Protocol  
> **chronyc** – command-line interface for the chrony daemon  

```shell
$ sudo apt update 
$ sudo apt install -y chrony
```

```shell
$ sudo systemctl status chronyd
```

Уақыт белдеуін (Time Zone) өзгерту
```shell
$ sudo timedatectl set-timezone Asia/Almaty
$ timedatectl status
```
> Time Zones in Kazakhstan https://www.timeanddate.com/time/zone/kazakhstan  

**NTP серверді конфигурациялау**

> NTP Pool Time Servers Link: https://www.ntppool.org/zone/kz  

> артық DNS атауларды "#" comment-ге алып, төменгі қатарға Қазақстанға ең жақын NTP сервердің DNS атауын енгіземіз!  

```shell
$ sudo nano /etc/chrony/chrony.conf

#pool ntp.ubuntu.com        iburst maxsources 4
#pool 0.ubuntu.pool.ntp.org iburst maxsources 1
#pool 1.ubuntu.pool.ntp.org iburst maxsources 1
#pool 2.ubuntu.pool.ntp.org iburst maxsources 2

# Kazakhstan NTP pool
server ntp.nic.kz iburst
pool 2.kz.pool.ntp.org iburst
pool 1.kz.pool.ntp.org iburst

# Global NTP pool
pool time.google.com iburst
pool time.cloudflare.com iburst

# Listen on all interfaces
bindcmdaddress 0.0.0.0
bindcmdaddress ::

# Allow NTP client access from Local Network
allow 172.16.128.0/24

# NTP authentication
keyfile /etc/chrony/chrony.keys
trustedkey 1

# Log files location
logdir /var/log/chrony
log measurements statistics tracking

# Hardware clock synchronization
rtcsync

# Time adjustment settings (уақыт дәлдігін реттеу)
makestep 1 3
```

NTP аутентификация
```shell
$ sudo nano /etc/chrony/chrony.keys
1 md5 Datacom@123

CTRL+O, ENTER, CTRL+X
```

Firewall конфигурациялау 
```shell
$ sudo ufw status
$ sudo ufw enable
$ sudo ufw status verbose

NTP портын (123/UDP) ашу
$ sudo ufw allow from 172.16.128.0/24 to any port 123 proto udp
$ sudo ufw allow from 172.16.128.0/24 to any port 22 proto tcp

$ sudo ufw reload
$ sudo ufw status
```

Daemon-ды қайта жүктеу және ...
```shell
$ sudo systemctl restart chronyd
$ sudo systemctl status chronyd
```

```shell
$ ss -tulpn

$ sudo apt install -y net-tools
$ netstat -tulpn
```

Нәтижені тексеру
```shell
$ sudo chronyc sources -v
$ sudo chronyc tracking
$ sudo chronyc activity
```

```shell
$ sudo apt install ntpdate
$ sudo ntpdate -q 80.241.0.72
```

## Step2: Configure NTP Client on Huawei VRP (Router, Switch)

**NTP қызметін іске қосу**
```shell
ntp-service enable
```

**NTP сервермен байланыс орнату**
```shell
ntp-service unicast-server 172.16.128.10 authentication-keyid 1
```

**NTP аутентификация**
```shell
ntp-service authentication enable
ntp-service authentication-keyid 1 authentication-mode md5 Datacom@123
ntp-service reliable authentication-keyid 1
```
> *Нақты физикалық құрылғыда **"hmac-sha256"** аутентификация режимін қолдану ұсынылады!*  
> **Мысалы:** ntp-service authentication-keyid 1 authentication-mode hmac-sha256 cipher Datacom@123  

**Source interface көрсету**
```shell
[R1] ntp-service source-interface g0/0/0
[S1] ntp-service source-interface Vlanif1
```

**Нәтижені тексеру**
```shell
display ntp-service status
display ntp-service sessions
display clock
```
