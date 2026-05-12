# Network Automation and Programmability. Python

### 🖧 Network Topology
![Topology](images/Lab14_NetworkTopology_Programmability_Automation.png)  
[Download Link for eNSP Topology File](Topology/Lab14_NetworkTopology_Programmability_Automation.topo)  

## Scenario
1) Configure Remote Access (Telnet, SSH);
2) Python TELNET Library;
3) Python Netmiko Library;
4) Python NAPALM Library.

## Configure Remote Access (Telnet, SSH)

**Configure Huawei VRP Router**

```shell
# Configure Local User Authentication and Authorization
aaa
 local-user user1 password cipher Huawei@123
 local-user user1 service-type terminal ssh telnet
 local-user user1 privilege level 15
```

```shell
# Configure SSH User Settings
ssh user user1 authentication-type password
ssh user user1 service-type stelnet
```

```shell
# Enable SSH
stelnet server enable
display ssh server status
```
```shell
# Enable Telnet
display telnet server status
telnet server enable
```

```shell
# Generate RSA Key
rsa local-key-pair create

Warning: Confirm to replace them! Continue? [Y/N] Y
Input the bits in the modulus[default = 3072]: 2048

display rsa local-key-pair public
```

```shell
# Configure VTY Lines
user-interface vty 0 4
 authentication-mode aaa
 protocol inbound all
```

```shell
# Configure SSH Client Settings
ssh client first-time enable
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

Ping from Ubuntu to R1
```shell
student@ubuntu:~$ ping -c4 172.16.128.11
```

```shell
student@ubuntu:~$ sudo nano ~/.ssh/config
Ciphers aes128-ctr,aes192-ctr,aes256-ctr,aes128-cbc,3des-cbc
KexAlgorithms +diffie-hellman-group-exchange-sha1,diffie-hellman-group1-sha1
HostKeyAlgorithms=+ssh-rsa
CTRL+O, ENTER, CTRL+X
```
немесе
```shell
student@ubuntu:~$ sudo nano ~/.ssh/config
Host 172.16.128.11
    KexAlgorithms +diffie-hellman-group1-sha1
    HostKeyAlgorithms +ssh-rsa
    PubkeyAcceptedAlgorithms +ssh-rsa
    Ciphers +aes128-cbc
CTRL+O, ENTER, CTRL+X
```

```shell
# Verify SSH Connectivity
student@ubuntu:~$ ssh user1@172.16.128.11

# Verify Telnet Connectivity
student@ubuntu:~$ telnet 172.16.128.11
```

## Python TELNET Library

```shell
student@ubuntu:~$ python3 --version
```

**Example #1**

```shell
student@ubuntu:~$ sudo nano script1_telnet.py

import telnetlib
import time

host = '172.16.128.11'
user = 'user1'
password = 'Huawei@123'
UserPrompt = '>'
ConfigPrompt = ']'

tn = telnetlib.Telnet(host)

tn.read_until(b'Username:')
tn.write(user.encode('ascii') + b'\n')
tn.read_until(b'Password:')
tn.write(password.encode('ascii') + b'\n')

print('Connection to ' + '172.16.128.11' + ' is Successful')

UserMode = tn.read_until(UserPrompt.encode('ascii'))
print(UserMode.decode('ascii'))

tn.write(b'system-view \n')
ConfigMode = tn.read_until(ConfigPrompt.encode('ascii'))
print(ConfigMode.decode('ascii'))

tn.write(b'sysname R1 \n')
ConfigMode = tn.read_until(ConfigPrompt.encode('ascii'))
print(ConfigMode.decode('ascii'))

tn.write(b'display version \n')
ConfigMode = tn.read_until(ConfigPrompt.encode('ascii'))
print(ConfigMode.decode('ascii'))

tn.close()

CTRL+O, ENTER, CTRL+X
```

```shell
student@ubuntu:~$ python3 script1_telnet.py
```

**Example #2**

```shell
student@ubuntu:~$ sudo nano script2_telnet.py

import telnetlib
import time

host = '172.16.128.11'
user = 'user1'
password = 'Huawei@123'
UserPrompt = '>'
ConfigPrompt = ']'

tn = telnetlib.Telnet(host)

tn.read_until(b'Username:')
tn.write(user.encode('ascii') + b'\n')
tn.read_until(b'Password:')
tn.write(password.encode('ascii') + b'\n')

print('Connection to ' + '172.16.128.11' + ' is Successful')

UserMode = tn.read_until(UserPrompt.encode('ascii'))
print(UserMode.decode('ascii'))

tn.write(b'system-view \n')
ConfigMode = tn.read_until(ConfigPrompt.encode('ascii'))
print(ConfigMode.decode('ascii'))

tn.write(b'interface Loopback 50 \n')
ConfigMode = tn.read_until(ConfigPrompt.encode('ascii'))
print(ConfigMode.decode('ascii'))

tn.write(b'ip address 50.1.1.1 32 \n')
ConfigMode = tn.read_until(ConfigPrompt.encode('ascii'))
print(ConfigMode.decode('ascii'))

tn.write(b'display ip interface brief \n')
ConfigMode = tn.read_until(ConfigPrompt.encode('ascii'))
print(ConfigMode.decode('ascii'))

tn.close()

CTRL+O, ENTER, CTRL+X
```

```shell
student@ubuntu:~$ python3 script2_telnet.py
```

## Python NetMiko Library

> Netmiko – Multi-vendor library to simplify CLI connections to network devices  
> Paramiko – SSH2 protocol library  

```shell
student@ubuntu:~$ sudo apt update
```

```shell
student@ubuntu:~$ python3 --version
student@ubuntu:~$ sudo apt install -y python3-pip python3-venv
student@ubuntu:~$ pip3 --version
```

```shell
student@ubuntu:~$ python3 -m venv netmiko_huawei_vrp					// Create Virtual Environment (venv)
student@ubuntu:~$ source netmiko_huawei_vrp/bin/activate				// Activate Virtual Environment
```
> Deactivate Virtual Environment 
> (netmiko_huawei_vrp) student@ubuntu:~$ deactivate  

```shell
(netmiko_huawei_vrp) student@ubuntu:~$ python3 -m pip install paramiko
(netmiko_huawei_vrp) student@ubuntu:~$ python3 -m pip install netmiko
(netmiko_huawei_vrp) student@ubuntu:~$ python3 -m pip list
```

**Example #1**

```shell
(netmiko_huawei_vrp) student@ubuntu:~$ nano script1_netmiko.py

from netmiko import ConnectHandler

AR2220 = {
	'device_type': 'huawei',
	'host': '172.16.128.11',
	'username': 'user1',
	'password': 'Huawei@123'
}

net_connect = ConnectHandler(**AR2220)
output = net_connect.send_command('display version')
print(output)

CTRL+O, ENTER, CTRL+X
```

```shell
(netmiko_huawei_vrp) student@ubuntu:~$ python script1_netmiko.py
```

**Example #2**

```shell
(netmiko_huawei_vrp) student@ubuntu:~$ nano script2_netmiko.py

from netmiko import ConnectHandler

AR2220 = {
	'device_type': 'huawei',
	'host': '172.16.128.11',
	'username': 'user1',
	'password': 'Huawei@123'
}

net_connect = ConnectHandler(**AR2220)
output = net_connect.send_command('display cu int g0/0/1')
print(output)

commands = ['interface GigabitEthernet 0/0/1', 'ip address 10.1.1.101 30', 'display this', 'ospf 1 router-id 50.1.1.1', 'area 0', 'network 10.1.1.100 0.0.0.3', network 172.16.128.0 0.0.0.255']
output = net_connect.send_config_set(commands)
print(output)

output = net_connect.send_command('display cu section ospf')
print(output)

CTRL+O, ENTER, CTRL+X
```

```shell
(netmiko_huawei_vrp) student@ubuntu:~$ python script2_netmiko.py
```

## Python NAPALM Library

*NAPALM (Network Automation and Programmability Abstraction Layer with Multivendor support) is a Python library that implements a set of functions to interact with different network device Operating Systems using a unified API.*

```shell
student@ubuntu:~$ python3 -m venv napalm_huawei_vrp
student@ubuntu:~$ source napalm_huawei_vrp/bin/activate
(napalm_huawei_vrp) student@ubuntu:~$ python3 -m pip install napalm-huawei-vrp
(napalm_huawei_vrp) student@ubuntu:~$ python3 -m pip list
```

**Example #1**

```shell
(napalm_huawei_vrp) student@ubuntu:~$ nano script1_napalm.py

from napalm import get_network_driver
import pprint

driver = get_network_driver('huawei_vrp')
device = driver(hostname='172.16.128.11', username='user1', password='Huawei@123')
device.open()

# Get Facts API. Return general device information
get_facts = device.get_facts()
pprint.pprint(get_facts)

CTRL+O, ENTER, CTRL+X
```

```shell
(napalm_huawei_vrp) student@ubuntu:~$ python script1_napalm.py
```

**Example #2**

```shell
(napalm_huawei_vrp) student@ubuntu:~$ nano script2_napalm.py

from napalm import get_network_driver
import pprint

driver = get_network_driver('huawei_vrp')
device = driver(hostname='172.16.128.11', username='user1', password='Huawei@123')
device.open()

# Send Any CLI command
send_command = device.cli(['display version'])
pprint.pprint(send_command)

CTRL+O, ENTER, CTRL+X
```

```shell
(napalm_huawei_vrp) student@ubuntu:~$ python script2_napalm.py
```

**Example #3**

```shell
```

```shell
```

```shell
```
