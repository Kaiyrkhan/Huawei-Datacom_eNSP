# Network Automation. Python

### 🖧 Network Topology (желі топологиясы)
![Topology](images/Lab13_NetworkTopology_NetworkAutomation.png)  

## Scenario
1) Configure Remote Access (Telnet, SSH);
2) Python TELNET Library;
3) Python Netmiko Library;
4) Python NAPALM Library.

## Configure Remote Access (Telnet, SSH)

```shell
Configure Local User Authentication and Authorization
aaa
 local-user user1 password irreversible-cipher Huawei@123
 local-user user1 service-type terminal ssh
 local-user user1 privilege level 15
```

```shell
ssh user student authentication-type password
ssh user student service-type stelnet
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
display telnet server status
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

```shell
student@ubuntu:~$ ssh user1@172.16.128.11
student@ubuntu:~$ telnet 172.16.128.11
```

## Python TELNET Library

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

```shell
```

```shell
```

```shell
```
