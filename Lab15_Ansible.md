# Network Programmability and Automation. Ansible

### 🖧 Network Topology (желі топологиясы)
![Topology](images/Lab15_NetworkTopology_NetworkAutomation_Ansible.png)

## Scenario
1) Configure Remote Access (SSH);
2) Ansible on Ubuntu Server.

## Configure Remote Access (SSH)

**Configure Huawei VRP Router**

```shell
Configure Local User Authentication and Authorization
aaa
 local-user user1 password irreversible-cipher Huawei@123
 local-user user1 service-type terminal ssh
 local-user user1 privilege level 15
```

```shell
ssh user user1 authentication-type password
ssh user user1 service-type stelnet
```

```shell
Configure VTY Lines
user-interface vty 0 4
 authentication-mode aaa
 protocol inbound all
```

```shell
Generate RSA Key
rsa local-key-pair create

Warning: Confirm to replace them! Continue? [Y/N] Y
Input the bits in the modulus[default = 3072]: 2048

display rsa local-key-pair public
```

```shell
Enable SSH
stelnet server enable

display ssh server status
```

**Configure Ubuntu Server**

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

```shell
student@ubuntu:~$ sudo netplan try
немесе
student@ubuntu:~$ sudo netplan apply
```

```shell
student@ubuntu:~$ ip address
```

Windows+R ➜ Turn off Windows Defender Firewall  
![images](images/windows_firewall_on_to_off.png)

Ping from Ubuntu to SW1
```shell
student@ubuntu:~$ ping -c4 172.16.128.11
```

```shell
student@ubuntu:~$ sudo nano ~/.ssh/config
Host 172.16.128.11
    KexAlgorithms +diffie-hellman-group1-sha1
    HostKeyAlgorithms +ssh-rsa
    PubkeyAcceptedAlgorithms +ssh-rsa
    Ciphers +aes128-cbc
CTRL+O, ENTER, CTRL+X
```
немесе
```shell
student@ubuntu:~$ sudo nano ~/.ssh/config
Ciphers aes128-ctr,aes192-ctr,aes256-ctr,aes128-cbc,3des-cbc
KexAlgorithms +diffie-hellman-group-exchange-sha1,diffie-hellman-group1-sha1
HostKeyAlgorithms=+ssh-rsa
CTRL+O, ENTER, CTRL+X
```

```shell
student@ubuntu:~$ ssh user1@172.16.128.11
student@ubuntu:~$ telnet 172.16.128.11
```

## Ansible on Ubuntu Server

```shell
$ sudo mkdir /etc/ansible/
$ sudo mkdir /etc/ansible/group_var

$ sudo touch /etc/ansible/hosts 
$ sudo chmod 777 /etc/ansible/hosts 
```

```shell
$ sudo nano  /etc/ansible/hosts

localhost ansible_connection=local

[switch]
172.16.128.11
```

```shell
$ sudo nano /etc/ansible/group_vars/switch.yml

ansible_user: user1
ansible_password: Huawei@123
ansible_connection: network_cli
ansible_network_os: community.network.ce
```

**Example #1**
```shell
$ sudo nano /etc/ansible/sysname.yml

---
- name: Change hostname manually using CLI command
  hosts: switch
  gather_facts: no
  connection: ansible.netcommon.network_cli

  tasks:
    - name: Set new hostname
      ansible.netcommon.cli_command:
        command: |
          system-view
          sysname SW1
          quit
          save
          y
```

**Example #2**
```shell
$ sudo nano /etc/ansible/vlan_ip_address.yml

---
- name: Create VLAN and assign IP address on Huawei Switch
  hosts: switch
  gather_facts: no
  connection: ansible.netcommon.network_cli

  tasks:
    - name: Create VLAN 20
      ansible.netcommon.cli_command:
        command: |
          system-view
          vlan 20
          quit

    - name: Configure VLAN 20 interface with IP address
      ansible.netcommon.cli_command:
        command: |
          system-view
          interface Vlanif20
          ip address 192.168.20.1 255.255.255.0
          quit
          quit
          save
          y
```

```shell
```

```shell
```

```shell
```

```shell
```

```shell
```
